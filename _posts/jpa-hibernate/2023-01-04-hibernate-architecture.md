---
title: Hibernate Architecture
author: dejavuhyo
date: 2023-01-04 22:00:00 +0900
categories: [DevOps, JPA/Hibernate]
tags: [hibernate, hibernate-architecture, configuration-object, sessionfactory-object, session-object, transaction-object, query-object, criteria-object, 하이버네이트-아키텍처, 하이버네이트]
---

hibernate-architecture
이미지, 날짜 확인

## 1. Architecture
Hibernate는 사용자가 기본 API를 알지 않아도 작동할 수 있도록 도와주는 계층 구조를 가지고 있다.

Hibernate는 데이터베이스 및 구성 데이터를 사용하여 애플리케이션에 지속성 서비스(및 영구 개체)를 제공한다.

다음은 Hibernate 응용 프로그램 아키텍처이다.

![hibernate-high-level](/assets/img/2023-01-04-hibernate-architecture/hibernate-high-level.png)

다음은 핵심 클래스가 포함된 Hibernate 응용 프로그램 아키텍처이다.

![hibernate-architecture](/assets/img/2023-01-04-hibernate-architecture/hibernate-architecture.png)

Hibernate는 JDBC, 자바 트랜잭션 API(JTA), 자바 네이밍 및 디렉토리 인터페이스(JNDI)와 같은 기존의 다양한 자바 API를 사용한다.

JDBC는 관계형 데이터베이스에 공통적인 기능의 기본적인 수준의 추상화를 제공하여 JDBC 드라이버가 있는 거의 모든 데이터베이스를 Hibernate에서 지원할 수 있게 한다.

JNDI 및 JTA를 사용하면 Hibernate를 J2EE 응용 프로그램 서버와 통합할 수 있다.

## 2. Configuration Object
구성 개체는 Hibernate 응용 프로그램에서 생성하는 첫 번째 최대 절전 모드 개체이다. 일반적으로 응용 프로그램 초기화 중에 한 번만 생성된다. Hibernate에 필요한 구성 또는 속성 파일을 나타낸다. 구성 개체는 두 가지 주요 구성 요소를 제공한다.

* Database Connection: 이 작업은 Hibernate에서 지원하는 하나 이상의 구성 파일을 통해 처리된다. 이러한 파일은 `hibernate.properties` 및 `hibernate.cfg.xml`이다.

* Class Mapping Setup: 이 구성 요소는 Java 클래스와 데이터베이스 테이블 간의 연결을 생성한다.

## 3. SessionFactory Object
Configuration object는 SessionFactory 개체를 만드는데 사용되며, SessionFactory 개체는 제공된 구성 파일을 사용하여 응용 프로그램의 최대 절전 모드를 구성하고 Session 개체를 인스턴스화할 수 있다. SessionFactory는 thread safe object이며 응용프로그램의 모든 스레드에서 사용된다.

SessionFactory는 일반적으로 응용 프로그램 시작 중에 생성되어 나중에 사용할 수 있도록 보관된다. 별도의 구성 파일을 사용하여 데이터베이스당 하나의 SessionFactory 개체가 필요하다. 따라서 여러 데이터베이스를 사용하는 경우 여러 SessionFactory 개체를 생성해야 한다.

## 4. Session Object
세션은 데이터베이스와 물리적으로 연결하는데 사용된다. 세션 개체는 가볍고 데이터베이스와의 상호 작용이 필요할 때마다 인스턴스화되도록 설계되었다. 영구 개체는 세션 개체를 통해 저장 및 검색된다.

세션 개체는 일반적으로 스레드 세이프가 아니며 필요에 따라 생성 및 삭제해야 하므로 세션 개체를 오랫동안 열어 두면 안 된다.

## 5. Transaction Object
트랜잭션은 데이터베이스 작업 단위를 나타내며 대부분의 RDBMS는 트랜잭션 기능을 지원한다. 최대 절전 모드의 트랜잭션은 기본 트랜잭션 관리자 및 트랜잭션(JDBC 또는 JTA)에 의해 처리된다.

이것은 선택적 개체이며 Hibernate 응용 프로그램은 자체 응용 프로그램 코드로 트랜잭션을 관리하는 대신 이 인터페이스를 사용하지 않도록 선택할 수 있다.

## 6. Query Object
쿼리 개체는 SQL 또는 HQL(Hibernate Query Language) 문자열을 사용하여 데이터베이스에서 데이터를 검색하고 개체를 만든다. 쿼리 인스턴스는 쿼리 매개 변수를 바인딩하고 쿼리에 의해 반환되는 결과 수를 제한하며 마지막으로 쿼리를 실행하는데 사용된다.

## 7. Criteria Object
기준 개체는 개체를 검색하기 위해 개체 지향 기준 쿼리를 만들고 실행하는데 사용된다.

## [출처 및 참고]
* [https://www.tutorialspoint.com/hibernate/hibernate_architecture.htm](https://www.tutorialspoint.com/hibernate/hibernate_architecture.htm)
