---
title: HTTP 세션
author: dejavuhyo
date: 2025-05-26 08:30:00 +0900
categories: [DevOps, Web]
tags: [http, session, http-session, http-세션, 세션]
---

## 1. 일반적인 HTTP 세션
HTTP와 같은 클라이언트-서버 프로토콜에서 세션은 세 단계로 구성된다.

① 클라이언트는 TCP 연결(전송 계층이 TCP가 아닌 경우 해당 연결)을 설정한다.

② 클라이언트는 요청을 보내고 답변을 기다린다.

③ 서버는 요청을 처리하여 답변을 보내고 상태 코드와 적절한 데이터를 제공한다.

HTTP/1.1부터는 세 번째 단계를 완료한 후에도 연결이 더 이상 닫히지 않으며, 클라이언트에게 추가 요청이 허용된다. 즉, 두 번째와 세 번째 단계를 이제는 원하는 횟수만큼 수행할 수 있다.

## 2. 연결 설정
클라이언트-서버 프로토콜에서는 클라이언트가 연결을 설정한다. HTTP에서 연결을 여는 것은 기본 전송 계층(일반적으로 TCP)에서 연결을 시작하는 것을 의미한다.

TCP를 사용하는 경우, 컴퓨터의 HTTP 서버에 대한 기본 포트는 80번 포트이다. 8000이나 8080과 같은 다른 포트도 사용할 수 있다. 가져올 페이지의 URL에는 도메인 이름과 포트 번호가 모두 포함되지만, 포트 번호는 80인 경우 생략할 수 있다.

## 3. 클라이언트 요청 보내기
연결이 설정되면 사용자 에이전트가 요청을 전송할 수 있다(사용자 에이전트는 일반적으로 웹 브라우저이지만 크롤러 등 다른 어떤 것이든 될 수 있다). 클라이언트 요청은 CRLF(캐리지 리턴, 줄 바꿈)로 구분된 텍스트 지시어로 구성되며, 이는 세 개의 블록으로 나뉜다.

① 첫 번째 줄에는 요청 메서드와 매개변수가 포함된다.

② 프로토콜이나 도메인 이름이 없는 절대 URL로 문서 경로

* HTTP 프로토콜 버전

* 이후 줄은 HTTP 헤더를 나타내며, 서버에 어떤 유형의 데이터가 적합한지(예: 언어, MIME 유형) 또는 서버의 동작을 변경하는 다른 데이터(예: 이미 캐시된 경우 응답을 전송하지 않음)에 대한 정보를 제공한다. 이러한 HTTP 헤더는 빈 줄로 끝나는 블록을 형성한다.

③ 마지막 블록은 선택적 데이터 블록으로, 주로 POST 메서드에서 사용되는 추가 데이터를 포함할 수 있다.

