---
title: "💬 Docker Compose란?"
date: 2025-03-05 15:05 +0900
categories: [CS]
tags: [Docker, Docker Compose, Container Orchestration]
---

> 기존에 Dockerfile은 한계점이 있습니다. Dockerfile만으로는 웹 서버, DB, 캐시 등 연동된 서비스의 실행 명령을 개별적으로 작성하는 등 수동 관리로 인한 비효율성이 발생했습니다. 여기에 더해 개발/테스트/운영 환경마다 컨테이너 설정이 달라 환경 불일치 문제가 발생했으며, CI/CD 환경에서 복잡한 의존성 관리를 Dockerfile만으로는 힘들었습니다. 이런 문제와 더불어 다중 컨테이너 애플리케이션 관리의 복잡성을 해결하기 위해 도입된 도구가 `Docker Compose`입니다. 이 글에서 Docker Compose에 대해 자세히 알아보겠습니다.

## Docker Compose란?

Dokcer Compse는 다중 컨테이너 도커 애플리케이션을 정의하고 실행하기 위한 도구로, 복잡한 시스템 구성을 단일 파일로 간소화하는 역활을 합니다. 여러 컨테이너의 설정, 네트워크, 볼륨, 의존성을 YAML 형식의 파일(docker-compose.yml)에 정의하여 한 번에 관리할 수 있도록 설계되었습니다.

하나의 애플리케이션에 여러 개의 컨테이너가 있을 때 도커 컴포즈를 사용하지 않는다면, 이를 테스트하기 위해 각 컨테이너를 하나씩 생성해야 합니다. 예시로 웹 애플리케이션을 테스트 하기 위해 웹 서버, 데이터베이스 컨테이너 두 개의 컨테이너를 각각 생성해야 합니다.

**✅ Docker Compose의 주요 기능과 특징**

- 다중 컨테이너 관리: 웹 서버 데이터베이스, 캐시 시스템 등 상호 연결된 서비스를 단일 명령어(`docker-compose up`)로 실행 및 중지
- 환경 일관성 보장: 개발, 테스트, 프로덕션 환경에서 동일한 설정을 재현 가능
- 의존성 자동 처리: 컨테이너 간 시작 순서와 연결을 자동으로 관리(ex. `depends_on` 옵션)

## Docker Compose 핵심 구성 요소

```yml
version: "3.9"
services:
	web:
		build: .
		ports:
			- "5000:5000"
		depends_on:
			- redis
	redis:
		image: "redis:alpine"
```

- `services`: 실행할 컨테이너 정의
- `networks/volumes`: 컨테이너 간 공유 리소스 설정
- `environment`: 환경 변수 주입(ex. 데이터베이스 비밀번호)

Docker Compose의 구성은 위와 같으며 작업 흐름은 다음과 같습니다.

1. 서비스 정의: 각 컨테이너의 Dockerfile 작성
2. YAML 구성: 컨테이너 간 관계를 docker-compose.yml에 정의
3. 실행 및 관리: `docker-compose up`으로 전체 시스템 시작, `docker-compose logs`로 로그 확인
4. 중지 및 정리: 작업을 완료한 후 `docker-compose down`으로 Docker Compose 파일에 정의된 서비스들을 중지하고 관련 컨테이너들을 삭제

> 이를 통해 개발자는 로컬 환경에서 복잡한 마이크로 서비스 아키텍처도 쉽게 구성하고 테스트 할 수 있으며, 팀 협업 시 환경 차이로 인한 문제를 최소화할 수 있습니다.
{: .prompt-info } 

## Dokcer와의 차이점

Dokcer와 Docker Compose의 가장 큰 차이는 관련 설정을 한 번에 실행할 수 있어 간편하다는 것이다. 예시로 Spring Boot 애플리케이션에 MySQL 데이터베이스를 연동하는 경우로 설명하겠습니다.

### Docker 단독 실행하는 경우

**✅ MySQL 도커 컨테이너 생성**

```bash
# MySQL 이미지 다운로드
docker pull mysql:8.0

# 네트워크 생성(컨테이너 간 통신용)
docker network create spring-mysql-network

# 컨테이너 실행
docker run -d --name mysql-container \
	--network spring-mysql-network \
	-e MYSQL_ROOT_PASSWORD=root \
	-e MYSQL_DATABASE=appdb \
	-e MYSQL_USER=appuser \
	-e MYSQL_PASSWORD=apppass \
	-p 3306:3306 \
	-v mysql_data:/var/lib/mysql \
	mysql:8.0
```

- `--network` : 컨테이너 간 통신을 위한 네트워크 지정
- `-v` : 데이터 영속성을 위한 볼륨 마운트

**✅ Spring Boot 도커화**

먼저, Dockerfile을 만듭니다.

```text
FROM openjdk:17
ARG JAR_FILE=build/libs/*.jar
COPY ${JAR_FILE} app.jar
ENTRYPOINT ["java", "-jar", "/app.jar"]
```

✅ 컨테이너로 빌드하고 실행합니다.

```bash
# 이미지 빌드
docker build -t spring-app .

# 컨테이너 실행
docker run -d --name spring-container \
	--network spring-mysql-network \
	-p 8080:8080 \
	spring-app
```

- `--network` : MySQL과 동일한 네트워크 사용

### Docker Compose로 실행

**✅ docker-compose.yml 파일을 만들어 줍니다.**

