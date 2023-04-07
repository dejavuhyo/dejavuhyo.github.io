---
title: Hibernate Sessions
author: dejavuhyo
date: 2023-01-12 18:20:00 +0900
categories: [Framework, JPA/Hibernate]
tags: [hibernate, hibernate-architecture, configuration-object, sessionfactory-object, session-object, transaction-object, query-object, criteria-object, 하이버네이트-아키텍처, 하이버네이트]
---

## 1. Sessions
세션은 데이터베이스와 물리적으로 연결하는데 사용된다. 세션 개체는 가볍고 데이터베이스와의 상호 작용이 필요할 때마다 인스턴스화되도록 설계되었다. 영구 개체는 세션 개체를 통해 저장 및 검색된다.

세션 개체는 일반적으로 스레드 세이프가 아니며 필요에 따라 생성 및 삭제해야 하므로 세션 개체를 오랫동안 열어 두면 안 된다. 세션의 주요 기능은 매핑된 엔티티 클래스의 인스턴스에 대한 작업을 제공, 생성, 읽기 및 삭제하는 것이다.

인스턴스는 특정 시점에 다음 세 가지 상태 중 하나로 존재할 수 있다.

* transient: 세션과 연결되지 않고 데이터베이스에 표시되지 않으며 식별자 값도 없는 영구 클래스의 새 인스턴스는 Hibernate에서 일시적인 것으로 간주된다.

* persistent: 임시 인스턴스를 세션에 연결하여 영구적으로 만들 수 있다. 영구 인스턴스는 데이터베이스에 표현, 식별자 값을 가지며 세션과 연결된다.

* detached: 최대 절전 모드 세션을 닫으면 영구 인스턴스가 분리된 인스턴스가 된다.

세션 인스턴스는 해당 영구 클래스가 직렬화 가능한 경우 직렬화 가능하다. 일반적인 트랜잭션은 다음 관용구를 사용해야 한다.

```java
Session session = factory.openSession();
Transaction tx = null;

try {
   tx = session.beginTransaction();
   // do some work
   ...
   tx.commit();
}

catch (Exception e) {
   if (tx!=null) tx.rollback();
   e.printStackTrace();
} finally {
   session.close();
}
```

세션에서 예외가 발생하면 트랜잭션을 롤백하고 세션을 삭제해야 한다.

## 2. 세션 인터페이스 방법
세션 인터페이스에서 제공하는 방법은 여러 가지가 있지만, 몇 가지 중요한 방법만 나열한다. Hibernate 설명서에서 세션 및 세션 팩토리와 관련된 전체 방법 목록을 확인할 수 있다.

| Session Methods | Description |
|:-----:|:-----:|
| `Transaction beginTransaction()` | 작업 단위를 시작하고 연결된 트랜잭션 개체를 반환한다. |
| `void cancelQuery()` | 현재 쿼리 실행 취소한다. |
| `void clear()` | 세션을 완전히 지운다. |
| `Connection close()` | JDBC 연결을 해제하고 정리하여 세션을 종료한다. |
| `Criteria createCriteria(Class persistentClass)` | 지정된 엔티티 클래스 또는 엔티티 클래스의 수퍼 클래스에 대해 새 기준 인스턴스를 만든다. |
| `Criteria createCriteria(String entityName)` | 지정된 엔티티 이름에 대해 새 기준 인스턴스를 만든다. |
| `Serializable getIdentifier(Object object)` | 이 세션과 연결된 지정된 엔티티의 식별자 값을 반환한다. |
| `Query createFilter(Object collection, String queryString)` | 지정된 컬렉션 및 필터 문자열에 대한 쿼리의 새 인스턴스를 만든다. |
| `Query createQuery(String queryString)` | 지정된 HQL 쿼리 문자열에 대한 쿼리의 새 인스턴스를 만든다. |
| `SQLQuery createSQLQuery(String queryString)` | 지정된 SQL 쿼리 문자열에 대해 SQLQuery의 새 인스턴스를 만든다. |
| `void delete(Object object)` | 데이터스토어에서 영구 인스턴스를 제거한다. |
| `void delete(String entityName, Object object)` | 데이터스토어에서 영구 인스턴스를 제거한다. |
| `Session get(String entityName, Serializable id)` | 지정된 명명된 엔티티의 영구 인스턴스를 지정된 식별자와 함께 반환하거나, 해당 영구 인스턴스가 없으면 null을 반환한다. |
| `SessionFactory getSessionFactory()` | 이 세션을 만든 세션 팩토리를 가져온다. |
| `void refresh(Object object)` | 기본 데이터베이스에서 지정된 인스턴스의 상태를 다시 읽는다. |
| `Transaction getTransaction()` | 이 세션과 연결된 트랜잭션 인스턴스를 가져온다. |
| `boolean isConnected()` | 세션이 현재 연결되어 있는지 확인한다. |
| `boolean isDirty()` | 이 세션에 데이터베이스와 동기화해야 하는 변경 사항이 있는가? |
| `boolean isOpen()` | 세션이 아직 열려 있는지 확인한다. |
| `Serializable save(Object object)` | 지정된 일시적 인스턴스를 유지하고 먼저 생성된 식별자를 할당한다. |
| `void saveOrUpdate(Object object)` | 지정된 인스턴스를 저장(개체)하거나 업데이트(개체)한다. |
| `void update(Object object)` | 영구 인스턴스를 지정된 분리된 인스턴스의 식별자로 업데이트한다. |
| `void update(String entityName, Object object)` | 영구 인스턴스를 지정된 분리된 인스턴스의 식별자로 업데이트한다. |

## [출처 및 참고]
* [https://www.tutorialspoint.com/hibernate/hibernate_sessions.htm](https://www.tutorialspoint.com/hibernate/hibernate_sessions.htm)
