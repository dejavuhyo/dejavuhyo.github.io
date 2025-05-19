---
title: HTTP의 발전
author: dejavuhyo
date: 2025-05-19 08:30:00 +0900
categories: [DevOps, Web]
tags: [http, web, world-wide-web, http-protocol, http-발전, http-프로토콜]
---

## 1. World Wide Web의 발명
1989년, CERN에서 근무하던 팀 버너스-리는 인터넷 기반 하이퍼텍스트 시스템 구축을 제안했다. 처음에는 메시(Mesh) 라고 불렸지만, 1990년 구현 과정에서 월드 와이드 웹(World Wide Web)으로 이름이 바뀌었다. 기존의 TCP와 IP 프로토콜을 기반으로 구축된 이 시스템은 네 가지 구성 요소로 이루어져 있다.

* 하이퍼텍스트 문서를 표현하기 위한 텍스트 형식의 '하이퍼텍스트 마크업 언어(HTML)'

* 하이퍼텍스트 문서를 교환하기 위한 간단한 프로토콜인 '하이퍼텍스트 전송 프로토콜(HTTP)'

* 문서를 보여주기(그리고 수정하기) 위한 클라이언트인 '월드 와이드 웹(WorldWideWeb)'이라고 불리는 첫번째 브라우저

* 문서에 접근하도록 해주는 서버, 'httpd'의 초기 버전

이 네 가지 구성 요소는 1990년 말에 완성되었고, 최초의 서버는 1991년 초 CERN 외부에서 운영되기 시작했다. 1991년 8월 6일, 팀 버너스-리는 공개 alt.hypertext 뉴스그룹에 글을 올렸다. 이는 현재 월드 와이드 웹(WWW)이 공개 프로젝트로서 공식적으로 시작된 것으로 여겨진다.

초기 단계에 사용된 HTTP 프로토콜은 매우 간단했다. 이후 HTTP/0.9로 명명되었고, 원라인 프로토콜(One-Line Protocol)이라고도 불린다.

## 2. HTTP/0.9 - 단일 라인 프로토콜
HTTP 초기 버전에는 버전 번호가 없었으며, 이후 버전과 구분하기 위해 0.9라는 이름이 붙었다. HTTP/0.9는 매우 간단했다. 요청은 한 줄로 구성되었고, 가능한 유일한 메서드로 시작하여 GET리소스 경로가 이어졌다. 서버에 연결되면 프로토콜, 서버, 포트가 필요하지 않았기 때문에 전체 URL은 포함되지 않았다.


```http
GET /mypage.html
```

응답도 매우 간단했다. 파일 자체만 있었을 뿐이었다.


```html
<html>
    A very simple HTML page
</html>
```

이후의 진화와는 달리 HTTP 헤더가 없었다. 즉, HTML 파일만 전송할 수 있었다. 상태 코드나 오류 코드도 없었다. 문제가 발생하면 특정 HTML 파일이 생성되어 사용자가 볼 수 있도록 문제에 대한 설명이 포함되었다.

## 3. HTTP/1.0 - 확장성 구축
HTTP/0.9는 매우 제한적이었지만, 브라우저와 서버가 빠르게 이를 더욱 다재다능하게 만들었다.

* 버전 정보는 각 요청 내에 전송되었다(GET 라인에 HTTP/1.0이 추가).

* 응답 시작 부분에 상태 코드 줄도 전송되었다. 이를 통해 브라우저는 요청의 성공 또는 실패를 스스로 인식하고 그에 따라 동작을 조정할 수 있었다. 예를 들어, 로컬 캐시를 특정 방식으로 업데이트하거나 사용하는 것이다.

* HTTP 헤더 개념이 요청과 응답 모두에 도입되었다. 메타데이터를 전송할 수 있게 되면서 프로토콜은 매우 유연하고 확장 가능해졌다.

* Content-Type 헤더 덕분에 일반 HTML 파일 이외의 문서도 전송할 수 있다.

