---
layout: article
title: ETC - http message 알아보기 (content-type이란? status란?)
aside:
 toc: true
tags: etc


---

## HTTP message란?

HTTP 프로토콜을 사용해 웹에서 클라이언트와 서버가 데이터를 주고 받을 수 있다.    

클라이언트가 서버에게 특정한 작업을 요청하는 것을 **request**, 클라이언트가 서버어게 데이터를 보내주는 것을 **response**라고 한다. 이 두가지 종류의 통신을  **HTTP message** 라고 한다.      

HTTP message에는 데이터 외에도 데이터의 형식, 통신 진행 상황 등의 추가 정보가 담겨있다. HTTP request를 보내야하는 클라이언트와 response를 작성해야하는 서버 개발자가 HTTP message의 구성을 이해한다면 개발에 큰 도움이 될 것이다. 



![](/assets/httpmsg.png))

---

## Request HTTP message 구조

### 1. Start line 

- http method 
  - GET : 서버에서 정보를 받아오기
  - POST : 데이터를 서버로 보내 추가 작업(생성, 변경 등)을 진행
  - PUT
  - DELETE
  - HEAD
  - OPTIONS
- target URL (path + port(`:`))
- http version 

```sh
GET /background.png HTTP/1.0
```

### 2. Headers

- Request headers : request에 제약 조건을 추가
  - User-Agent
  - Accept
- General headers
  - Connect
  - Via
- Representation headers : request data의 상태 정보를 제공
  - Content-Type ([MIME 종류](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types))
  - Content-Length

```sh
Host: localhost:8000
User-Agent:
Accept: text/html,application/xml,...,*/*; q=0.8
Content-Type: text/html; charset=UTF-8
```

### 3. Body

http method가 `POST` 인 경우 body에 데이터를 입력해서 request message를 서버에게 보낸다.

---

## Response HTTP message 구조

### 1. Status line 

- Protocol version
- Status code (200, 404, 302, ...)
- Status text

```sh
HTTP/1.1 404 Not Found.
```

### 2. Headers

* Response headers
  * Server
  * Vary 
* General headers
  * Connection
* Representation headers
  * Content-Type
  * Content-Encoding

### 3. Body

요청을 처리한 데이터



## Reference 

1. [http message](https://developer.mozilla.org/en-US/docs/Web/HTTP/Messages#start_line)

