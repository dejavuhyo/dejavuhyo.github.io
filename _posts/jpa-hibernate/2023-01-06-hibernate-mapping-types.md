---
title: Hibernate Mapping Types
author: dejavuhyo
date: 2023-01-06 21:50:00 +0900
categories: [Framework, JPA/Hibernate]
tags: [hibernate, hibernate-mapping-types, mapping-types, hibernate-mapping, primitive-types, date-time-types, binary-large-object-types, jdk-related-types, 하이버네이트-매핑-타입, 매핑-타입]
---

## 1. Mapping Types
Hibernate 매핑 문서를 준비할 때 Java 데이터 유형을 RDBMS 데이터 유형에 매핑한다. 매핑 파일에 선언되고 사용되는 유형은 Java 데이터 유형이 아니며 SQL 데이터베이스 유형도 아니다. 이러한 유형을 Hibernate 매핑 유형이라고 하며, Java에서 SQL 데이터 유형으로 변환하거나 그 반대로 변환할 수 있다.

## 2. Primitive Types

| Mapping type | Java type | ANSI SQL Type |
|:-----:|:-----:|:-----:|
| integer | int or java.lang.Integer | INTEGER |
| long | long or java.lang.Long | BIGINT |
| short | short or java.lang.Short | SMALLINT |
| float | float or java.lang.Float | FLOAT |
| double | double or java.lang.Double | DOUBLE |
| big_decimal | java.math.BigDecimal | NUMERIC |
| character | java.lang.String | CHAR(1) |
| string | java.lang.String | VARCHAR |
| byte | byte or java.lang.Byte | TINYINT |
| boolean | boolean or java.lang.Boolean | BIT |
| yes/no | boolean or java.lang.Boolean | CHAR(1) ('Y' or 'N') |
| true/false | boolean or java.lang.Boolean | CHAR(1) ('T' or 'F') |

## 3. Date and Time Types

| Mapping type | Java type | ANSI SQL Type |
|:-----:|:-----:|:-----:|
| date | java.util.Date or java.sql.Date | DATE |
| time | java.util.Date or java.sql.Time | TIME |
| timestamp | java.util.Date or java.sql.Timestamp | TIMESTAMP |
| calendar | java.util.Calendar | TIMESTAMP |
| calendar_date | java.util.Calendar | DATE |

## 4. Binary and Large Object Types

| Mapping type | Java type | ANSI SQL Type |
|:-----:|:-----:|:-----:|
| binary | byte[] | VARBINARY (or BLOB) |
| text | java.lang.String | CLOB |
| serializable | any Java class that implements java.io.Serializable | VARBINARY (or BLOB) |
| clob | java.sql.Clob | CLOB |
| blob | java.sql.Blob | BLOB |

## 5. JDK-related Types

| Mapping type | Java type | ANSI SQL Type |
|:-----:|:-----:|:-----:|
| class | java.lang.Class | VARCHAR |
| locale | java.util.Locale | VARCHAR |
| timezone | java.util.TimeZone | VARCHAR |
| currency | java.util.Currency | VARCHAR |

## [출처 및 참고]
* [https://www.tutorialspoint.com/hibernate/hibernate_mapping_types.htm](https://www.tutorialspoint.com/hibernate/hibernate_mapping_types.htm)
