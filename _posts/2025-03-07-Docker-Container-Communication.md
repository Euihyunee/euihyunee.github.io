---
title: "💬 도커 컨테이너끼리 통신하는 방법"
date: 2025-03-07 16:05 +0900
categories: [CS]
tags: [Docker, Docker Compose, Docker Container, Docker Network]
---

도커 컨테이너(프론트엔드-백엔드-MySQL)을 각각 띄워서 서로 통신해보자

## 1️⃣ MySQL 컨테이너 실행 

```bash
# DB/BackEnd 컨테이너 네트워크 
docker network create spring-mysql-net
# F/B End 컨테이너 네트워크
docker network create front-spring-net 

# MySQL 컨테이너 실행
docker run -d \
    --name mysql-container \
    --network spring-mysql-net \
    -e MYSQL_ROOT_PASSWORD=root \
    -e MYSQL_DATABASE=simple_login \
    -v mysql_data:/var/lib/mysql \
    -p 3306:3306 \
    mysql:8.0
```

## 2️⃣ Spring Boot 컨테이너 실행

✅ Spring Boot의 Dockerfile 작성

```text
# Gradle 버전 및 자바 버전 설정
FROM gradle:8.12.1-jdk17 AS builder

# 작업 디렉토리 설정
WORKDIR /app

# .jar 파일 복사하기 -> 실제 프로젝트에 .jar 파일이 있어야 함
COPY build/libs/docker-0.0.1-SNAPSHOT.jar spring.jar

# .jar 파일 실행
CMD ["java", "-jar", "spring.jar"]
```

✅ Dockerfile 이미지로 빌드 및 컨테이너 실행

```bash
# Image 빌드
docker build -f Dockerfile -t spring-image .

# Docker Container 실행
docker run -d \
  --name spring-container \
  # MySQL과 네트워크 연결
  --network spring-mysql-net \
  # 이후 프론트엔드 서버와 연동할 네트워크
  --network front-spring-net \
  -p 8080:8080 \
  spring-image
```

<details>
<summary>UserController</summary>
<div markdown="1">

```java
@RestController
@RequestMapping("/api")
@CrossOrigin(origins = "http://localhost:3000")
public class UserController {

    @Autowired
    private UserRepository userRepository;

    @PostMapping("/signup")
    public ResponseEntity<?> registerUser(@RequestBody User user) {
        if (userRepository.existsByUsername(user.getUsername())) {
            return ResponseEntity.badRequest().body("Username already exists");
        }
        userRepository.save(user);
        return ResponseEntity.ok("User registered successfully");
    }

    @PostMapping("/login")
    public ResponseEntity<?> authenticateUser(@RequestBody LoginRequest loginRequest) {
        Optional<User> userOptional = userRepository.findByUsername(loginRequest.getUsername());
        if (userOptional.isEmpty()) {
            return ResponseEntity.status(HttpStatus.NOT_FOUND).body("아이디를 찾을 수 없음");
        }

        User user = userOptional.get();

        // 비밀번호 일치 여부 확인
        if (!user.getPassword().equals(loginRequest.getPassword())) {
            return ResponseEntity.status(HttpStatus.UNAUTHORIZED).body("비밀번호 불일치");
        }
        return ResponseEntity.ok("로그인 성공");
    }
}
```


</div>
</details>

<details>
<summary>WebConfig</summary>
<div markdown="1">

```java
@Configuration
public class WebConfig {

    @Bean
    public WebMvcConfigurer corsConfigurer() {
        return new WebMvcConfigurer() {
            @Override
            public void addCorsMappings(CorsRegistry registry) {
                registry.addMapping("/api/**")
                        .allowedOrigins("http://front-container:3000", "http://localhost:3000")
                        .allowedMethods("GET", "POST", "PUT", "DELETE", "OPTIONS")
                        .allowedHeaders("*")
                        .allowCredentials(true);
            }
        };
    }
}
```

</div>
</details>

## 3️⃣ Front-End 컨테이너 실행

✅ Front 서버 Dockerfile 작성

여기서는 HTML 파일을 NPM으로 실행했습니다.

```text
FROM node:18-alpine

WORKDIR /app

COPY package.json package.json

RUN npm install

COPY . .

EXPOSE 3000

CMD ["npm", "start"]
```

✅ Front의 Dockerfile 이미지 빌드 및 컨테이너 실행

```bash
# Front Dockerfile 이미지 빌드
docker build -t front-image .

# 컨테이너 실행
docker run -d \
    --name front-container \
    --network front-spring-net \
    -p 3000:3000 \
    front-image
```