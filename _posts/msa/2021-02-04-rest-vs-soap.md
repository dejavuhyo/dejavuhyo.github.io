---
title: REST와 SOAP 비교
author: dejavuhyo
date: 2021-02-04 06:00:00 +0900
categories: [DevOps, MSA]
tags: [rest-vs-soap, rest, soap, 데이터-전송, 데이터-전송-방식]
---

## 1. 데이터 전송 방식
REST와 SOAP는 각기 다른 두 가지의 온라인 데이터 전송 방식이다. 둘 다 웹 애플리케이션 간 데이터 통신을 허용하는 애플리케이션 프로그래밍 인터페이스(Application Programming Interface, API)를 구축하는 방법을 정의한다.

REST(Representational State Transfer)는 아키텍처 원칙 세트이고, SOAP(Simple Object Access Protocol)는 World Wide Web Consortium(W3C)에서 유지관리하는 공식 프로토콜이다.

즉, SOAP는 프로토콜이지만, REST는 프로토콜이 아니라는 점이 주요 차이점이다. 일반적으로 API는 활용 사례와 개발자의 선호에 따라 REST 또는 SOAP 중 하나를 준수한다.

## 2. REST(Representational State Transfer)

![img001](/assets/img/2021-02-04-rest-vs-soap/img001.png)

REST는 웹 서비스와 모바일 애플리케이션 경량화의 필요에 맞춘 아키텍처 원칙 세트이다. 가이드라인이기 때문에 이러한 권장 사항의 구현 여부는 개발자에게 달려 있다.

데이터 요청이 REST API로 전송될 때는 일반적으로 하이퍼텍스트 전송 프로토콜(HTTP)을 통해 이루어진다. 이러한 요청을 수신하면 REST용으로 설계된 API(RESTful API 또는 RESTful 웹 서비스)가 HTML, XML, 일반 텍스트, JSON과 같은 다양한 형식으로 메시지를 반환할 수 있다. JSON(JavaScript Object Notation)은 이름과는 달리 어떠한 프로그래밍 언어로든 읽을 수 있고, 인간과 기계가 모두 읽을 수 있으며, 경량화되어 있기 때문에 선호되는 메시지 형식이다. 이러한 이유로 RESTful API는 보다 유연하고 설정하기도 쉽다.

다음 6가지 아키텍처 가이드라인을 준수한 애플리케이션이 RESTful에 속한다. RESTful 애플리케이션이 갖춰야 할 요소는 다음과 같다.> 

* 클라이언트, 서버, 리소스로 구성된 클라이언트-서버 아키텍처가 필요하다.

* 요청이 통과하는 서버에 클라이언트 콘텐츠가 저장되지 않는 스테이트리스(stateless) 클라이언트-서버 커뮤니케이션이 필요하다. 대신 세션의 상태에 대한 정보가 클라이언트에 저장된다.

* 일부 클라이언트-서버 간 상호 작용의 필요성을 제거할 캐시 가능 데이터가 필요하다.

* 애플리케이션 요구 사항별로 다른 형식이 아닌, 표준화된 형식으로 정보를 전송할 수 있도록 구성 요소 간 통합된 인터페이스가 필요하다. REST를 처음으로 제시한 Roy Fielding은 이를 "REST 아키텍처 스타일을 다른 네트워크 기반 스타일과 차별화하는 핵심적인 기능"이라고 설명한다.

* 클라이언트-서버 간의 상호 작용을 계층적으로 조정할 수 있도록 계층화된 시스템 제약이 필요하다.

* 실행 가능한 코드를 전송해 서버가 클라이언트의 기능을 확장할 수 있게 해주는 코드 온디맨드가 필요하다. 단, 가시성이 감소할 수 있으므로 이는 선택적 가이드라인이다.

## 3. SOAP(Simple Object Access Protocol)

![img002](/assets/img/2021-02-04-rest-vs-soap/img002.png)

SOAP는 다른 언어로 다른 플랫폼에서 빌드된 애플리케이션이 통신할 수 있도록 설계된 최초의 표준 프로토콜이다. 프로토콜이기 때문에 복잡성과 오버헤드를 증가시키는 빌트인 룰을 적용하므로, 페이지 로드 시간이 길어질 수 있다.

그러나 이러한 표준은 빌트인 컴플라이언스를 제공한다는 의미이므로, 기업에서 선호하는 방식이기도 하다. 빌트인 컴플라이언스 표준에는 보안과 안정적인 데이터베이스 트랜잭션의 기본 속성인 원자성, 일관성, 격리성, 내구성(Atomicity, Consistency, Isolation and Durability, ACID)이 포함된4다.

일반적인 웹 서비스 사양에는 다음이 포함된다.

* **웹 서비스 보안(WS-security):** 토큰이라고 불리는 고유 식별자를 통해 메시지를 보호하고 전송하는 방식을 표준화한다.

* **WS-ReliableMessaging:** 불안정한 IT 인프라로 전송되는 메시지 간 오류 처리를 표준화한다.

* **웹 서비스 주소지정(WS-addressing):** 심층 네트워크에 라우팅 정보를 유지관리하는 대신, SOAP 헤더 내에 메타데이터로 해당 정보를 패키징한다.

* **웹 서비스 기술 언어(WSDL):** 웹 서비스가 무엇을 하는지, 해당 서비스의 시작과 종료 위치를 기술한다.

데이터에 대한 요청이 SOAP API로 전송되는 경우 HTTP(웹 브라우저), SMTP(이메일), TCP 등의 다양한 애플리케이션 레이어 프로토콜을 통해 처리될 수 있다.

그러나 요청이 수신되면, 인간과 기계가 모두 읽을 수 있는 마크업 언어인 XML 문서 형식으로 반환 SOAP 메시지가 반환된다. SOAP API에 대한 완료된 요청은 브라우저에서 캐시할 수 없으므로, API로 재전송하지 않는 한 이후에 액세스할 수 없다.

## 4. SOAP과 REST 차이
대부분의 레거시 시스템에서 SOAP를 준수하며, REST는 그보다 뒤에 고려하거나 웹 기반 시나리오에서의 더 빠른 대안으로 여기는 경우가 많다. REST는 유연한 구현을 제공하는 가이드라인 세트고, SOAP는 XML 메시징과 같은 특정 요건이 있는 프로토콜이다.

REST API는 경량화되어 있기 때문에 사물 인터넷(IoT), 모바일 애플리케이션 개발, 서버리스(servreless) 컴퓨팅과 같이 보다 새로운 컨텍스트에 이상적이다. SOAP 웹 서비스는 많은 기업에서 필요로 하는 기본 보안과 트랜잭션 컴플라이언스를 제공하지만, 이로 인해 좀 더 무거운 경향이 있다. 또한 Google Maps API와 같은 대부분의 퍼블릭 API는 REST 가이드라인을 따른다.

## [출처 및 참고]
* [https://www.redhat.com/ko/topics/integration/whats-the-difference-between-soap-rest](https://www.redhat.com/ko/topics/integration/whats-the-difference-between-soap-rest)
* [https://ettrends.etri.re.kr/ettrends/122/0905001533/25-2_112_120.pdf](https://ettrends.etri.re.kr/ettrends/122/0905001533/25-2_112_120.pdf)
