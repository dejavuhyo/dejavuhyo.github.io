---
title: Spring JNDI
author: dejavuhyo
date: 2021-07-02 06:00:00 +0900
categories: [Framework, Spring]
tags: [spring-jndi, jndi, java-naming-and-directory-interface, tomcat-datasource, 스프링-jndi]
---

## 1. JNDI
JNDI(Java Naming and Directory Interface)는 디렉터리 서비스에서 제공하는 데이터 및 객체를 참고하여 사용할 수 있도록 도와주는 자바 API이다.

## 2. Tomcat 설정

### 1) server.xml

```xml
<GlobalNamingResources>
    <Resource
        auth="Container"
        name="jdbc/postgres"
        type="javax.sql.DataSource"
        factory="org.apache.tomcat.dbcp.dbcp2.BasicDataSourceFactory"
        driverClassName="org.postgresql.Driver"
        url="jdbc:postgresql://localhost:5432/postgres"
        username="postgres"
        password="postgres"
        maxTotal="30"
        maxIdle="30"
        maxWaitMillis="3000"
        minIdle="10"
        testWhileIdle="true"
        validationQuery="select 1"
        closeMethod="close" />
</GlobalNamingResources>
```

> Tomcat 8.0 이상일 경우 org.apache.tomcat.dbcp.dbcp2.BasicDataSourceFactory, 이하일 경우 org.apache.tomcat.dbcp.dbcp.BasicDataSourceFactory로 사용하면 된다.

### 2) context.xml

```xml
<context>
    <ResourceLink name="jdbc/postgres" global="jdbc/postgres" type="javax.sql.DataSource" />
</context>
```

### 3) web.xml

```xml
<resource-ref>
    <description></description>
    <res-ref-name>jdbc/postgres</res-ref-name>
    <res-type>javax.sql.DataSource</res-type>
    <res-auth>Container</res-auth>
</resource-ref>
```

## 3. 사용

```java
import java.sql.Connection;
import java.sql.SQLException;

import javax.naming.Context;
import javax.naming.InitialContext;
import javax.naming.NamingException;
import javax.sql.DataSource;

public class DBConnection {
    public static Connection getConnection() throws SQLException, NamingException {
        Context initCtx = new InitialContext();

        // initCtx의 lookup메서드를 이용해서 'java:comp/env'에 해당하는 객체를 찾아서 context에 삽입
        Context context = (Context) initCtx.lookup("java:comp/env");

        // context의 lookup메서드를 이용해서 'jdbc/postgres'에 해당하는 객체를 찾아서 dataSource에 삽입
        DataSource dataSource = (DataSource) context.lookup("jdbc/postgres");

        // getConnection메서드를 이용해서 커넥션 풀로 부터 커넥션 객체를 얻어내어 conn변수에 저장
        Connection conn = dataSource.getConnection();
        return conn;
    }
}
```

## [출처 및 참고]
* [https://all-record.tistory.com/104](https://all-record.tistory.com/104)
* [https://jieun0113.tistory.com/109](https://jieun0113.tistory.com/109)
