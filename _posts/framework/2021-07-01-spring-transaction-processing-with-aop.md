---
title: Spring AOP를 이용한 Transaction 처리
date: 2021-07-01 06:00:00 +0900
categories: [Application, Framework]
tags: [spring-aop-transaction, aop-transaction, spring-transaction, spring-aop, 스프링-트랜잭션, 스프링-aop, aop-트랜잭션]
---

## 1. AOP를 이용한 Transaction 처리

### 1) xml 설정

* transaction-context.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/aop
       http://www.springframework.org/schema/aop/spring-aop-4.3.xsd
       http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans-4.3.xsd
       http://www.springframework.org/schema/tx
       http://www.springframework.org/schema/tx/spring-tx-4.3.xsd">

    <beans:bean class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
        <beans:property name="location" value="/WEB-INF/config/config.properties"/>
        <beans:property name="fileEncoding" value="UTF-8" />
    </beans:bean>

    <!-- DB connection 설정 (dataSouce) -->
    <bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
        <property name="driverClassName" value="${jdbc.driverClassName}"/>
        <property name="url" value="${jdbc.url}"/>
        <property name="username" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
        <property name="initialSize" value="0"/> 
        <property name="maxActive" value="8"/>
        <property name="maxIdle" value="8"/>
        <property name="minIdle" value="0"/>
        <property name="maxWait" value="-1"/>
    </bean>

    <!-- Transaction 설정하기 -->
    <bean id="txManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"></property>
    </bean>

    <!-- AOP Transaction을 실행하는 대상 Method 설정 -->
    <tx:advice id="txAdvice" transaction-manager="txManager">
        <tx:attributes>
            <tx:method name="insert*" rollback-for="RuntimeException"></tx:method>
            <tx:method name="create*" rollback-for="RuntimeException"></tx:method>
            <tx:method name="update*" rollback-for="RuntimeException"></tx:method>
            <tx:method name="delete*" rollback-for="RuntimeException"></tx:method>
            <tx:method name="select*" read-only="true"></tx:method>
            <tx:method name="get*" read-only="true"></tx:method>
        </tx:attributes>
    </tx:advice>

    <!-- AOP Pointcut/Advisor 설정 -->
    <aop:config>
        <aop:pointcut id="txMethod" expression="execution(* com.example..*Service.*(..))"/>
        <aop:advisor advice-ref="txAdvice" pointcut-ref="txMethod"/>
    </aop:config>

</beans>
```

* config.properties

```properties
jdbc.driverClassName=org.postgresql.Driver
jdbc.url=jdbc:postgresql://localhost:5432/postgres
jdbc.username=postgres
jdbc.password=postgres
```

### 2) 프로퍼티 의미

* __initialSize:__ 연결 풀이 최초 생성될 때 같이 이 숫자만큼 데이터베이스 연결을 미리 생성한다. 기본값은 0이다.

* __maxActive:__ 동시에 사용할 수 있는 최대 연결의 개수이다. 음수이면 제한이 없으며 기본값은 8이다. 이 개수를 초과하여 요청이 들어오면 최대 maxWait 만큼 대기하고, 그래도 연결을 사용할 수 없을 때는 예외를 발생한다.

* __maxIdle:__ 풀에서 사용되지 않은 상태로 존재할 수 있는 최대 연결의 숫자이다. 음수이면 제한이 없으며 기본값은 8이다. maxActive가 10이고, maxIdle이 8일 때 10개의 연결이 모두 사용되고 있는 상태를 가정해 본다. 연결이 사용을 마치고 하나씩 반환이 되면, max Idle 숫자인 8개가 될 때까지는 유휴 상태로 풀에 남겨진다. 아홉 번째부터는 연결이 해제되어 제거된다. 모두 반환된 후에 풀에는 유휴상태인 8개의 연결이 남아 있다.

* __minIdle:__ 풀에서 사용되지 않은 상태로 존재할 수 있는 최소 연결의 숫자이다. 기본값은 0이다. 풀을 관리하는 스레드는 풀 내의 유휴 연결의 최소 개수를 이 값으로 유지한다. 주로 발생하는 상황은 오류가 발생한 연결이나, 사용되지 않고 오래된 연결은 풀에서 제거된다. 이 경우 풀 내의 연결의 개수가 minIdle 이하로 떨어지면 새로 생성해서 minIdle 개수에 맞추게 된다.

* __maxWait:__ 풀에 사용 가능할 연결이 없을 때 대기하는 최대 시간을 밀리 초 단위로 나타낸다. 이 대기시간 후에도 사용 가능한 연결이 없으면 예외를 발생한다. -1은 무한대기를 나타내며 기본값은 -1이다.

## [출처 및 참고]
* <https://goodncuteman.tistory.com/25>
* <https://offbyone.tistory.com/161>
