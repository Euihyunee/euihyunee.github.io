---
title: "🌐HTTP의 동작방식과 버전차이(작성중)"
date: 2025-01-06 11:28 +0900
categories: [cs]
tags: [http]
---

> HTTP는 현대 웹에서 필수적인 중요한 프로토콜입니다. HTTP는 인터넷 상에서 데이터를 주고받는 규칙을 정의하는 프로토콜입니다. HTTP는 웹의 발전과 함께 끊임없이 진화해왔습니다. 본 글에서는 HTTP의 기본 개념부터 시작하여 각 버전별 특징과 발전 과정을 살펴보고, 버전에 따른 사용 현황에 대해 알아보겠습니다.

## HTTP란?

HTTP(Hypertext Transfer Protocol)는 프로토콜이라는 말 그대로 클라이언트와 서버가 서로 데이터를 요청/응답하기 위해 사용되는 `통신 규약`을 말합니다. HTTP는 웹 상에서 정보를 주고받는 데 사용하며 다음과 같은 여러 종류의 데이터들을 폭 넓게 전송할 수가 있다. 

![http_evolution.png](https://github.com/Euihyunee/euihyunee.github.io/blob/main/_posts/img/http_data.png?raw=true)


> - HTTP의 애플리케이션 프로토콜에서는 전송 프로토콜로 TCP와 UDP 이외에도 QUIC가 있습니다. HTTP의 버전에 따라 사용하는 전송 프로토콜이 다릅니다. 
> - IP는 HTTP와 직접적으로 통신하진 않지만, HTTP 메시지가 네트워크를 통해 전달할 때 출발지에서 목적지까지 전달하는 패킷 라우팅, 네트워크 상의 주소를 식별하는 등 중요한 역활을 합니다.

#### HTTP의 구성요소 

HTTP는 클라이언트와 서버 간의 통신이 가능하다고 했습니다. 이는 HTTP 메시지로 요청과 응답이 가능하게 합니다. HTTP 메시지는 시작 줄(Start Line), 헤더(Headers), 본문(Body)로 이루어져 있으며 예제와 함께 살펴보겠습니다.


**HTTP 요청 예시**를 보며 살펴보겠습니다.
```html 
<!--시작 줄: 메서드, 경로, 버전 -->
POST /api/login HTTP/1.1

<!-- 헤더 -->
Host: www.example.com
Content-Type: application/json
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64)
Accept: application/json
Content-Length: 47
Connection: keep-alive

<!-- body -->
{
  "username": "johndoe",
  "password": "secretpass123"
}
```
1. 시작줄 : HTTP 메서드와 경로와 HTTP 버전을 나타내고 있습니다.
    - 메서드 : POST
    - 경로 : /api/login
    - HTTP 버전 : 1.1
2. 헤더 : 요청에 대한 추가 정보를 `Key Value` 형태로 제공합니다.
    - Host : 요청하는 서버의 도메인 이름
    - Content-Type : 메시지 본문의 미디어 타입
    - User-Agent : 클라이언트의 애플리케이션 정보
    - Accept : 클라이언트가 처리 가능한 미디어 타입
3. 본문 : 실제 전송되는 데이터를 나타냄
    - 여기서는 POST 요청으로 JSON 데이터를 실어서 보냈습니다. 


다음으로 **HTTP 응답 예시**입니다.
```html
<!-- 시작 줄 : 버전, 상태 코드, 상태 메시지 -->
HTTP/1.1 200 OK

<!-- 헤더 -->
Date: Tue, 07 Jan 2025 15:30:21 GMT
Server: Apache/2.4.41 (Ubuntu)
Content-Type: application/json; charset=utf-8
Content-Length: 234
Cache-Control: no-cache
X-Powered-By: Express
Access-Control-Allow-Origin: *

<!-- 본문 -->
{
  "status": "success",
  "message": "Login successful",
  "data": {
    "user_id": 12345,
    "username": "johndoe",
    "email": "johndoe@example.com",
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
  }
}
```
1. 응답 시작 줄:
    - HTTP 버전
    - 상태 코드: 요청이 성공 여부, 예외 등을 알려주는 코드입니다.
    - 상태 메시지: OK
2. 응답 헤더: 응답에 대한 추가 정보를 `Key Value` 형태로 제공합니다.
    - Date: 응답이 전송된 시간
    - Server: 웹 서버 소프트웨어 정보
    - Content-Type: 응답 본문(body)의 미디어 타입 및 문자 인코딩
    - Content-Length: 응답 본문(body)의 바이트 길이
    - Cache-Control: 캐시 제어
3. 응답 본문: 실제 전송되는 데이터를 나타냅니다.
    - 여기서는 JSON 형식의 데이터입니다.
    - 로그인 성공 상태, 메시지, 사용자 정보 및 인증 토큰을 본문에 포함하여 전송했습니다.

HTTP 메시지의 각 구성요소는 예시와 달리 실제 통신에서는 더 많은 헤더와 복잡한 본문이 포함될 수 있습니다. HTTP 메시지는 이러한 구성요소를 조합하여 클라이언트와 서버 간의 통신을 가능하게 합니다. HTTP 통신에서 각 구성요소는 중요한 역활을 하며, 웹 개발자는 반드시 이를 이해해야 합니다.  

</br>
다음으로 HTTP의 진호과정에 대해 살펴보겠습니다. 

## HTTP 진화과정

![http_evolution.png](https://github.com/Euihyunee/euihyunee.github.io/blob/main/_posts/img/http_evolution.png?raw=true)

[Web Almance](https://almanac.httparchive.org/en/2024/http)에 따르면 2024년 HTTP 버전별 통계는 다음과 같습니다. 

| HTTP Version | desktop | mobile |
|:--:|:--:|:--:|
|HTTP/1.1|22%|21%|
|HTTP/2|71%|70%|
|HTTP/3|7%|9%|

> HTTP 프로토컬의 버전별 사용량 통계를 살펴보면 웹 기술의 진화 과정과 그 중요성을 명확히 알 수 있습니다. 현재 HTTP/1.1, HTTP/2, HTTP/3가 공존하며 각각의 장단점에 따라 사용되고 있기에 버전에 따른 차이를 알고 있는 것은 중요합니다.

#### 🌐 HTTP/0.9 (1991년)

HTTP/0.9는 WWW(World Wide Web)의 초기 버전으로, 1991년에 팀 버너스 리(Tim Berners-Lee)가 학술 정보 교환을 위해 개발되었습니다. 당시의 HTTP는 극도로 단순한 프로토콜이며 특징은 다음과 같습니다. 

- 요청은 단일 라인으로, GET 메서드만 지원했습니다.
- HTTP헤더가 없었으며, HTML 파일만 전송 가능했습니다.
- 상태 코드가 없었기 때문에 특정 html 파일을 오류에 대한 설명과 함께 보냈다.
- 각 요청마다 새로운 TCP 연결을 생성하고 응답 후 즉시 종료했습니다.

#### HTTP/1.0 (1996년)

인터넷이 빠르게 진화하며 HTTP/0.9를 사용해서 HTML 문서는 물론 다양한 컨텐츠가 필요해졌습니다. 이에 HTTP-WG는 HTTP/1.0의 문서 [`RFC 1945`](https://datatracker.ietf.org/doc/html/rfc1945)를 발표하게 된다.



- 매번 요청마다 커넥션 수립 (매번 새로운 연결)
    - TCP 커넥션이 HTTP 요청마다 3-way handshake와 TearDown 반복
    - 서버에 어버헤드 발생, 시간의 지연
- 커넥션 하나당 요청 하나와 응답하나만 처리함 

- 개선사항 : 
    - 버전 정보 전송
    - 상태 코드 도입
    - HTTP 헤더 개념 도입
    - Content-Type 헤더로 다양한 문서 유형 지원
- 한계점
    - 연결 당 하나의 요청만 처리 가능(Keep-Alive 헤더로 부분적 해결 시도)
    - 대역폭 비효율적 사용
    - 연결 설정에 따른 속도 저하 

#### HTTP/1.1 (1997년)

- 개선사항 : 
    - 영속적 연결
    - 파이프라이닝
    - 호스트 헤더
    - 청크 전송 인코딩
    - 캐시 제어 메커니즘 개선
    - 새로운 메서드 추가(PUT, PATCH, DELETE 등)
- 한계점
    - Head-of-Line-Blocking 문제
    - 헤더 중복으로 인한 오버헤드
    - 요청 우선순위 지정 불가
    - 서버 푸시 기능 부재


#### HTTP/2.0 

- 개선사항 : 
    - 멀티플랙싱 : 단일 연결로 여러 요청/응답 동시 처리ㅣ
    - 헤더 압축 : HPACK 사용으로 오버헤드 감소
    - 서버 푸시 : 클라이언트 요청 없이 리소스 전송 가능
    - 스트림 우선순위 지성
    - 바이너리 프로토콜 사용
    - 연결 설정 시간 단축
    - Head-of-Line-Blocking 해결
- 잠재적 한계점
    - 새로운 프로토콜로 인한 호환성 문제
    - 구현 및 디버깅의 복잡성
    - 일부 네트워크에서의 성능 오버헤드
    - 보안 관련 새로운 도전 과제 

HTTP/1.1이 발표된 1997년 이후로 HTTP는 꾸준히 확장되었고, 그 사이 웹은 더욱 복잡해져 점점 한계점이 드러나기 시작했다. 

1. 헤더의 중복 

HTTP/1.1에 다양한 기능이 추가되면서 헤더에도 많은 메타 데이터가 담기게 되었습니다. 하지만 매 요청마다 헤더를 중복해서 전송해야만 했는데, 이것이 굉장한 낭비로 이어졌습니다. 

2. HOLB(Head-of-Line--Blocking)
가장 큰 문제는 서버가 항상 요청받은 순서대로 응답하기에 발생하는 HOLB(Head-of-Line Blocking) 문제 입니다. HTTP/1.1에서는 하나의 연결 내에서 응답 다중화(multiplexing)를 할 수  없었기 때문에 요청이 순차적으로 처리되어야 했는데, 서버가 응답 작성 중간에 문제가 생기면 후속 요청들이 전송되지 못하고 지연되는 문제가 발생했습니다. 
이 문제를 해결하기 위해 HTTP/1.1에서는 물리적인 TCP 연결을 여러 개 두는 방식으로 병렬 연결을 구현하게 되었습니다. 브라우저마다의 정책도 다른데 최대 6개의 동시 TCP 연결을 지원하는 것으로 알려져 있습니다. 하지만 이 방식 역시 임시적인 해결책일 뿐 HTTP/1.1의 HOLB를 해결할 수 있는 해결책은 아니었습니다. 

3. 이 외에도 요청 우선순위 지정 불가, 서버 푸시 기능 불가 등의 문제가 있었습니다. 

HTTP/2.0은 이러한 한계를 극복하기 위해 2015년 국제 표준으로 등장했습니다. HTTP/2.0 SPDY라는 프로토콜을 기반으로 동작합니다. SPDY는 HTTP/1.1의 성능 제한 사항을 해결하여 웹 페이지의 로드 대기 시간을 줄이는 것을 목표로 구글에서 개발하고 2009년 중반에 발표한 프로토콜입니다. 

이러한 HTTP/2.0은 기존 프로토콜과 호환성을 유지하면서, HTTP/1.1의 문제를 해결하기 위해 여러 특징을 가지고 있습니다. 

HTTP/2.0은 이진(binary) 프로토콜입니다. HTTP/1.1은 텍스트 기반 프로토콜이기 때문에 아스키코드로 작성되었습니다. 덕분에 사람이 읽기에는 편하지만, 불필요하게 데이터가 커지는 문제가 있었다. 

HTTP/2.0에서는 보내야 할 데이터를 바이너리로 변환하는 계층이 있기 때문에 단순 텍스트를 전송하는 것보다 훨씬 더 효율적으로 데이터를 전송할 수 있습니다. 

#### HTTP/3.0 (2022년)

- 개선 사항:
    - QUIC(Quick UDP Internet Connections) 프로토콜 사용 : UDP 기반으로 HTTP/2.0의 HTTP/1.1보다 30% faster
    - 연결 설정 시간 단축
    - HOLB 해결 
    - 향상된 오류 복구 및 혼잡 제어
    - 연걸 마이그레이션 지원 
    - TLS 1.3 기반 암호화로 보안 강화 
- 한계점
    - 새로운 프로토콜로 인한 호환성 문제
    - 구현 및 디버깅의 복잡성
    - 일부 네트워크에서의 성능 오버헤드
    - 보안 관련 새로운 도전 과제 