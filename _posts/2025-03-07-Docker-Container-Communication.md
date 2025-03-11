---
title: "💬 도커 컨테이너끼리 통신하는 방법"
date: 2025-03-07 16:05 +0900
categories: [CS]
tags: [Docker, Docker Compose, Docker Container, Docker Network]
---

> 도커를 실행하면 호스트 시스템 내에서 가상 네트워크를 생성할 수 있습니다. 이 네트워크는 내부망에서만 사용할 수 있는 IP이므로 외부와 통신하기 위해 별도의 연결이 필요합니다. 또한 컨테이너에 내부에 있는 특정 네트워크를 연결하여 IP를 할당받을 수 있습니다. 같은 네트워크에 있는 컨테이너들은 서로 통신할 수 있게 됩니다. 이 글에서는 도커 네트워크를 이용하여 컨테이너가 통신하는 방법을 알아보겠습니다.


## 도커 네트워크란?

`도커 네트워크(Docker Network)`란 격리된 컨테이너 간의 통신을 가능하게 하는 가상의 네트워크 인프라입니다. 이를 통해 여러 컨테이너가 서로 안전하고 효율적으로 통신할 수 있습니다. 도커 네트워크는 다양한 드라이버를 지원하며, 주로 `bridge, host, none` 네트워크가 기본적으로 생성됩니다.

