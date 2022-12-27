---
title: Spring WebClient vs RestTemplate
author: dejavuhyo
date: 2022-11-22 19:40:00 +0900
categories: [Application, Framework]
tags: [spring-webclient, spring-resttemplate, webclient, resttemplate, webclient-vs-resttemplate, webclient-resttemplate-비교]
---

## 1. 차단 및 비차단 클라이언트
Spring 5 문서는 이제 WebClient가 HTTP 요청을 만드는 데 선호되는 방법임을 시사한다. WebClient는 Spring WebFlux의 일부이며 고전적인 RestTemplate를 대체하기 위한 것이다.

RestTemplate에 비해 WebClient는 기능적인 느낌이 더 좋고 완전히 반응한다. Spring 5.0 이후 RestTemplate는 더 이상 사용되지 않는다. 그것은 아마도 좀 더 오래 머물 것이지만 앞으로 출시될 주요한 새로운 기능들은 추가되지 않을 것이다. 따라서 새 코드에서 RestTemplate를 사용하지 않는 것이 좋다.

## 2. WebClient와 RestTemplate의 차이점

### 1) RestTemplate 차단 클라이언트
RestTemplate는 본질적으로 차단되며 자바 서블릿 API의 요청당 하나의 스레드 모델을 사용한다. 이는 RestTemplate가 요청을 원격 서버로 발송할 때마다 응답을 대기한다는 것을 의미한다.

기본적으로 RestTemplate은 매번 새 Http 연결을 만들고 응답이 수신되고 처리되면 연결을 닫는다.

URL 연결을 만들고 닫는 작업은 비용이 많이 든다. 프로덕션 클래스 응용 프로그램에서 RestTemplate를 사용하려면 HTTP 연결 풀링을 사용해야 한다.

RestTemplate는 스레드 세이프이며 단일 인스턴스는 언제든지 여러 연결에서 공유할 수 있다.

각 요청에 대해 RestTemplate는 새 스레드를 생성하고 요청-응답 수명 주기에 사용한다. 요청을 보낸 후 RestTemplate는 구성된 시간 초과에 도달할 때까지 서버의 응답을 기다린다. 이 작업은 스레드를 차단한다.

응용프로그램에 많은 요청이 있을 때, 비례하여 높은 수의 스레드와 연결이 있을 것이다. 이렇게 하면 서버 리소스에 부하가 걸린다.

그리고 서버 속도가 느리면 곧 사용자가 애플리케이션의 성능 저하와 응답성이 저하되는 것을 볼 수 있다.

### 2) WebClient 비차단 클라이언트
RestTemplate와 반대로 WebClient는 비동기식 비차단이다. Spring Web Flux의 반응형 프레임워크에서 이벤트 기반 아키텍처를 따른다.

Web Client를 사용하면 클라이언트가 응답이 돌아올 때까지 기다릴 필요가 없습니다. 대신 서버에서 응답이 있을 때 콜백 방법을 사용하여 알림을 받는다.

Mono 또는 Flux를 반환하는 Web Client를 통해 API를 호출하면 즉시 반환된다. 통화 결과는 모노 또는 플럭스 콜백이 가능해지면 당사에 전달된다.

`Block()` 메서드를 사용하면 Web Client에서 동기 처리와 같은 RestTemplate를 얻을 수 있다.

## [출처 및 참고]
* <https://howtodoinjava.com/spring-webflux/webclient-vs-resttemplate/>
* <https://www.baeldung.com/spring-webclient-resttemplate>
