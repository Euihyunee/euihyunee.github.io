---
title: "💬 Docker File과 빌드 방법"
date: 2025-03-05 13:55 +0900
categories: [CS]
tags: [Docker, Docker File, Docker Build]
---

> 이 글에서 도커 파일을 작성하는 방법과 도커 파일을 빌드하는 방법에 대해서 알아보겠습니다. [도커 개념](https://euihyunee.github.io/posts/Docker-VM/)을 잘 모르신다면 읽고 오시는 것을 추천드립니다.

## Docker File 작성

도커 파일은 컨테이너 실행 환경을 정의하는 텍스트 파일로 FROM, RUN, COPY 등의 명령어로 구성됩니다. 이미지 구성 레이어 구조, 명령어 사용법, 빌드 프로세스 최적화로 크게 분류됩니다. 

## Docker File 기본 구성 요소

|명령어|용도|예시|
|:--|:--|:--|
|FROM|베이스 이미지 지정|`FROM ubuntu:22.04`|
|RUN|빌드 중 패키지 설치/설정 실행|`RUN apt-get install -y curl`|
|COPY|로컬 파일 -> 이미지 복사|`COPY . /app`|
|ADD|COPY에서 URL/압축 파일 해제 기능 포함<br>단순 복사에는 COPY 권장|`ADD https://example.com/data.tar.gz/ data`<br>`ADD my-archive.tar.gz /app/src`|
|CMD|컨테이너 시작 시 실행할 명령어|`CMD ["npm", "start"]`|
|WORKDIR|작업 디렉토리 설정. 이후 명령어는 이 경로 기준 실행<br>경로가 없을 경우 자동 생성|`WORKDIR /usr/src/app`|
|EXPOSE|컨테이너가 노출할 포트 정의|`EXPOSE80`|
|ENTRYPOINT|컨테이너 시작 시 실행 명령|`ENTRYPOINT ["node","app.js]`|

## 도커 빌드 순서

파이썬 환경에서 flask를 이용하여 간단히 실행해 보겠습니다.

### 1. Docker Install

- brew를 이용하여 도커를 설치해 줍니다.

```bash
brew install --cask docker
```

설치가 완료된 이후에 docker를 실행해 줍니다.

![docker-app.png](https://github.com/Euihyunee/euihyunee.github.io/blob/main/_posts/img/docker-app.png?raw=true)

✅설치 확인 방법

```bash
docker --version # 설치된 도커 클라이언트 버전 출력
docker version   # 클라이언트+서버(데몬) 버전 동시 확인

docker ps # 실행 중인 컨테이너 목록 조회
# Cannot connect to the Docker daemon -> 데몬 미실행

docker info # 도커 엔진 전체 상태 진단
# 출력 내용에 Server Version이 포함되어야 정상입니다.
```

> docker version이 출력되지 않으면 도커 데몬이 실행되지 않은 상태입니다. 필자는 재시작하니깐 정상적으로 출력되었습니다.

![success-docker-installed.png](https://github.com/Euihyunee/euihyunee.github.io/blob/main/_posts/img/success-docker-installed.png?raw=true)

✅도커 터미널 검증

```bash
docker --version && docker run hello-world
```

도커의 version~Status 설명 다음에 다음과 같은 메시지가 나오면 성공입니다. 

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
```

### 2. Docker 명령어

1️⃣ 프로젝트 디렉토리 경로로 이동

```bash
cd ${project_dir}

# 예시
cd ~/Desktop/project/docker_test
```

2️⃣ 프로젝트 구조 생성

```bash
mkdir myapp && cd myapp
touch Dockerfile app.py requirements.txt
```

3️⃣ 파일 작성

📋app.py

```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello():
    return "Hello from Docker on Mac!"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=80)
```

📋DockerFile

```text
# 파이썬 공식 이미지 (ARM64 호환 Slim 버전)
FROM python:3.11-slim

# 종속성 먼저 복사하여 캐시 활용
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# 애플리케이션 코드 복사
WORKDIR /app
COPY . .

# M1/M2 에뮬레이션 활성화 (필요시)
# RUN apt-get update && apt-get install -y libgl1-mesa-glx

# 실행 명령
CMD ["python", "app.py"]
```

📋 requirements.txt

```text
flask==3.0.0
```

4️⃣ 도커 이미지 빌드 및 실행

```bash
docker build --platform linux/amd64 -t myapp:1.0 .
```

5️⃣ 도커 컨테이너 실행(포트 매핑)

```bash
docker run -p 4000:80 --rm myapp:1.0
```

6️⃣ 서버 확인

[[127.0.0.1:4000](http://localhost:4000)] 접속

![container_check.png](https://github.com/Euihyunee/euihyunee.github.io/blob/main/_posts/img/container_check.png?raw=true)

## 도커 명령어 정리

✅ 도커 새 컨테이너 생성 및 실행

```bash
docker run -d -p 8080:80 --name my-python myapp:1.0 .
```

| 옵션          | 설명                                                                 | 예시                                  |
|---------------|----------------------------------------------------------------------|---------------------------------------|
| **`-d`**      | 백그라운드 실행 (데몬 모드)                                         | `docker run -d nginx`                 |
| **`-it`**     | 인터랙티브 모드 (터미널 연결 + TTY 할당)                            | `docker run -it ubuntu /bin/bash`     |
| **`--name`**  | 컨테이너 이름 지정                                                  | `docker run --name my-app my-image`   |
| **`-p`**      | 포트 매핑 (`호스트포트:컨테이너포트`)                               | `docker run -p 8080:80 nginx`         |
| **`-v`**      | 볼륨/디렉토리 마운트 (`호스트경로:컨테이너경로`)                    | `docker run -v /data:/app/data redis` |
| **`--rm`**    | 컨테이너 종료 시 자동 삭제                                          | `docker run --rm alpine echo "hello"` |
| **`-e`**      | 환경 변수 설정 (`KEY=VALUE`)                                        | `docker run -e "ENV=prod" my-app`     |
| **`--network`**| 네트워크 설정 (기본: `bridge`, `host`, `none` 등)                   | `docker run --network=host my-app`    |
| **`--restart`**| 재시작 정책 (`no`, `on-failure`, `always`, `unless-stopped`)        | `docker run --restart=always my-app`  |
| **`--cpus`**  | CPU 리소스 제한 (예: `1.5` → 1.5코어)                              | `docker run --cpus=2 my-app`          |
| **`--memory`**| 메모리 제한 (`512m`, `2g` 등)                                      | `docker run --memory=1g my-app`       |


✅ 이미지 목록 조회

```bash
docker images
```

✅ 컨테이너 목록 조회

```bash
docker ps # 실행 중인 컨테이너만 조회
docker ps -a # 실행 중/중지 포함
docker ps --filter "status=exited" # 종료된 컨테이너만 표시
docker ps --filter "name=hello-world" # 이름에 "hello_world" 포함된 컨테이너
```

✅ 현황 파악

```bash
docker system df
```

✅ 도커 재시작

```bash
# 도커 종료
osascript -e 'quit app "Docker"'
# 도커 재시작(백그라운드 실행)
open -a Docker --background
```

재시작 자동화

`~/.zshrc` or `~/.bashrc`에 추가

```bash
vim ~/.zshrc # 또는 ~/.bashrc

# 작성 후 :wq로 저장
alias docker_restart="osascript -e 'quit app \"Docker\"' && open -a Docker --background"

# 터미널에서 변경사항 적용
source ~/.zshrc # 또는 ~/.bashrc
```

이후 터미널에서 `docker_restart`로 재시작

## 마치며 

이상으로 도커 파일을 이미지로 빌드하여 컨테이너에서 실행하는 방법에 대해 알아보았습니다. 이 글에서는 기본 구성 요소에 대한 내용만 다루었습니다. 도커 파일에서 레이어 구조와 명령어 체인으로 레이어 최적화 전략을 할 수 있습니다. 여기에는 캐시 활용, 다단계 빌드, .dockerignore 등의 레이어 최적화 전략이 있으며 여기에 대해서는 다음에 다뤄보겠습니다.