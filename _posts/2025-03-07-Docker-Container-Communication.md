---
title: "💬 도커 컨테이너끼리 통신하는 방법(작성중)"
date: 2025-03-07 16:05 +0900
categories: [CS]
tags: [Docker, Docker Compose, Docker Container, Docker Network]
---

> 도커 컨테이너(프론트엔드-백엔드-MySQL)을 각각 띄워서 서로 통신해보자

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
<summary>User & LoginRequest</summary>
<div markdown="1">

```java
@Entity
@Getter @Setter @NoArgsConstructor
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(unique = true)
    private String username;

    private String password;
}

@Getter @Setter
public class LoginRequest {

    private String username;
    private String password;
}
```

</div>
</details>

<details>
<summary>UserRepository</summary>
<div markdown="1">

```java
public interface UserRepository extends JpaRepository<User, Long> {
    Optional<User> findByUsername(String username);
    boolean existsByUsername(String username);
}
```

</div>
</details>

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

<details>
<summary>SecurityConfig</summary>
<div markdown="1">

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
                .csrf(AbstractHttpConfigurer::disable)  // CSRF 보호 비활성화
                .cors(withDefaults())  // CORS 기본 설정 활성화
                .authorizeHttpRequests(authorize -> authorize
                        .requestMatchers("/api/signup", "/api/login").permitAll()
                        .anyRequest().authenticated()
                )
                .formLogin(AbstractHttpConfigurer::disable)  // 폼 로그인 비활성화
                .httpBasic(AbstractHttpConfigurer::disable);  // 기본 HTTP 인증 비활성화

        return http.build();
    }
}
```

</div>
</details>

## 3️⃣ Front-End 컨테이너 실행

전체 코드 

<details>
<summary>login.html</summary>
<div markdown="1">

```html
<!DOCTYPE html>
<html>
<head>
    <title>로그인</title>
    <style>
        .container { max-width: 400px; margin: 50px auto; }
        input { margin: 10px 0; padding: 8px; width: 100%; }
        button { padding: 10px 20px; }
    </style>
</head>
<body>
    <div class="container">
        <h2>로그인</h2>
        <input type="text" id="username" placeholder="Username">
        <input type="password" id="password" placeholder="Password">
        <button onclick="login()">로그인</button>
        <p>계정이 없으신가요? <a href="signup.html">회원가입</a></p>
    </div>
    <script>
        async function login() {
            const user = {
                username: document.getElementById('username').value,
                password: document.getElementById('password').value
            };
            
            const response = await fetch('http://localhost:8080/api/login', {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify(user)
            });
            
            if (response.status === 404) {
                alert('존재하지 않는 아이디입니다');
            } else if (response.status === 401) {
                alert('비밀번호가 틀렸습니다');
            } else if (response.ok) {
                window.location.href = `success.html?username=${user.username}`;
            }
        }
    </script>
</body>
</html>

```

</div>
</details>

<details>
<summary>signup.html</summary>
<div markdown="1">

```html
<!-- signup.html 수정 버전 -->
<!DOCTYPE html>
<html>
<head>
    <title>회원가입</title>
    <style>
        .container { max-width: 400px; margin: 50px auto; }
        input { margin: 10px 0; padding: 8px; width: 100%; }
        button { padding: 10px 20px; }
        .login-link { margin-top: 20px; display: block; }
    </style>
</head>
<body>
    <div class="container">
        <h2>회원가입</h2>
        <input type="text" id="username" placeholder="Username">
        <input type="password" id="password" placeholder="Password">
        <button onclick="signup()">가입하기</button>
        <a href="login.html" class="login-link">이미 계정이 있으신가요? 로그인</a>
    </div>
    <script>
        async function signup() {
            const username = document.getElementById('username').value;
            const password = document.getElementById('password').value;

            if (!username || !password) {
                alert('모든 필드를 입력해주세요');
                return;
            }

            try {
                const response = await fetch('http://localhost:8080/api/signup', {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({ username, password })
                });

                const result = await response.text();
                
                if (response.ok) {
                    alert('가입 성공! 로그인 페이지로 이동합니다');
                    setTimeout(() => {
                        window.location.href = 'login.html';
                    }, 300);
                } else {
                    alert(`가입 실패: ${result}`);
                }
            } catch (error) {
                alert('서버 연결에 실패했습니다');
            }
        }
    </script>
</body>
</html>

```

</div>
</details>

<details>
<summary>success.html</summary>
<div markdown="1">

```html
<!DOCTYPE html>
<html>
<head>
    <title>로그인 성공</title>
    <style>
        .container { max-width: 600px; margin: 100px auto; text-align: center; }
    </style>
</head>
<body>
    <div class="container">
        <h1>🎉 로그인 성공!</h1>
        <p id="welcomeMessage"></p>
        <a href="login.html">로그아웃</a>
    </div>
    <script>
        const urlParams = new URLSearchParams(window.location.search);
        const username = urlParams.get('username');
        document.getElementById('welcomeMessage').textContent = 
            `${username}님, 환영합니다!`;
    </script>
</body>
</html>

```

</div>
</details>

<details>
<summary>package.json</summary>
<div markdown="1">

```json
{
    "name": "html-server",
    "version": "1.0.0",
    "description": "HTML server for Docker",
    "main": "server.js",
    "scripts": {
      "start": "node server.js"
    },
    "dependencies": {
      "express": "^4.18.2"
    }
}
```

</div>
</details>


<details>
<summary>server.js</summary>
<div markdown="1">

```js
const express = require('express');
const path = require('path');
const app = express();

app.use(express.static(path.join(__dirname, 'html')));

app.get('/', (req, res) => {
    res.sendFile(path.join(__dirname, 'html', 'login.html'));
});

app.listen(3000, () => {
    console.log('Server running on port 3000');
});

```

</div>
</details>

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


이렇게 실행하고 나면 docker의 dashboard에 이렇게 나옵니다. 만약에 옆에 초록불이 안 나온다면 로그를 확인하셔서 오류를 찾아보세요. 저는 다음과 같은 에러가 나왔습니다.

1. Spring Boot: SQL Server Error MySQL 서버를 못 찾아 발생하는 이슈
2. Front-End: CORS 문제. Container 자체는 실행 중이지만 Spring Boot Container에 API를 요청했을 때 CORS로 인해 요청이 거부됩니다.
3. Front-End: npm run failed. `server.js` 파일이 잘못되어 실행이 안 되는 경우입니다.

![docker_board.png](https://github.com/Euihyunee/euihyunee.github.io/blob/main/_posts/img/docker_board.png?raw=true)

실행에 성공하면 다음과 같이 동작합니다.

![docker_actions.gif](https://github.com/Euihyunee/euihyunee.github.io/blob/main/_posts/img/docker_actions.gif?raw=true)



## Compose로 한 번에 하기