![docker_network.png](https://github.com/Euihyunee/euihyunee.github.io/blob/main/_posts/img/docker_network.png?raw=true)

- `veth`: 각 컨테이너와 브리지를 연결하는 가상의 이더넷 케이블입니다. 
- `eth0`: 컨테이너 내부에서 네트워크 통신을 위한 인터페이스입니다. 
- `Host eth0`: 호스트의 가상 네트워크 인터페이스로, 외부 네트워크와의 통신을 담당합니다.

### 도커 네트워크 드라이버

1. 브리지 네트워크(Bridge Network)
    - 도커를 설치하면 기본적으로 `docker0`라는 브리지 네트워크가 자동으로 생성됩니다. 컨테이너를 생성할 때 네트워크를 지정하지 않으면 기본적으로 이 `docker0` 브리지에 연결됩니다. 위 그림에서는 각 컨테이너마다 브리지를 지정했습니다.
    - 브리지 네트워크로 연결되면 컨테이너 간 통신을 가능하게 하며, 격리된 환경에서 실행할 수 있습니다. 사용자 정의 브리지(위 그림처럼)를 생성하면 컨테이너 이름으로 DNS 해석이 가능하여 더 편리한 통신을 제공할 수 있습니다.
2. 호스트 네트워크(Host Network)
    - 호스트 네트워크는 컨테이너를 생성할 때 `--network host` 옵션을 명시적으로 지정해야 사용됩니다. 기본적으로 연결되지 않습니다.
    - 이는 컨테이너가 호스트의 네트워크 스택을 직접 사용할 때 사용됩니다. 호스트와 동일한 IP 주소를 공유하지만 네트워크 격리가 없습니다.
3. None 네트워크
    - None 네트워크는 컨테이너를 완전히 격리하여 외부 네트워크와의 연결을 차단합니다. 이 모드에서는 루프백 인터페이스(`IO`)만 활성화됩니다.
    - 보안이 중요한 곳에서 외부 공격을 방지하거나, 네트워크(디버깅 or 없는) 환경에서 테스트할 때 사용하거나 외부 의존성이 없는 독립적인 컨테이너 실행 시 활용됩니다.

### 도커 네트워크 드라이버의 종류

|드라이버|설명|사용 사례|
|:--|:--|:--|
|bridge|기본 네트워크 드라이버로, 컨테이너 간 통신을 가능하게 하며 외부와도 연결 가능.|단일 호스트 내 컨테이너 간 통신|
|host|컨테이너가 호스트의 네트워크 스택을 직접 사용.|성능 최적화가 필요한 경우|
|none|컨테이너를 완전히 격리하여 네트워크 연결을 비활성화.|보안 테스트 또는 독립적인 실행 환경|
|overlay|여러 호스트의 컨테이너를 연결하여 Swarm 서비스 지원.|멀티 호스트 통신 및 클러스터링.|
|macvlan|컨테이너에 MAC 주소를 할당하여 물리적 장치처럼 작동.|레거시 애플리케이션 또는 VM 환경 대체.|
|ipvlan|IPv4/IPv6 주소를 완전히 제어하며 VLAN 태깅 지원.|VLAN 및 레이어 2/3 통합이 필요한 환경.|

## 도커 개별 컨테이너로 실행

Spring Boot 컨테이너, MySQL 데이터베이스 컨테이너, Front-End 컨테이너를 생성하여 개별적으로 실행해보겠습니다.

### 1️⃣ MySQL 컨테이너 실행 

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

### 2️⃣ Spring Boot 컨테이너 실행

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

### 3️⃣ Front-End 컨테이너 실행


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


이렇게 실행하고 나면 docker의 dashboard에 이렇게 나옵니다. 만약에 옆에 초록불이 안 나온다면 로그를 확인하셔서 오류를 찾아보세요. 저는 다음과 같은 에러가 나왔습니다.

1. Spring Boot: SQL Server Error MySQL 서버를 못 찾아 발생하는 이슈
2. Front-End: CORS 문제. Container 자체는 실행 중이지만 Spring Boot Container에 API를 요청했을 때 CORS로 인해 요청이 거부됩니다.
3. Front-End: npm run failed. `server.js` 파일이 잘못되어 실행이 안 되는 경우입니다.

![docker_board.png](https://github.com/Euihyunee/euihyunee.github.io/blob/main/_posts/img/docker_board.png?raw=true)

실행에 성공하면 다음과 같이 동작합니다.

![docker_actions-ezgif.com-speed.gif](https://github.com/Euihyunee/euihyunee.github.io/blob/main/_posts/img/docker_actions-ezgif.com-speed.gif?raw=true)


## Compose로 한 번에 실행하기

먼저 프로젝트 구조 입니다.

```text
Docker-Test/
├── docker_front/
│   ├── index.html
│   ├── styles.css
│   └── script.js
├── docker_spring/
│   ├── src/
│   │   ├── main/
│   │   │   ├── java/
│   │   │   └── resources/
│   │   └── test/
│   │       ├── java/
│   │       └── resources/
│   ├── pom.xml (또는 build.gradle)
│   └── Dockerfile
├── README.md
└── docker-compose.yml (전체 프로젝트용)
```

### docker-compose.yml 파일 작성하기

여기서는 기존에 작성한 도커 파일을 이용하여 docker-compose.yml 파일을 작성해보겠습니다.

```
version: '3'
services:
  front-end-app:
    build: ./docker_front
    ports:
      - "3000:3000"
    depends_on:
      - spring-boot-app
    restart: always

  spring-boot-app:
    build: ./docker_spring
    ports:
      - "8080:8080"
    depends_on:
      - mysql
    restart: always

  mysql:
    container_name: mysql-container
    image: mysql:8.0
    environment:
      - MYSQL_ROOT_PASSWORD=root
      - MYSQL_DATABASE=simple_login
```

1. `build`: 도커 파일이 존재하는 경로를 작성
2. `ports`: 도커 파일에 포트가 명시되어 있어 docker-compose.yml 파일에는 작성 안 해도 됩니다. 다만 호스트 머신과 컨테이너 간 포트 매핑, 명시적이고 유연한 설정, 다양한 환경에 맞게 사용하기 위해 사용합니다.
3. `depends_on`: 적혀 있는 서비스가 실행된 후 시작됩니다. 예시로 MySQL이 실행되기 전에 spring이 실행된다면 SQLError가 발생할 것입니다.
4. `restart:`: 컨테이너가 중지되었을 때 재시작 정책입니다.
    - no: 기본 정책으로, 컨테이너가 중지되거나 충돌해도 재시작하지 않습니다.
    - always: 컨테이너가 중지되면 항상 재시작합니다. Docker 데몬이 재시작되거나 호스트 머신이 재부팅되더라도 재시작됩니다.
    - on-failure: 컨테이너가 비정상적으로 종료(비정상 종료 코드)되면 재시작합니다. 최대 재시작 시도를 지정할 수 있습니다.
    - unless-stopped: 컨테이너가 수동으로 중지되지 않는 한 항상 재시작합니다. Docker 데몬이 재시작되더라도 재시작되지 않습니다.
5. `environment`: MySQL 데이터베이스의 환경 변수를 설정합니다.

### docker-compose.yml 파일 실행

docker-compose.yml 파일이 있는 곳(프로젝트 최상위)으로 이동하여 다음 명령어를 입력합니다.

```bash
docker-compose up -d 
```

이렇게 하면 제대로 실행됩니다.

```bash 
Error response from daemon: Conflict. 
The container name "/mysql-container" is already in use by container "71e9f0cade642a38c761537070d2e53505ced40622ead282dee3f8c47cc4733c". 
You have to remove (or rename) that container to be able to reuse that name.
```

> 위와 같은 에러 발생 시 개별 도커 파일 실행했을 때 나온 MySQL 컨테이너가 존재해서 그렇습니다. docker-compose.yml 파일에서 name 부분을 변경하거나 도커에서 기존의 mysql-container를 삭제하고 실행하면 정상적으로 작동합니다.
{: .prompt-warning }

## 마치며

여기까지 도커 파일과 도커 컴포즈를 이용해서 컨테이너 간 통신에 대해서 알아보았습니다. 호스트와 연결되는 포트부터 내부에서 어떻게 통신하는 지 알 수 있었습니다. 

학습하면서 필요한 부분이 많이 있다는 것을 느꼈습니다. CORS 설정을 Front-end의 포트에 따라 달리 해야 하는 점, 데이터베이스는 보통 컨테이너를 만들어 놓고 docker-compose로 연결한다는 점, 다양한 환경에서 일관성을 유지하기 위해 어떻게 해야 하는 지 등에 대해서 좀 더 학습이 필요하다는 것을 알게되었습니다. 이와 관련된 부분은 다음에 알아보겠습니다.