일반적인 요청과 응답은 다음과 같다.

```http
GET /mypage.html HTTP/1.0
User-Agent: NCSA_Mosaic/2.0 (Windows 3.1)

200 OK
Date: Tue, 15 Nov 1994 08:12:31 GMT
Server: CERN/3.0 libwww/2.17
Content-Type: text/html
<HTML>
A page with an image
  <IMG SRC="/myimage.gif">
</HTML>
```

그 다음에는 두 번째 연결이 이어졌고 이미지를 가져오라는 요청(해당 응답 포함)이 왔다.

```http
GET /myimage.gif HTTP/1.0
User-Agent: NCSA_Mosaic/2.0 (Windows 3.1)

200 OK
Date: Tue, 15 Nov 1994 08:12:32 GMT
Server: CERN/3.0 libwww/2.17
Content-Type: text/gif
(image content)
```

1991년부터 1995년 사이에 이러한 프로토콜은 '시도해 보고(try-and-see)' 방식으로 도입되었다. 서버와 브라우저가 기능을 추가하고 그 효과가 나타나는지 확인하는 방식이었다. 상호운용성 문제가 흔했다. 이러한 문제를 해결하기 위해 1996년 11월에 일반적인 실행 방식을 설명하는 정보 문서가 발표되었다. 이 문서는 [RFC 1945](https://datatracker.ietf.org/doc/html/rfc1945)로 알려져 있으며 HTTP/1.0을 정의했다.

## 4. HTTP/1.1 : 표준 프로토콜
그동안 적절한 표준화가 진행 중이었다. 이는 HTTP/1.0의 다양한 구현과 병행하여 이루어졌다. HTTP의 첫 번째 표준화된 버전인 HTTP/1.1은 HTTP/1.0 출시 후 불과 몇 달 후인 1997년 초에 발표되었다.

HTTP/1.1은 모호성을 해소하고 수많은 개선 사항을 도입했다.

* 연결을 재사용할 수 있어 시간이 절약되었다. 더 이상 단일 원본 문서에 포함된 리소스를 표시하기 위해 연결을 여러 번 열 필요가 없다.

* 파이프라인이 추가되었다. 이를 통해 첫 번째 요청에 대한 응답이 완전히 전송되기 전에 두 번째 요청을 전송할 수 있었다. 이를 통해 통신 지연 시간이 단축되었다.

* 청크 응답도 지원되었다.

* 추가적인 캐시 제어 메커니즘이 도입되었다.

* 언어, 인코딩, 유형을 포함한 콘텐츠 협상 기능이 도입되었다. 이제 클라이언트와 서버는 어떤 콘텐츠를 교환할지 합의할 수 있다.

* 헤더 덕분에 Host동일한 IP 주소에서 서로 다른 도메인을 호스팅할 수 있는 기능이 제공되어 서버를 함께 배치할 수 있게 되었다.

하나의 연결을 통해 이루어지는 일반적인 요청 흐름은 다음과 같다.

```http
GET /en-US/docs/Glossary/CORS-safelisted_request_header HTTP/1.1
Host: developer.mozilla.org
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.9; rv:50.0) Gecko/20100101 Firefox/50.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: https://developer.mozilla.org/en-US/docs/Glossary/CORS-safelisted_request_header

HTTP/1.1 200 OK
Connection: Keep-Alive
Content-Encoding: gzip
Content-Type: text/html; charset=utf-8
Date: Wed, 20 Jul 2016 10:55:30 GMT
Etag: "547fa7e369ef56031dd3bff2ace9fc0832eb251a"
Keep-Alive: timeout=5, max=1000
Last-Modified: Tue, 19 Jul 2016 00:59:33 GMT
Server: Apache
Transfer-Encoding: chunked
Vary: Cookie, Accept-Encoding

(content)

GET /static/img/header-background.png HTTP/1.1
Host: developer.mozilla.org
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.9; rv:50.0) Gecko/20100101 Firefox/50.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: https://developer.mozilla.org/en-US/docs/Glossary/CORS-safelisted_request_header

HTTP/1.1 200 OK
Age: 9578461
Cache-Control: public, max-age=315360000
Connection: keep-alive
Content-Length: 3077
Content-Type: image/png
Date: Thu, 31 Mar 2016 13:34:46 GMT
Last-Modified: Wed, 21 Oct 2015 18:27:50 GMT
Server: Apache

(image content of 3077 bytes)
```

HTTP/1.1은 1997년 1월에 [RFC 2068](https://datatracker.ietf.org/doc/html/rfc2068)로 처음 공개되었다.

## 5. 20년 이상의 개발
HTTP의 확장성 덕분에 새로운 헤더와 메서드를 쉽게 만들 수 있었다. HTTP/1.1 프로토콜은 1999년 6월에 공개된 [RFC 2616](https://datatracker.ietf.org/doc/html/rfc2616)과 HTTP/2가 출시되기 전인 2014년 6월에 공개된 [RFC 7230](https://datatracker.ietf.org/doc/html/rfc7230)-[RFC 7235](https://datatracker.ietf.org/doc/html/rfc7235)의 두 번의 개정을 거쳐 개선되었지만 15년 이상 매우 안정적이었다. HTTP/1.1은 2022년에 [RFC 9110](https://datatracker.ietf.org/doc/html/rfc9110)으로 다시 업데이트되었다. HTTP/1.1이 업데이트되었을 뿐만 아니라 HTTP 전체가 개정되어 이제 의미론([RFC 9110](https://datatracker.ietf.org/doc/html/rfc9110)), 모든 HTTP 버전에 적용되는 캐싱([RFC 9111](https://datatracker.ietf.org/doc/html/rfc9111)), HTTP/1.1([RFC 9112](https://datatracker.ietf.org/doc/html/rfc9112)), HTTP/2([RFC 9113](https://datatracker.ietf.org/doc/html/rfc9113)), HTTP/3([RFC 9114](https://datatracker.ietf.org/doc/html/rfc9114))의 문서로 나뉜다. 또한 이 사양은 마침내 인터넷 표준(STD 97)의 지위를 얻었지만 이전에는 항상 제안/초안 표준이었다.

### 1) 보안 전송을 위한 HTTP 사용
HTTP에 가장 큰 변화는 1994년 말에 이루어졌다. 컴퓨터 서비스 회사인 넷스케이프 커뮤니케이션즈(Netscape Communications)는 기본 TCP/IP 스택을 통해 HTTP를 전송하는 대신, 그 위에 추가적인 암호화된 전송 계층인 SSL(Secure Socket Layer)을 구축했다. SSL 1.0은 공개되지 않았지만, SSL 2.0과 그 후속 버전인 SSL 3.0은 전자상거래 웹사이트 구축을 가능하게 했다. 이를 위해 서버와 클라이언트 간에 교환되는 메시지의 진위를 암호화하고 보장했다. SSL은 결국 표준화되어 TLS로 발전했다.

같은 시기에 암호화된 전송 계층의 필요성이 분명해졌다. 웹은 더 이상 학술적인 네트워크가 아니라 광고주, 일반인, 범죄자들이 최대한 많은 개인 정보를 확보하기 위해 경쟁하는 정글로 변했다. HTTP 기반으로 구축된 애플리케이션이 더욱 강력해지고 주소록, 이메일, 사용자 위치 정보와 같은 개인 정보에 대한 접근을 요구함에 따라, TLS는 전자상거래 사용 사례 외에서도 필수적이 되었다.

### 2) 복잡한 애플리케이션에 HTTP 사용
팀 버너스-리는 원래 HTTP를 읽기 전용 매체로 생각하지 않았다. 그는 사람들이 원격으로 문서를 추가하고 이동할 수 있는 웹, 즉 일종의 분산 파일 시스템을 만들고 싶었다. 1996년경, HTTP는 문서 작성을 허용하도록 확장되었고, WebDAV라는 표준이 만들어졌다.

이 표준은 주소록 항목을 처리하는 CardDAV와 달력을 처리하는 CalDAV와 같은 특정 애플리케이션을 포함하도록 확장되었다. 하지만 이러한 모든 *DAV 확장 기능에는 단점이 있었다. 서버에서 구현되었을 때만 사용할 수 있다는 것이었다.

2000년에 HTTP 사용을 위한 새로운 패턴인 [표현형 상태 전송](https://developer.mozilla.org/en-US/docs/Glossary/REST) (REST)이 고안되었다. 이 API는 새로운 HTTP 메서드를 기반으로 하지 않고, 기본 HTTP/1.1 메서드를 사용하여 특정 URI에 접근하는 방식을 사용했다. 이를 통해 모든 웹 애플리케이션은 브라우저나 서버를 업데이트하지 않고도 API를 통해 데이터를 검색하고 수정할 수 있었다. 필요한 모든 정보는 표준 HTTP/1.1을 통해 웹사이트에서 제공하는 파일에 내장되었다.

REST 모델의 단점은 각 웹사이트가 자체적인 비표준 RESTful API를 정의하고 이를 완전히 제어할 수 있다는 점이었다. 이는 클라이언트와 서버가 상호 운용 가능한 *DAV 확장과는 달랐다. RESTful API는 2010년대에 매우 보편화되었다.

2005년 이후 웹 페이지에 더 많은 API가 제공되었다. 이러한 API 중 일부는 특정 목적을 위해 HTTP 프로토콜을 확장한다.

* [Server-sent events](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events)는 서버가 가끔씩 브라우저에 메시지를 푸시하는 경우이다.

* [WebSocket](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API)은 기존 HTTP 연결을 업그레이드하여 설정할 수 있는 새로운 프로토콜이다.

### 3) 웹의 보안 모델 완화
HTTP는 동일 출처 정책(Same-Origin Policy) 이라고 하는 웹 보안 모델과는 별개이다. 사실, 현재의 웹 보안 모델은 HTTP가 개발된 이후에 개발되었다. 수년에 걸쳐 특정 제약 조건 하에서 이 정책의 일부 제한을 해제하는 것이 유용하다는 것이 입증되었다. 서버는 새로운 HTTP 헤더 세트를 사용하여 이러한 제한을 해제할 양과 시기를 클라이언트에 전송했다. 이러한 제한은 CORS(Cross-Origin Resource Sharing) 및 CSP(Content Security Policy)와 같은 사양에 정의되어 있다.

이러한 대규모 확장 기능 외에도 많은 헤더가 추가되었으며, 때로는 실험적으로만 추가되기도 했다. 주목할 만한 헤더로는 개인정보 보호 기능을 제어하는 ​​추적 금지(DNT), X-Frame-Options, 그리고 업그레이드-불안정-요청(Upgrade-Insecure-Requests)이 있으며, 이 외에도 많은 헤더가 존재한다.

## 6. HTTP/2 - 더 높은 성능을 위한 프로토콜
수년에 걸쳐 웹 페이지는 더욱 복잡해졌다. 어떤 페이지는 그 자체로 애플리케이션이 되기도 했다. 더 많은 시각적 미디어가 표시되었고, 상호작용성을 추가하는 스크립트의 양과 크기 또한 증가했다.

훨씬 더 많은 HTTP 요청을 통해 훨씬 더 많은 데이터가 전송되었고, 이는 HTTP/1.1 연결의 복잡성과 오버헤드를 증가시켰다. 이를 해결하기 위해 구글은 2010년대 초 실험적인 프로토콜인 SPDY를 구현했다.

클라이언트와 서버 간의 데이터 교환을 위한 이 대안적인 방식은 브라우저와 서버 모두에서 작업하는 개발자들의 관심을 끌었다. SPDY는 응답성을 향상시키고 중복 데이터 전송 문제를 해결하여 HTTP/2 프로토콜의 기반이 되었다.

HTTP/2 프로토콜은 몇 가지 면에서 HTTP/1.1과 다르다.

* 텍스트 프로토콜이 아닌 바이너리 프로토콜이다. 수동으로 읽고 생성할 수 없다. 이러한 어려움에도 불구하고, 향상된 최적화 기술을 구현할 수 있다.

* 다중화 프로토콜이다. 동일한 연결을 통해 병렬 요청을 보낼 수 있으므로 HTTP/1.x 프로토콜의 제약이 사라진다.

* 헤더를 압축한다. 헤더는 여러 요청에서 유사한 경우가 많기 때문에, 이를 통해 전송되는 데이터의 중복과 오버헤드를 제거한다.

2015년 5월 공식 표준화된 HTTP/2는 2022년 1월 전체 웹사이트의 46.9%로 정점을 찍었다. 트래픽이 많은 웹사이트는 데이터 전송 오버헤드와 그에 따른 예산 절감을 위해 가장 빠르게 도입되었다.

HTTP/2가 웹사이트와 애플리케이션을 변경할 필요가 없었기 때문에 이러한 빠른 도입이 가능했을 가능성이 높다. HTTP/2를 사용하려면 최신 브라우저와 통신하는 최신 서버만 있으면 되었다. 도입을 촉진하는 데는 제한된 그룹만 필요했고, 기존 브라우저와 서버 버전이 업데이트됨에 따라 웹 개발자의 큰 노력 없이도 사용량이 자연스럽게 증가했다.

## 7. HTTP/2 이후의 진화
HTTP의 확장성은 여전히 ​​새로운 기능을 추가하는데 활용되고 있다. 특히 2016년에 등장한 HTTP 프로토콜의 새로운 확장 기능을 예로 들 수 있다.

* Alt-Svc 지원을 통해 특정 리소스의 식별 정보와 위치를 분리할 수 있었다. 이는 더욱 스마트한 CDN 캐싱 메커니즘을 의미했다.

* 클라이언트 힌트가 도입되어 브라우저 또는 클라이언트가 요구 사항 및 하드웨어 제약 조건에 대한 정보를 서버에 사전에 전달할 수 있게 되었다.

* 쿠키 헤더에 보안 관련 접두사가 도입되어 보안 쿠키가 변경되지 않도록 보장하는데 도움이 되었다.

## 8. HTTP/3 - QUIC를 통한 HTTP
HTTP의 다음 주요 버전인 HTTP/3는 이전 버전의 HTTP와 동일한 의미를 갖지만 전송 계층 부분에서 TCP 대신 QUIC을 사용한다. 2022년 10월까지 전체 웹사이트의 26%가 HTTP/3를 사용했다.

QUIC은 HTTP 연결의 지연 시간을 크게 단축하도록 설계되었다. HTTP/2와 마찬가지로 다중화 프로토콜이지만, HTTP/2는 단일 TCP 연결을 통해 실행되므로 TCP 계층에서 처리되는 패킷 손실 감지 및 재전송으로 인해 모든 스트림이 차단될 수 있다.

QUIC은 UDP를 통해 여러 스트림을 실행 하고 각 스트림에 대해 독립적으로 패킷 손실 감지 및 재전송을 구현한다. 따라서 오류 발생 시 해당 패킷에 데이터가 포함된 스트림만 차단된다.

[RFC 9114](https://datatracker.ietf.org/doc/html/rfc9114)에 정의된 HTTP/3는 Chromium(Chrome 및 Edge와 같은 변형)과 Firefox를 포함한 대부분의 주요 브라우저에서 지원된다.

## [출처 및 참고]
* [https://developer.mozilla.org/ko/docs/Web/HTTP/Guides/Evolution_of_HTTP](https://developer.mozilla.org/ko/docs/Web/HTTP/Guides/Evolution_of_HTTP)
