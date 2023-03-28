---
title: Spring Boot JPA Architecture
author: dejavuhyo
date: 2022-12-28 19:40:00 +0900
categories: [DevOps, JPA/Hibernate]
tags: [spring-boot-jpa, jpa-architecture, jpa-relationships, jpa-class, entitymanagerfactory, entitymanager, entity, entitytransaction, persistence, query]
---

## 1. Spring Boot JPA
Java Persistence API는 비즈니스 엔티티를 관계형 엔티티로 저장하는 소스이다. PLAIN OLD JAVA Object(POJO)를 엔티티로 정의하는 방법과 관계가 있는 엔티티를 관리하는 방법을 보여준다.

## 2. Class Level Architecture
다음 이미지는 JPA의 클래스 수준 아키텍처이다. JPA의 핵심 클래스와 인터페이스를 보여준다.

![architecture](/assets/img/2022-12-28-spring-boot-jpa-architecture/architecture.png)

다음 표에서는 위의 아키텍처에 표시된 각 단위를 설명한다.

| Units | Description |
|:-----:|:-----:|
| EntityManagerFactory | EntityManager의 factory 클래스이다. 여러 EntityManager 인스턴스를 생성하고 관리한다. |
| EntityManager | 이것은 인터페이스이며, 객체에 대한 지속성 작업을 관리한다. Query 인스턴스를 위한 factory처럼 작동한다. |
| Entity | 엔티티는 지속성 개체이며 데이터베이스에 레코드로 저장된다. |
| EntityTransaction | 그것은 EntityManager와 일대일 관계를 맺고 있다. 각 EntityManager에 대해 작업은 EntityTransaction 클래스에 의해 유지된다. |
| Persistence | 이 클래스에는 EntityManagerFactory 인스턴스를 가져오는 정적 메서드가 포함되어 있다. |
| Query | 이 인터페이스는 기준을 충족하는 관계형 객체를 얻기 위해 각 JPA 공급업체에 의해 구현된다. |

위의 클래스 및 인터페이스는 엔티티를 레코드로 데이터베이스에 저장하는데 사용된다.

데이터베이스에 데이터를 저장하기 위한 코드를 작성하는 노력을 줄임으로써 프로그래머들이 클래스를 데이터베이스 테이블과 매핑하기 위한 코드 작성과 같은 더 중요한 활동에 집중할 수 있도록 도와준다.

## 3. JPA Class Relationships
위의 아키텍처에서 클래스와 인터페이스 사이의 관계는 javax.persistence 패키지에 속한다. 다음 다이어그램은 이들 사이의 관계를 보여준다.

relationships.png

* EntityManagerFactory와 EntityManager의 관계는 일대일 관계이다. EntityManager 인스턴스의 팩토리 클래스이
다.

* EntityManager와 EntityTransaction의 관계는 일대일 관계이다. 각 EntityManager 작업에 대해 EntityTransaction 인스턴스가 있다.

* EntityManager와 Query의 관계는 일대일이다. 하나의 EntityManager 인스턴스를 사용하여 여러 쿼리를 실행할 수 있다.

* EntityManager와 Entity의 관계는 일대일 관계이다. 하나의 EntityManager 인스턴스가 여러 엔티티를 관리할 수 있다.

## [출처 및 참고]
* [https://www.tutorialspoint.com/spring_boot_jpa/spring_boot_jpa_architecture.htm](https://www.tutorialspoint.com/spring_boot_jpa/spring_boot_jpa_architecture.htm)
