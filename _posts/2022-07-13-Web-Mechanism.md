---
title: "[Web] 웹 동작방식"
author: "김소연"
date: 2022-07-13 21:00:00 -0500
categories: [Web Application]
tags: []
---



웹 브라우저에서 URL을 입력했을 때 일어나는 일

> 1. 웹 브라우저에 URL을 입력하고 Enter 키를 누릅니다.
> 2. 웹 브라우저가 도메인의 IP주소를 조회합니다.
>    (먼저 캐시를 찾고, 그 다음 DNS를 검색합니다.)
> 3. 웹 브라우저가 찾은 IP주소를 기반으로 서버와의 TCP 연결을 시작합니다.
> 4. 웹 브라우저가 HTTP 요청을 서버로 전송합니다.
>    (필요한 경우, HTTPS 보안 통신이 진행됩니다.)
> 5. 웹 서버가 요청을 처리하고 응답을 다시 웹 브라우저로 전송합니다.
> 6. 웹 브라우저가 전송 받은 콘텐츠를 렌더링합니다.



### 1. 웹 브라우저에 URL을 입력하고 Enter 키를 누릅니다. 

#### URL 분류

`https://channy.creation.net/blog/hello-world`

- 통신 규약 (Protocol)  `https://`
- 도메인 (Domain) `channy.creation.net`
- 경로 (Path) `/blog`
- 리소스 (Resource) `hello-world`



### 2. 웹 브라우저가 도메인의 IP주소를 조회합니다. 

- 먼저 캐시를 확인합니다.

  DNS는 빨라야하기 때문에 DNS 데이터는 웹 브라우저 사이의 서로 다른 계층과 인터넷의 다양한 위치에 임시로 저장됩니다. 이를 캐시(Cache)라고 부릅니다.
  웹 브라우저는 고유한 캐시, 운영체제 캐시, 라우터의 로컬 네트워크 캐시, 회사 네트워크 또는 인터넷 서비스 제공업체(ISP)의 DNS 서버 캐시를 확인합니다.

- 캐시 계층에서 IP주소를 찾을 수 없는 경우 DNS를 조회합니다.

  회사 네트워크 또는 ISP의 DNS 서버가 재귀적 DNS 조회를 수행합니다. 재귀적 DNS 조회는 인터넷에 있는 여러 DNS 서버를 요청하며, 검색될 때까지 DNS 레코드에 대해 더 많은 DNS 서버에 요청합니다.

- 웹 브라우저가 IP주소로 DNS 레코드를 가져오면 인터넷에서 서버를 찾아 연결을 설정합니다.



### 3. 웹 브라우저가 찾은 IP주소를 기반으로 서버와의 TCP 연결을 시작합니다. 

- 웹 브라우저 요청 패킷은 일반적으로 TCP/IP라고 하는 전송 제어 프로토콜을 사용하여 연결할 IP주소가 있는 웹 서버를 찾습니다.

  하지만, 웹 서버에 직접 연결하기 보다 콘텐츠 전송 네트워크(CDN)를 사용하여 정적 및 동적 콘텐츠를 웹 브라우저 가까이에 위치시킵니다. (동영상, 음악 파일 같은 것은 서비스 제공자들의 데이터 센터에 있는 콘텐츠 배포 서버에 위치할 확률이 큽니다.)

- 웹 브라우저가 인터넷에서 서버를 찾으면 TCP 연결을 설정하고 HTTP를 통해 평문 통신을 시작합니다.

  하지만, HTTPS를 사용하는 경우 주고받는 데이터의 암호화를 위해 TLS 핸드셰이크라는 추가 과정을 수행합니다. (Transport Layer Security, 암호화를 할 상호 대상을 확인하는 것)



### 4. 웹 브라우저가 HTTP 요청을 서버로 전송합니다.

#### HTTP 요청

- 요청 라인

  `GET /blog/1620 HTTP/1.1`

  - 요청 메서드 (GET, POST, PUT, PATCH, DELETE 또는 다른 HTTP 동사)
  - 요청된 리소스를 가리키는 경로
  - 통신할 HTTP 버전

- 요청 헤더(요청에 대한 메타데이터)

  ```
  Host : channy.creation.net
  User-Agent: curl/7.64.1
  Accept: */*
  ```

  키-값 페어

  요청을 라우팅하는 데 도움이 되는 추가 정보를 클라이언트에서 전달하고, 어떤 유형의 클라이언트가 요청을 수행하는지 나타내며, 블루/그린 배포 및 카나리 배포를 지원하는 데 사용할 수 있습니다.

- 본문

  - 보통 `GET` 요청일 때는 비어있다.
  - `POST`, `PUT`, `PATCH` 등 리소스를 조작하는 경우 본문에 생성하거나 업데이트할 클라이언트의 데이터가 포함됩니다.
  - 서로 다른 형식일 수 있으며 서버는 요청 헤더인 `Content-Type`을 기반으로 형식을 이해합니다.

### 5. 웹 서버가 요청을 처리하고 응답을 다시 웹 브라우저로 전송합니다. 

### HTTP 응답

- 상태 라인

  클라이언트에게 요청 상태를 알려줍니다.

  `HTTP/1.1 200 OK`

  - HTTP 버전
  - 요청 상태의 숫자
  - 텍스트 표현

- 응답 헤더

  브라우저에 응답 처리 방법을 알려줍니다.

  ```
  Date: Tue, 25 May 2021 19:40:59 GMT
  Server: Apache
  X-Frame-Options: SAMEORIGIN
  X-Powered-By: Express
  Cache-Control: max-age=0, no-cache
  Content-Type: text/html; charset=utf-8
  Vary: Accept-Encoding
  X-Mod-Pagespeed: 1.13.35.2-0
  Content-Encoding: br
  Keep-Alive: timeout=1, max=100
  Connection: Keep-Alive
  Transfer-Encoding: chunked
  ```

- 해당 경로에서 요청된 리소스

  HTML, CSS, Javascript, 이미지 파일과 같은 콘텐츠 또는 데이터

### 6. 웹 브라우저가 전송 받은 콘텐츠를 렌더링합니다. 

웹 브라우저가 서버로부터 응답을 받으면 응답 헤더를 검사하여 리소스를 렌더링하는 방법에 대한 정보를 확인합니다. 위의 `Content-Type` 헤더에서 어떤 리소스를 수신했는지 알 수 있습니다.



### 출처📎


- [Amazon Web Services 한국 블로그](https://aws.amazon.com/ko/blogs/korea/what-happens-when-you-type-a-url-into-your-browser/?utm_source=dlvr.it&utm_medium=facebook&mibextid=MNDb3O&fs=e&s=cl)