### 1) 요청 예시
developer.mozilla.org의 루트 페이지를 가져오고(https://developer.mozilla.org/), 가능하다면 사용자 에이전트가 해당 페이지를 프랑스어로 선호한다는 것을 서버에 알린다.

```http
GET / HTTP/1.1
Host: developer.mozilla.org
Accept-Language: fr
```

마지막 빈 줄이 있다. 이 줄은 데이터 블록과 헤더 블록을 구분한다. Content-Length HTTP 헤더에 데이터 블록이 없으므로, 이 데이터 블록은 비어 있는 상태로 표시되어 헤더의 끝을 나타내며, 서버는 이 빈 줄을 수신하는 즉시 요청을 처리할 수 있다.

예를 들어, 양식의 결과를 보내는 경우이다.

```http
POST /contact_form.php HTTP/1.1
Host: developer.mozilla.org
Content-Length: 64
Content-Type: application/x-www-form-urlencoded

name=Joe%20User&request=Send%20me%20one%20of%20your%20catalogue
```

### 2) 요청 방법
HTTP는 리소스에 대해 수행할 작업을 나타내는 요청 메서드 집합을 정의한다. 명사로도 사용될 수 있지만, 이러한 요청 메서드는 HTTP 동사라고도 한다. 가장 일반적인 요청은 GET과 POST이다.

* GET 메서드는 지정된 리소스의 데이터 표현을 요청한다. GET을 사용하는 요청은 데이터만 가져와야 한다.

* POST 메서드는 서버에 데이터를 전송하여 상태를 변경할 수 있도록 한다. 이는 HTML 폼에서 자주 사용되는 메서드이다.

## 4. 서버 응답 구조
연결된 에이전트가 요청을 전송하면 웹 서버는 이를 처리하고 최종적으로 응답을 반환한다. 클라이언트 요청과 유사하게, 서버 응답은 CRLF로 구분된 텍스트 지시어로 구성되지만, 세 개의 블록으로 나뉜다.

* 첫 번째 줄인 상태 줄은 사용된 HTTP 버전에 대한 확인으로 구성되며, 그 뒤에 응답 상태 코드(및 사람이 읽을 수 있는 텍스트로 된 간략한 의미)가 나온다.

* 이후 줄은 특정 HTTP 헤더를 나타내며, 클라이언트에게 전송된 데이터에 대한 정보(예: 데이터 유형, 데이터 크기, 사용된 압축 알고리즘, 캐싱에 대한 힌트)를 제공한다. 클라이언트 요청에 대한 HTTP 헤더 블록과 마찬가지로, 이러한 HTTP 헤더는 빈 줄로 끝나는 블록을 형성한다.

* 마지막 블록은 선택적 데이터를 포함하는 데이터 블록이다.

### 1) 예시 응답
성공적인 웹 페이지 응답이다.

```http
HTTP/1.1 200 OK
Content-Type: text/html; charset=utf-8
Content-Length: 55743
Connection: keep-alive
Cache-Control: s-maxage=300, public, max-age=0
Content-Language: en-US
Date: Thu, 06 Dec 2018 17:37:18 GMT
ETag: "2e77ad1dc6ab0b53a2996dfd4653c1c3"
Server: meinheld/0.6.1
Strict-Transport-Security: max-age=63072000
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
X-XSS-Protection: 1; mode=block
Vary: Accept-Encoding,Cookie
Age: 7

<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>A basic webpage</title>
</head>
<body>
  <h1>Basic HTML webpage</h1>
  <p>Hello, world!</p>
</body>
</html>
```

요청된 리소스가 영구적으로 이동되었다는 알림이다.

```http
HTTP/1.1 301 Moved Permanently
Server: Apache/2.4.37 (Red Hat)
Content-Type: text/html; charset=utf-8
Date: Thu, 06 Dec 2018 17:33:08 GMT
Location: https://developer.mozilla.org/ (this is the new link to the resource; it is expected that the user-agent will fetch it)
Keep-Alive: timeout=15, max=98
Accept-Ranges: bytes
Via: Moz-Cache-zlb05
Connection: Keep-Alive
Content-Length: 325 (the content contains a default page to display if the user-agent is not able to follow the link)

<!doctype html>… (contains a site-customized page helping the user to find the missing resource)
```

요청한 리소스가 존재하지 않는다는 알림이다.

```http
HTTP/1.1 404 Not Found
Content-Type: text/html; charset=utf-8
Content-Length: 38217
Connection: keep-alive
Cache-Control: no-cache, no-store, must-revalidate, max-age=0
Content-Language: en-US
Date: Thu, 06 Dec 2018 17:35:13 GMT
Expires: Thu, 06 Dec 2018 17:35:13 GMT
Server: meinheld/0.6.1
Strict-Transport-Security: max-age=63072000
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
X-XSS-Protection: 1; mode=block
Vary: Accept-Encoding,Cookie
X-Cache: Error from cloudfront

<!doctype html>… (contains a site-customized page helping the user to find the missing resource)
```

### 2) 응답 상태 코드
[HTTP 응답 상태 코드](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Status)는 특정 HTTP 요청이 성공적으로 완료되었는지 여부를 나타낸다. 응답은 정보 응답, 성공 응답, 리디렉션, 클라이언트 오류, 서버 오류의 다섯 가지 클래스로 분류된다.

* 200: 확인되었다. 요청이 성공했다.

* 301: 영구적으로 이동되었다. 이 응답 코드는 요청된 리소스의 URI가 변경되었음을 의미한다.

* 404: 찾을 수 없음. 서버가 요청한 리소스를 찾을 수 없다.

## [출처 및 참고]
* [https://developer.mozilla.org/ko/docs/Web/HTTP/Guides/Session](https://developer.mozilla.org/ko/docs/Web/HTTP/Guides/Session)
