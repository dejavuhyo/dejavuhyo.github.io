---
title: Hibernate
author: dejavuhyo
date: 2022-12-22 21:30:00 +0900
categories: [Framework, JPA/Hibernate]
tags: [hibernate, hibernate-advantages, hibernate-supported-databases, orm, object-relational-mapping]
---

## 1. Hibernate
Hibernate는 JAVA용 Object-Relational Mapping (ORM) 솔루션이다. 2001년 Gavin King이 만든 오픈 소스 영구 프레임워크다. 이것은 모든 Java 애플리케이션을 위한 강력한 고성능 객체 관계 지속성 및 쿼리 서비스이다.

Hibernate는 자바 클래스를 데이터베이스 테이블에 매핑하고 자바 데이터 유형에서 SQL 데이터 유형으로 매핑하며 개발자에게 일반적인 데이터 지속성 관련 프로그래밍 작업의 95%를 덜어준다.

Hibernate는 기존 Java 객체와 데이터베이스 서버 사이에 위치하여 적절한 O/R 메커니즘과 패턴을 기반으로 객체를 유지하는 모든 작업을 처리한다.

![hibernate](/assets/img/2022-12-22-hibernate/hibernate.png)

## 2. Hibernate 장점

* Hibernate는 코드를 작성하지 않고 XML 파일을 사용하여 Java 클래스를 데이터베이스 테이블에 매핑한다.

* 데이터베이스에서 직접 Java 개체를 저장하고 검색하기 위한 간단한 API를 제공한다.

* 데이터베이스나 테이블에 변경 사항이 있으면 XML 파일 속성만 변경하면 된다.

* 익숙하지 않은 SQL 유형을 추상화하고 친숙한 Java 개체를 해결하는 방법을 제공한다.

* Hibernate는 애플리케이션 서버가 작동할 필요가 없다.

* 데이터베이스 개체의 복잡한 연결을 조작한다.

* 스마트 가져오기 전략으로 데이터베이스 액세스를 최소화한다.

* 간단한 데이터 쿼리를 제공한다.

## 3. 지원되는 데이터베이스
Hibernate는 거의 모든 주요 RDBMS를 지원한다. 다음은 Hibernate가 지원하는 몇 가지 데이터베이스 엔진 목록이다.

* HSQL Database Engine

* DB2/NT

* MySQL

* PostgreSQL

* FrontBase

* Oracle

* Microsoft SQL Server Database

* Sybase SQL Server

* Informix Dynamic Server

## 4. 지원되는 기술
Hibernate는 다음과 같은 다양한 다른 기술을 지원한다.

* XDoclet Spring

* J2EE

* Eclipse plug-ins

* Maven

## [출처 및 참고]
* [https://www.tutorialspoint.com/hibernate/hibernate_overview.htm](https://www.tutorialspoint.com/hibernate/hibernate_overview.htm)
