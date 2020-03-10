# HTTP(HyperText Transfer Protocol)

> [WWW](https://ko.wikipedia.org/wiki/WWW) 상에서 정보를 주고받을 수 있는 [프로토콜](https://ko.wikipedia.org/wiki/통신_프로토콜)이다. 주로 [HTML](https://ko.wikipedia.org/wiki/HTML) 문서를 주고받는 데에 쓰인다. [TCP](https://ko.wikipedia.org/wiki/전송_제어_프로토콜)와 [UDP](https://ko.wikipedia.org/wiki/사용자_데이터그램_프로토콜)를 사용하며, 80번 포트를 사용한다. [1996년](https://ko.wikipedia.org/wiki/1996년) 버전 1.0, 그리고 [1999년](https://ko.wikipedia.org/wiki/1999년) 1.1이 각각 발표되었다.

HTTP 는 서버/클라이언트 사이에 이루어지는 request/response 프로토콜이다.

-> 클라이언트의 요청에 서버가 응답한다

그 결과 `http:`로 시작하는  URL로 조회할 수 있다.

#### Request(Get)

요청메소드, URI, 프로토콜 버전, 각종 헤더

```shell
GET /restapi/v1.0 HTTP/1.1
Accept: application/json
Authorization: Bearer UExBMDFUMDRQV1MwMnzpdvtYYNWMSJ7CL8h0zM6q6a9ntw
```

#### Response

프로토콜, 응답코드, 날짜, 서버프로그램 및 스크립트 정보, 등등 여러 정보 저장가능

```shell
HTTP/1.1 200 OK
Date: Mon, 23 May 2005 22:38:34 GMT
Content-Type: text/html; charset=UTF-8
Content-Encoding: UTF-8
Content-Length: 138
Last-Modified: Wed, 08 Jan 2003 23:11:55 GMT
Server: Apache/1.3.3.7 (Unix) (Red-Hat/Linux)
ETag: "3f80f-1b6-3e1cb03b"
Accept-Ranges: bytes
Connection: close

<html>
<head>
  <title>An Example Page</title>
</head>
<body>
  Hello World, this is a very simple HTML document.
</body>
</html>
```

### HTTP 작동 방식

`Connectless`,` Stateless` 방식

- 서버에 연결 -> 요청 -> 응답 -> 연결 끊기
- 위의 방식으로 처리하기 때문에 클라이언트의 전의 상태를 알 수 없다( ex)로그인 유지 문제 )
- 전의 상태를 알 수 없는 문제를 해결하기 위해 `쿠키(Cookie)` 사용

### HTTP 메소드

- GET - 정보 요청
- POST - 정보 등록
- PUT - 정보 수정
- DELETE - 정보 삭제
- HEAD - 헤더 정보만 요청시
- OPTIONS - 웹 서버가 지원하는 메소드의 종류
- TRACE - 클라이언트의 요청을 그대로 반환(서버의 상태확인 용도로 많이 사용)

> Restful API 참고

### 응답 코드

- 1XX - 정보
- 2XX - 성공
- 3XX - Redirection
- 4XX - Client Error
- 5XX - Server Error

#### HTTP 1.1 keep alive

http1.1에서 생긴 개념

keep alive가 없을 경우 연결 끊김의 반복으로 리소스 소모가 매우 크기 때문

keep alive는 하나의 TCP연결을 사용하여 복수의 request/response 가능