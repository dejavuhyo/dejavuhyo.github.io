---
title: Hibernate Configuration
author: dejavuhyo
date: 2023-01-05 20:40:00 +0900
categories: [DevOps, JPA/Hibernate]
tags: [hibernate, hibernate-architecture, configuration-object, sessionfactory-object, session-object, transaction-object, query-object, criteria-object, 하이버네이트-아키텍처, 하이버네이트]
---

## 1. Configuration
Hibernate에서는 Java 클래스와 데이터베이스 테이블 간의 관계를 정의하는 매핑 정보를 찾을 위치를 미리 알아야 한다. Hibernate를 사용하려면 데이터베이스 및 기타 관련 매개 변수와 관련된 구성 설정 세트도 필요하다. 이러한 모든 정보는 일반적으로 `hibernate.properties`라는 표준 Java 속성 파일 또는 `hibernate.cfg.xml`이라는 XML 파일로 제공된다.

XML 형식의 파일 `hibernate.cfg.xml`을 고려하여 필요한 hibernate 속성을 예제에서 지정한다. 대부분의 속성은 기본값을 사용하므로 실제로 필요한 경우가 아니면 속성 파일에 지정할 필요가 없다. 이 파일은 응용프로그램의 클래스 경로의 루트 디렉터리에 보관된다.

## 2. Hibernate Properties
다음은 중요한 속성 목록이다. `독립 실행형` 상황에서 데이터베이스를 구성해야 한다.

| Properties | Description |
|:-----:|:-----:|
| `hibernate.dialect` | 이 속성은 Hibernate가 선택한 데이터베이스에 적합한 SQL을 생성하도록 함 |
| `hibernate.connection.driver_class` | JDBC 드라이버 클래스 |
| `hibernate.connection.url` | 데이터베이스 인스턴스에 대한 JDBC URL |
| `hibernate.connection.username` | 데이터베이스 사용자 이름 |
| `hibernate.connection.password` | 데이터베이스 암호 |
| `hibernate.connection.pool_size` | Hibernate 데이터베이스 연결 풀에서 대기 중인 연결 수를 제한 |
| `hibernate.connection.autocommit` | JDBC 연결에 자동 커밋 모드를 사용 |

`애플리케이션 서버 및 JNDI`와 함께 데이터베이스를 사용하는 경우 다음 속성을 구성해야 한다.

| Properties | Description |
|:-----:|:-----:|
| `hibernate.connection.datasource` | 응용프로그램에 사용 중인 응용프로그램 서버 컨텍스트에 정의된 JNDI 이름 |
| `hibernate.jndi.class` | JNDI의 InitialContext 클래스 |
| `hibernate.jndi.<JNDIpropertyname>` | 원하는 JNDI property를 JNDI Initial Context로 전달 |
| `hibernate.jndi.url` | JNDI의 URL을 제공 |
| `hibernate.connection.username` | 데이터베이스 사용자 이름 |
| `hibernate.connection.password` | 데이터베이스 암호 |

## 3. MySQL 데이터베이스를 사용한 Hibernate 예제
`hibernate.cfg.xml` 구성 파일을 생성하여 응용 프로그램의 클래스 경로 루트에 배치한다. MySQL 데이터베이스에서 testdb 데이터베이스를 사용할 수 있고 데이터베이스에 액세스할 수 있는 사용자 테스트가 있는지 확인해야 한다.

XML 구성 파일은 [http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd](http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd)에서 제공되는 Hibernate 3 구성 DTD를 준수해야 한다.

```xml
<?xml version = "1.0" encoding = "utf-8"?>
<!DOCTYPE hibernate-configuration SYSTEM
        "http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">
<hibernate-configuration>
    <session-factory>

        <property name="hibernate.dialect">
            org.hibernate.dialect.MySQLDialect
        </property>

        <property name="hibernate.connection.driver_class">
            com.mysql.jdbc.Driver
        </property>

        <!-- Assume test is the database name -->

        <property name="hibernate.connection.url">
            jdbc:mysql://localhost/test
        </property>

        <property name="hibernate.connection.username">
            root
        </property>

        <property name="hibernate.connection.password">
            root123
        </property>

        <!-- List of XML mapping files -->
        <mapping resource="Employee.hbm.xml"/>

    </session-factory>
</hibernate-configuration>
```

데이터베이스 dialect 속성 유형 목록이다.

| Database | Dialect Property |
|:-----:|:-----:|
| `DB2` | org.hibernate.dialect.DB2Dialect |
| `HSQLDB` | org.hibernate.dialect.HSQLDialect |
| `HypersonicSQL` | org.hibernate.dialect.HSQLDialect |
| `Informix` | org.hibernate.dialect.InformixDialect |
| `Ingres` | org.hibernate.dialect.IngresDialect |
| `Interbase` | org.hibernate.dialect.InterbaseDialect |
| `Microsoft SQL Server 2000` | org.hibernate.dialect.SQLServerDialect |
| `Microsoft SQL Server 2005` | org.hibernate.dialect.SQLServer2005Dialect |
| `Microsoft SQL Server 2008` | org.hibernate.dialect.SQLServer2008Dialect |
| `MySQL` | org.hibernate.dialect.MySQLDialect |
| `Oracle (any version)` | org.hibernate.dialect.OracleDialect |
| `Oracle 11g` | org.hibernate.dialect.Oracle10gDialect |
| `Oracle 10g` | org.hibernate.dialect.Oracle10gDialect |
| `Oracle 9i` | org.hibernate.dialect.Oracle9iDialect |
| `PostgreSQL` | org.hibernate.dialect.PostgreSQLDialect |
| `Progress` | org.hibernate.dialect.ProgressDialect |
| `SAP DB` | org.hibernate.dialect.SAPDBDialect |
| `Sybase` | org.hibernate.dialect.SybaseDialect |
| `Sybase Anywhere` | org.hibernate.dialect.SybaseAnywhereDialect |

## [출처 및 참고]
* [https://www.tutorialspoint.com/hibernate/hibernate_configuration.htm](https://www.tutorialspoint.com/hibernate/hibernate_configuration.htm)