```yml
version: '3.8'

services:
  db:
    image: mysql:8.0
    container_name: mysql-compose
    environment:
      MYSQL_DATABASE: appdb
      MYSQL_USER: appuser
      MYSQL_PASSWORD: apppass
      MYSQL_ROOT_PASSWORD: rootpass
    volumes:
      - db_data:/var/lib/mysql
    ports:
      - "3306:3306"
    healthcheck:  # DB 준비 상태 확인
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]
      interval: 5s
      timeout: 10s
      retries: 5

  app:
    build: .
    container_name: spring-compose
    depends_on:
      db:
        condition: service_healthy
    ports:
      - "8080:8080"
    environment:
      SPRING_DATASOURCE_URL: jdbc:mysql://db:3306/appdb
      SPRING_DATASOURCE_USERNAME: appuser
      SPRING_DATASOURCE_PASSWORD: apppass
    restart: on-failure

volumes:
  db_data:
```

**✅ Spring Boot 도커화**

먼저, Dockerfile을 만듭니다.

```text
FROM openjdk:17
ARG JAR_FILE=build/libs/*.jar
COPY ${JAR_FILE} app.jar
ENTRYPOINT ["java", "-jar", "/app.jar"]
```

**✅ Docker Compose 실행**

```bash
# 애플리케이션 빌드 (Spring Boot jar 생성)
./gradlew build

# 도커 컴포즈 실행
docker compose up --build

# 백그라운드 실행 시
docker compose up -d

# 로그 확인
docker compose logs -f app
```

이렇게 Docker Compose를 사용하면 `--nettwork` or `--lin` 옵션을 이용해서 MySQL과 연결할 필요가 없어집니다. Docker Compose가 자동으로 네트워크 인프라를 관리하기 때문에 개발자는 인프라 관리보다 애플리케이션 로직에 집중할 수 있습니다.

|구분|Docker|Docker Compose|
|:--|:--|:--|
|실행 단위|단일 컨테이너|다중 컨테이너 애플리케이션|
|컨테이너 시작|`docer run` 2번 실행|`docker compose up` 1회 실행|
|네트워크 구성|수동 설정 필요|자동 브리지 네트워크 생성|
|로그 확인|각 컨테이너별 로그 확인|통합 로그 출력|
|서비스 의존성 관리|CLI로 수동 처리|YAML 파일에 선언적 정리|
|서비스 중지|각 컨테이너별 중지|`docker compose down`|
|환경 변수 관리|명령줄에서 전달|YAML 파일에 중앙 집중식 관리|
|실행 명령어|`docker run` 다중 실행|`docker compose up` 단일 명령|

## 컨테이너 오케스트레이션의 Docker Compose

컨테이너 오케스트레이션(Container Orchestration)이란 여러 컨테이너들의 생명주기를 관리하는 도구입니다. 컨테이너들의 배포, 관리, 확장, 네트워킹을 자동화하는 프로세스로 Docker Compose는 단일 호스트 환경에서 기본적인 컨테이너 오케스트레이션 기능을 제공하는 도구입니다. 즉, 개발 환경과 소규모 애플리케이션에 최적화되어 있습니다.

물론, 컨테이너 오케스트레이션의 Kubernetes와 같이 클러스터 관리 기능, 셀프힐링 기능, 자동 확장/로드밸런싱 등의 기능 부재가 존재합니다. 그러나 Docker Compose는 단순성과 개발자 경험에 초점을 맞춘 가벼운 도구입니다. 대규모 분산 시스템에서는 사용하기 어렵지만 사용할 수 있는 시스템에서는 복잡한 인프라 없이 빠르게 컨테이너 환경을 구성할 수 있습니다.

## 마치며

Docker Compose는 다중 컨테이너 애플리케이션 관리의 복잡성을 획기적으로 단순화하는 도구입니다. 기존 Dockerfile만으로는 해결하기 어려웠던 환경 불일치, 수동 의존성 관리, 다중 서비스 운영 문제를 YAML 기반의 선언적 설정 파일 하나로 통합해 해결합니다.

주요 강점은 개발 생산성 향상에 있습니다. docker-compose up 한 번으로 웹 서버, 데이터베이스, 캐시 시스템을 동시에 구동할 수 있어 로컬 개발 환경 구성 시간을 80% 이상 절감할 수 있습니다. 특히 컨테이너 간 네트워크 연결과 볼륨 마운트를 자동화함으로써 인프라 관리 부담 없이 애플리케이션 로직 개발에 집중할 수 있는 환경을 제공합니다.

|환경 구성 방식|명령어 실행 횟수|네트워크 설정|의존성 관리|
|:--|:--|:--|:--|
|Docker 단독 사용|5회 이상|수동 구성 필요|직접 제어|
|Docker Compose|1회|자동 생성|YAML선언|

개발 팀 협업 측면에서는 환경 표준화가 가장 큰 이점입니다. docker-compose.yml 파일을 버전 관리 시스템에 포함시키면 모든 팀원이 동일한 DB 버전, 포트 설정, 환경 변수를 사용할 수 있어 "내 로컬에서는 되는데..." 문제를 근본적으로 해결합니다. CI/CD 파이프라인과의 통합도 간편해 테스트 환경 구성이 자동화됩니다.

단, Docker Compose는 단일 호스트 환경에 최적화되어 있어 대규모 분산 시스템에는 Kubernetes 등의 오케스트레이션 도구와 조합해야 합니다. 2023년 Docker 설문조사에 따르면 실제로 68%의 개발자가 로컬 개발용으로 Docker Compose를, 프로덕션에는 Kubernetes를 사용하는 하이브리드 전략을 채택하고 있습니다.

이처럼 Docker Compose는 현대적인 애플리케이션 개발에 필수적인 도구로, 복잡성을 관리하는 동시에 개발자 경험을 극대화하는 최적의 솔루션입니다. 컨테이너 기반 개발을 시작한다면 Docker Compose를 활용해 인프라 관리 시간을 애플리케이션 개발에 집중투자하시길 추천합니다.