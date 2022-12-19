---
title: JPA vs Hibernate
author: dejavuhyo
date: 2022-12-19 20:20:00 +0900
categories: [DevOps, JPA/Hibernate]
tags: [jpa-vs-hibernate, spring-boot-jpa, hibernate, jpa-hibernate]
---

## 1. JPA
JPA는 자바 객체와 관계형 데이터베이스 사이의 정보/데이터에 접근, 관리, 유지하는 방법을 지정하는 규격이다. ORM, 객체 관계 매핑에 대한 표준 접근 방식을 제공한다.

## 2. Hibernate
Hibernate는 JPA의 구현입니다. 경량 프레임워크를 제공하며 가장 많이 사용되는 ORM 도구 중 하나이다.

## 3. JPA vs Hibernate

| Category | JPA | Hibernate |
|:-----:|:-----:|:-----:|
| Type | JPA는 자바 객체를 사용하여 관계형 데이터베이스 데이터를 관리하는 방법을 정의하는 규격이다. | Hibernate는 JPA의 구현이다. 자바 객체를 관계형 데이터베이스에 유지하기 위한 ORM 도구이다. |
| Package | JPA는 `javax.persistence` 패키지를 사용한다. | Hibernate는 `org.hibernate` 패키지를 사용한다. |
| Factory | JPA는 EntityManagerFactory 인터페이스를 사용하여 엔티티 관리자가 개체를 유지하도록 한다. | Hibernate는 세션 팩토리 인터페이스를 사용하여 세션 개체를 만든 다음 개체를 유지하는 데 사용된다. |
| CRUD Operations | JPA는 EntityManager 인터페이스를 사용하여 작업을 생성/읽기/삭제하고 지속성 컨텍스트를 유지한다. | Hibernate는 세션 인터페이스를 사용하여 작업을 생성/읽기/삭제하고 지속성 컨텍스트를 유지한다. |
| Language | JPA는 데이터베이스 작업을 위한 객체 지향 쿼리 언어로 JPQL(Java Persistence Query Language)을 사용한다. | Hibernate는 데이터베이스 작업을 위해 HQL(Hibernate Query Language)을 객체 지향 쿼리 언어로 사용한다. |

## [출처 및 참고]
* <https://www.tutorialspoint.com/spring_boot_jpa/spring_boot_jpa_hibernate.htm>
