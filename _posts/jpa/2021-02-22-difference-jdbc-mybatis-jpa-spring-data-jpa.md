---
title: JDBC, Mybatis, JPA, Spring Data JPA 차이점
author: dejavuhyo
date: 2021-02-22 06:00:00 +0900
categories: [DevOps, JPA]
tags: [jdbc, mybatis, jpa-hibernate, spring-data-jpa]
---

## 1. JDBC(Java Database Connectivity)
RDBMS에 액세스 하는 경우, 애플리케이션에서는 JDBC 데이터 소스를 참조해 액세스 하게 된다. JDBC 데이터 소스를 사용함으로써 JDBC 드라이버의 로드 접속 정보(접속 URL, 접속 사용자, 패스워드 등)의 설정을 애플리케이션에서 배제할 수 있다. 그 때문에 애플리케이션에서는 사용하는 RDBMS나 배포 환경을 의식할 필요가 없어진다.

* About JDBC DataSource

![common-datasource](/assets/img/2021-02-22-difference-jdbc-mybatis-jpa-spring-data-jpa/common-datasource.png)

JDBC 데이터 소스의 구현은 애플리케이션 서버, OSS 라이브러리 Third-Party 라이브러리, Spring Framework 등이 제공되고 있기 때문에 프로젝트 요구 사항 및 배포 환경에 맞는 데이터 소스의 선정이 필요하다. 대표적인 데이터 소스 3가지를 소개한다.

### 1) Application Server에서 제공하는 JDBC 데이터 소스 

| 애플리케이션 서버 | 참조 페이지 |
|:-----:|:-----:|
| Apache Tomcat 9.0 | [Apache Tomcat 9.0 User Guide (The Tomcat JDBC Connection Pool)](http://tomcat.apache.org/tomcat-9.0-doc/jdbc-pool.html), [Apache Tomcat 9.0 User Guide (JNDI Datasource HOW-TO)](http://tomcat.apache.org/tomcat-9.0-doc/jndi-datasource-examples-howto.html) |
| Apache Tomcat 8.5 | [Apache Tomcat 8.5 User Guide (The Tomcat JDBC Connection Pool)](http://tomcat.apache.org/tomcat-8.5-doc/jdbc-pool.html), [Apache Tomcat 8.5 User Guide (JNDI Datasource HOW-TO)](http://tomcat.apache.org/tomcat-8.5-doc/jndi-datasource-examples-howto.html) | 
| Apache Tomcat 7 | [Apache Tomcat 7 User Guide (The Tomcat JDBC Connection Pool)](http://tomcat.apache.org/tomcat-7.0-doc/jdbc-pool.html), [Apache Tomcat 7 User Guide (JNDI Datasource HOW-TO)](http://tomcat.apache.org/tomcat-7.0-doc/jndi-datasource-examples-howto.html) |
| Oracle WebLogic Server 12c | [Oracle WebLogic Server 12.2.1.4 Documentation](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/intro/jdbc.html#GUID-9FD5F552-B2E4-4FEC-8C10-503A08764B52) |
| IBM WebSphere Application Server Version 9.0 | [ WebSphere Application Server Online information center](https://www.ibm.com/support/knowledgecenter/ko/SSEQTP_9.0.5/com.ibm.websphere.wlp.doc/ae/twlp_dep_configuring_ds.html) |
| JBoss Enterprise Application Platform 7.2 | [JBoss Enterprise Application Platform 7.2 Product Documentation](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/datasource_management) |

### 2) OSS/Third-Party 라이브러리 제공 JDBC 데이터 소스
애플리케이션 서버에서 제공하는 JDBC 데이터 소스를 사용하지 않을 경우, OSS/Third-Party 라이브러리에서 제공되는 JDBC 데이터 소스를 사용한다. 'Apache Commons DBCP'만 소개하지만 다른 라이브러리를 사용해도 된다.

* OSS/Third-Party 라이브러리에서 제공되는 JDBC 데이터 소스

| 애플리케이션 서버 | 참조 페이지 |
|:-----:|:-----:|
| Apache Commons DBCP | [Apache Commons DBCP](http://commons.apache.org/proper/commons-dbcp/index.html) |

### 3) Spring Framework에서 제공하는 JDBC 데이터 소스
Spring Framework에서 제공되는 JDBC 데이터 소스 구현 클래스는 connection 풀링 기능이 없기 때문에 웹 애플리케이션의 데이터 소스로 사용하는 것은 아니다.

Spring Framework는 JDBC 데이터 소스 구현 클래스와 JDBC 데이터 소스의 어댑터 클래스를 제공하고 있지만, 사용하는 경우가 제한적이므로 Appendix의 Spring Framework에서 제공되는 JDBC 데이터 소스 클래스로 소개한다.

* 트랜잭션 관리 방법
  - Spring Framework의 기능을 사용하여 트랜잭션 관리를하는 경우, 프로젝트 요구 사항 및 배포 환경에 맞는 PlatformTransactionManager의 선정이 필요하다.
  - [트랜잭션 관리 사용 설정 참고](https://terasolunaorg.github.io/guideline/5.6.1.RELEASE/ja/ImplementationAtEachLayer/DomainLayer.html#service-enable-transaction-management)

* 트랜잭션 경계/속성 선언
  - 트랜잭션 경계 및 트랜잭션 속성의 선언은 Service에서 @Transactional 주석을 지정하여 수행한다.
  - [트랜잭션 관리 참고](https://terasolunaorg.github.io/guideline/5.6.1.RELEASE/ja/ImplementationAtEachLayer/DomainLayer.html#service-transaction-management)

* 데이터의 배타 제어
  - 데이터를 업데이트할 때 데이터 일관성 및 무결성을 보장하기 위해 배타 제어를 할 필요가 있다.
  - [베타 제어 참고](https://terasolunaorg.github.io/guideline/5.6.1.RELEASE/ja/ArchitectureInDetail/DataAccessDetail/ExclusionControl.html)

* 예외 처리
  - Spring Framework는 JDBC Exception(java.sql.SQLException)이나 O/R Mapper 특정 예외를 Spring Framework에서 제공하는 데이터 액세스 Exception(org.springframework.dao.DataAccessException 서브)으로 변환하는 기능이 있다.
  - [Spring Framework에서 제공되는 데이터 액세스 Exception 변환 클래스 참고](https://terasolunaorg.github.io/guideline/5.6.1.RELEASE/ja/ArchitectureInDetail/DataAccessDetail/DataAccessCommon.html#appendix-dataaccessexception-converter-class-label)

변환된 데이터 접근 예외는 기본적으로 애플리케이션 코드에서 처리할 필요는 없지만, 일부 오류(고유 제약 조건 위반, 독점적 오류 등) 내용은 요구 사항에 따라 처리 할 필요가 있다.

데이터 액세스 예외를 처리하는 경우 DataAccessException을 catch 하는 것이 아니라 오류 내용을 통지하는 서브 클래스의 예외를 catch 한다.

* 처리할 수 있는 DB의 액세스 Exception 서브 클래스

| 클래스 이름 | 설명 |
|:-----:|:-----:|
| org.springframework.dao.DuplicateKeyException | 고유 제약 조건 위반이 발생했을 경우에 발생하는 예외 |
| org.springframework.dao.OptimisticLockingFailureException | 낙관적 잠금에 성공하지 못한 경우에 발생하는 예외. 다른 프로세스에서 동일한 데이터가 업데이트된 경우에 발생한다. 이 예외는 O/R Mapper로 JPA를 사용하는 경우 발생하는 예외이다. MyBatis는 낙관적 lock을 하는 기능이 없기 때문에 O/R Mapper 본체에서 본 예외가 발생하는 것은 아니다. |
| org.springframework.dao.PessimisticLockingFailureException | 비관적 잠금에 성공하지 못한 경우에 발생하는 예외. 다른 처리에 동일한 데이터가 잠겨 있고 잠금 해제 대기 제한 시간을 초과해도 잠금이 해제되지 않는 경우에 발생한다. |

## 2. MyBatis
MyBatis3는 O/R Mapper로 데이터베이스에서 저장된 레코드와 오브젝트를 매핑 하는 개념이 아니라, SQL과 오브젝트를 매핑으로 개발된 O/R Mapper이다.

MyBatis3로 부터 추가된 Mapper 인터페이스를 사용하여 Entity의 CRUD 조작을 실시한다. Mapper 인터페이스의 자세한 내용은 [Mapper 인터페이스 구조](https://mybatis.org/mybatis-3/ko/index.html) 참조

* MyBatis3 Scope

![mybatis3-scope](/assets/img/2021-02-22-difference-jdbc-mybatis-jpa-spring-data-jpa/mybatis3-scope.png)

### 1) MyBatis3 구성 요소
My Batis 설정 파일을 읽고, Sql Session Factory 를 생성하기 위한 component. Spring과 연계하여 사용하는 경우 애플리케이션 클래스에서 본 컴포넌트를 직접 취급하지 않는다.

| 구성 요소 및 설정 파일 | 설명 |
|:-----:|:-----:|
| MyBatis configuration file | MyBatis3의 동작 설정을 설명하는 XML 파일이다. 데이터베이스 접속 대상, 매핑 파일 경로, MyBatis 동작 설정 등 세부 사항을 설명하는 파일이다. Spring과 통합하여 사용 시 데이터베이스의 연결 대상 및 매핑 파일 경로 설정을 구성 파일에 지정할 필요가 없기 때문에 MyBatis3의 기본 작업을 변경하거나 확장할 때 설정이 수행된다. |
| org.apache.ibatis.session.SqlSessionFactoryBuilder | MyBatis 설정 파일을 읽고 SqlSessionFactory를 생성하기 위한 component. 이 컴포넌트는 Spring과 통합하여 사용할 때 애플리케이션 클래스에 의해 직접 처리되지 않는다. |
| org.apache.ibatis.session.SqlSessionFactory | SqlSession을 생성하기 위한 컴포넌트. 이 컴포넌트는 Spring과 통합하여 사용할 때 애플리케이션 클래스에 의해 직접 처리되지 않는다. |
| org.apache.ibatis.session.SqlSession | SQL 실행 및 트랜잭션 제어를 위한 API를 제공하는 컴포넌트. MyBatis3를 이용하여 데이터베이스에 액세스할 때 가장 중요한 역할을 하는 컴포넌트이다. 이 컴포넌트를 Spring과 통합하여 사용하는 경우 애플리케이션 클래스에서 직접 처리하지 않는다. |
| Mapper interface | 매핑 파일에 정의된 SQL 형식을 안전하게 호출하기 위한 인터페이스. Mapper 인터페이스에 대한 구현 클래스는 MyBatis3가 자동으로 생성하기 때문에 개발자는 인터페이스 만 작성하면 된다. |
| Mapping file | SQL과 O/R 매핑 설정을 작성하는 XML 파일 |

MyBatis3의 주요 구성 요소가 데이터베이스에 액세스하는 흐름은 다음과 같다. 데이터베이스 접근 과정은 크게 두 가지로 나눌 수 있다.

* 애플리케이션 시작 시 수행되는 프로세스이다. 아래 프로세스 (1) ~ (3)의 프로세스가 이에 해당한다.

* 클라이언트의 각 요청에 대해 수행되는 프로세스이다. 아래 프로세스 (4) ~ (10)이 이 유형에 해당한다.

* MyBatis3 구성 요소의 관계

![mybatis3-relationship-of-components](/assets/img/2021-02-22-difference-jdbc-mybatis-jpa-spring-data-jpa/mybatis3-relationship-of-components.png)

애플리케이션 시작 시 수행되는 프로세스는 다음과 같은 흐름으로 실행된다.

① 애플리케이션은 SqlSessionFactoryBuilder에 대해서 SqlSessionFactory의 구축을 의뢰한다.

② SqlSessionFactoryBuilder는 SqlSessionFactory를 생성하기 위해 MyBatis 설정 파일을 가져온다.

③ SqlSessionFactoryBuilder는 MyBatis 설정 파일 정의에 따라 SqlSessionFactory를 생성한다.

클라이언트의 각 요청에 대해 수행되는 프로세스는 다음과 같은 흐름으로 실행된다.

④ 클라이언트는 어플리케이션 처리를 의뢰한다.

⑤ 애플리케이션은 SqlSessionFactoryBuilder에 의해 구축된 SqlSessionFactory로 부터 SqlSession을 얻는다.

⑥ SqlSessionFactory는 SqlSession을 생성해 애플리케이션에 반환한다.

⑦ 애플리케이션은 SqlSession에서 Mapper 인터페이스의 구현 오브젝트를 가져온다.

⑧ 애플리케이션은 Mapper 인터페이스의 메서드를 호출한다.

⑨ Mapper 인터페이스의 구현 오브젝트는 SqlSession 메소드를 호출하여 SQL의 실행을 의뢰한다.

⑩ SqlSession은 매핑 파일에서 실행할 SQL을 가져와 SQL을 실행한다.

### 2) MyBatis3와 Spring의 연계
MyBatis3와 Spring을 연계시키는 라이브러리로 MyBatis에서 MyBatis-Spring 이라는 라이브러리가 제공되고있다.

이 라이브러리를 사용하여 MyBatis3 컴포넌트를 Spring의 DI 컨테이너에서 관리할 수 있다.

MyBatis-Spring을 사용하면

* MyBatis3의 SQL 실행을 Spring이 관리하는 트랜잭션 내에서 실행할 수 있기 때문에 MyBatis3의 API를 사용하여 트랜잭션 제어를 할 필요가 없다.

* MyBatis3 예외는 Spring이 제공하는 범용적인 예외(org.springframework.dao.DataAccessException)로 변환되기 때문에 MyBatis3의 API에 의존하지 않는 예외 처리를 구현할 수 있다.

* MyBatis3를 사용하기 위한 초기화 작업은 모든 MyBatis-Spring API가 해주기 때문에 기본적으로 MyBatis3 API를 직접 사용할 필요가 없다.

* 쓰레드 세이프 한 Mapper 객체 생성을 가능하게 함으로써 싱글 톤 Service 클래스에 Mapper 개체를 주입 할 수 있다.

등의 장점이 있다.

* [Mybatis-Spring Reference Documentation 참조](http://mybatis.org/spring/ko/index.html)

### 3) MyBatis-Spring의 구성 요소
MyBatis-Spring은 다음 구성 요소를 연계하는 것으로, MyBatis3와 Spring의 통합을 실현하고있다.

| 구성 요소 및 설정 파일 | 설명 |
|:-----:|:-----:|
| org.mybatis.spring.SqlSessionFactoryBean | SqlSessionFactory 를 구축하고, Spring의 DI 컨테이너에 객체를 저장하기 위한 구성 요소. MyBatis3 표준은 MyBatis 설정 파일에 정의되어있는 정보를 바탕으로 SqlSessionFactory를 구축하는데, SqlSessionFactoryBean를 사용하면 MyBatis 설정 파일이 없이도 SqlSessionFactory을 구축할 수 있다. 물론 병용하는 것도 가능하다. |
| org.mybatis.spring.mapper.MapperFactoryBean | 싱글 톤의 Mapper 오브젝트를 구축하고 Spring의 DI 컨테이너에 객체를 저장하기 위한 컴포넌트. MyBatis3 표준 구조에서 생성되는 Mapper 객체는 스레드로부터 안전하지 않기 때문에 스레드마다 인스턴스를 할당해야 한다. MyBatis-Spring 컴포넌트로 만들어진 Mapper 오브젝트는 스레드 세이프 한 Mapper 오브젝트를 생성할 수 있기 때문에 Service 등의 싱글 톤 컴포넌트에 DI 할 수 있게 된다. |
| org.mybatis.spring.SqlSessionTemplate | SqlSession인터페이스를 구현한 싱글 톤 버전의 SqlSession 구성 요소. MyBatis3 표준 구조에서 생성되는 SqlSession 개체는 스레드로부터 안전하지 않기 때문에 스레드마다 인스턴스를 할당할 필요가 있었다. MyBatis-Spring 컴포넌트에서 생성된 SqlSession 객체는 스레드 세이프한 SqlSession 오브젝트가 생성되므로 Service 등의 싱글 톤 컴포넌트에 DI 할 수 있게 된다. |

다음은 MyBatis-Spring의 주요 컴포넌트가 어떤 흐름에서 데이터베이스에 액세스하고 있는지를 설명한다. 데이터베이스에 액세스하기 위한 처리는 크게 두 가지로 나눌 수 있다

* 애플리케이션 시작시 성능. 다음 (1) ~ (4)의 처리가 이에 해당한다.

* 클라이언트의 요청마다 수행하는 작업. 아래 (5) ~ (11)의 처리가 이에 해당한다.

![mybatis-spring-relationship-of-components](/assets/img/2021-02-22-difference-jdbc-mybatis-jpa-spring-data-jpa/mybatis-spring-relationship-of-components.png)

애플리케이션 시작시 처리는 다음의 흐름으로 실행된다.

① SqlSessionFactoryBean은 SqlSessionFactoryBuilder에 SqlSessionFactory 구축을 의뢰한다.

② SqlSessionFactoryBuilder은 SqlSessionFactory을 생성하기 위해 MyBatis 설정 파일을 가져온다.

③ SqlSessionFactoryBuilder는 MyBatis 설정 파일의 정의에 따라 SqlSessionFactory를 생성한다. 생성된 SqlSessionFactory은 Spring의 DI 컨테이너에 의해 관리된다.

④ MapperFactoryBean 스레드 세이프한 SqlSession(SqlSessionTemplate)과 스레드 세이프한 Mapper 오브젝트(Mapper 인터페이스의 Proxy 오브젝트)를 생성한다. 생성된 Mapper 객체는 Spring의 DI 컨테이너에 의해 관리되며 Service 클래스 등에 DI 된다. Mapper 오브젝트는 스레드 세이프한 SqlSession(SqlSessionTemplate)를 이용하여 스레드 세이프한 구현을 제공하고있다.

클라이언트의 요청마다 수행하는 작업은 다음의 흐름으로 실행된다.

⑤ 클라이언트 애플리케이션에 처리를 의뢰한다.

⑥ 어플리케이션(Service)는 DI 컨테이너에 의해 주입된 Mapper 오브젝트(Mapper 인터페이스를 구현하는 Proxy 오브젝트) 메소드를 호출한다.

⑦ Mapper 오브젝트는 호출된 메소드에 대응하는 SqlSession(SqlSessionTemplate) 메소드를 호출한다.

⑧ SqlSession(SqlSessionTemplate)은 Proxy 된 스레드로부터 세이프한 SqlSession 메서드를 호출한다.

⑨ Proxy 된 스레드로부터 세이프한 SqlSession 트랜잭션에 할당된 MyBatis3 표준 SqlSession사용한다. 트랜잭션에 할당된 SqlSession이 존재하지 않는 경우는 MyBatis3 표준 SqlSession를 얻기 위해, SqlSessionFactory 메소드를 호출한다.

⑩ SqlSessionFactory는 MyBatis3 표준 SqlSession를 반환한다. 반환된 MyBatis3 표준 SqlSession은 트랜잭션에 할당되기 때문에 동일한 트랜잭션 내라면 새로 생성되는 것이 아니라 같은 SqlSession 사용되는 구조로 되어 있다.

⑪ MyBatis3 표준 SqlSession은 매핑 파일에서 실행할 SQL을 취득하여 SQL을 실행한다.

## 3. JPA(Java Persistent API)
JPA를 사용해 데이터베이스에 접속하는 방법에 대해 설명한다. JPA 프로바이더로서 Hibernate를 JPA의 래퍼로서 Spring Data JPA를 사용하는 것을 전제로 하고 있다.

* JPA

![jpa](/assets/img/2021-02-22-difference-jdbc-mybatis-jpa-spring-data-jpa/jpa.png)

### 1) JAP란
JPA (Java Persistence API)는 관계형 데이터베이스에서 관리되는 레코드를 Java 오브젝트에 매핑하는 방법과 매핑된 Java 오브젝트에 대해 수행된 작업을 관계형 데이터베이스 레코드에 반영하기 위한 구조를 Java API 사양으로 정의한 것이다.

JPA는 사양을 정의하고 있을 뿐 구현은 제공하고 있지 않다. JPA 구현은 Hibernate와 같은 O/R Mapper를 개발하는 벤더에 의해 참조 구현으로 제공되고 있다. 따라서 O/R Mapper를 개발하고 있는 벤더에 의해 구현된 참조 구현을 JPA 공급자라고 부른다.

### 2) JPA의 O/R 매핑
JPA 사용 시 관계형 데이터베이스에서 관리되고 있는 레코드와 Java 오브젝트는 다음과 같다.

* O/R 매핑

![jpa-mapping](/assets/img/2021-02-22-difference-jdbc-mybatis-jpa-spring-data-jpa/jpa-mapping.png)

JPA에서는 '관리 상태'라고 불리는 상태의 Entity가 저장된 값을 변경(setter 메소드를 호출해 값을 변경) 했을 경우 변경 내용을 관계형 데이타베이스에 반영되는 메커니즘이 있다.

이 메커니즘은 편집 기능이 있는 테이블 뷰어와 같은 클라이언트 소프트웨어와 매우 유사하다.
테이블 뷰어 같은 클라이언트 소프트웨어에서는 뷰어값이 변경되면 데이터베이스에 반영되지만 JPA에서는 'Entity'라는 Java 객체(Java Bean)의 값이 변경되면 데이터베이스에 반영된다.

### 3) JPA 기본 용어

| 용어 | 설명 |
|:-----:|:-----:|
| Entity class | 관계형 데이터베이스에서 관리되고 있는 레코드를 표현하는 Java 클래스. @javax.persistence.Entity 어노테이션이 부여된 클래스가 Entity 클래스가 된다. |
| EntityManager | Entity의 라이프사이클을 관리하기 위해 필요한 API를 제공하는 인터페이스. 애플리케이션은 javax.persistence.EntityManager의 메소드를 사용하여 관계형 데이타베이스로 관리되고 있는 레코드를 Java 오브젝트로 조작한다. Spring Data JPA를 사용하는 경우에는 직접 사용하지 않지만 Spring Data JPA 구조로는 표현할 수 없는 Query를 발행해야 할 경우 이 인터페이스를 통해 Entity를 취득하게 된다. |
| TypedQuery | Entity를 검색하기 위한 API를 제공하는 인터페이스. 애플리케이션은 javax.persistence.TypedQuery 메소드를 사용하여 ID 이외의 조건에 일치하는 Entity를 검색한다. Spring Data JPA를 사용하는 경우에는 직접 사용하지 않지만, Spring Data JPA 구조에서는 표현할 수 없는 Query를 발행해야 할 경우에는 이 인터페이스를 사용하여 Entity를 검색하게 된다. 조건에 일치하는 영속 계층(DB)의 Entity를 직접 조작(업데이트 또는 삭제)하기 위한 메소드도 이 인터페이스에 포함되어 있다. |
| PersistenceContext | Entity를 관리하는 영역이다. EntityManager를 통해 취득 또는 생성된 Entity는 이 영역에 포함되어 라이프사이클 관리된다. 이 영역에서 관리되는 Entity 일을 '관리 상태의 Entity'라고 부른다. 이 영역은 응용 애플리케이션에서 직접 액세스할 수 없다. Entity의 상태는 '관리 상태' 이 외에, '작성 상태', '삭제 상태', '분리 상태'가 존재한다. |
| find method | 관리 상태의 Entity를 취득하기 위한 메소드. ID에 해당하는 Entity가 PersistenceContext에 존재하지 않는 경우는 관계형 데이터베이스에 저장되어있는 레코드를 검색(SELECT)하고 관리 상태의 Entity를 생성한다. |
| persist method | 애플리케이션으로 만든 상태의 Entity를 관리 상태의 Entity로 하기 위한 메소드. EntityManager 메소드로 제공되며 관계형 데이터베이스에 레코드 INSERT를 실행하는 작업이 PersistenceContext에 축적된다. |
| merge method | PersistenceContext에서 관리되지 않는 분리 상태의 Entity를 관리 상태의 Entity로 만들기 위한 메소드. EntityManager 메소드로 제공되며 기본적으로는 관계형 데이터베이스에 저장되어있는 레코드에 대해 UPDATE를 실행하기 위한 작업이 PersistenceContext에 축적된다. 그러나 관계형 데이터베이스에 ID와 일치하는 레코드가 존재하지 않는 경우는 UPDATE 대신 INSERT가 실행된다. |
| remove method | 관리 상태의 Entity를 삭제 상태의 Entity로 만들기 위한 메소드. EntityManager 메소드로 제공되며 관계형 데이터베이스에 저장되어있는 레코드에 대해 DELETE를 실행하기 위한 작업이 PersistenceContext에 축적된다. |
| flush method | PersistenceContext에서 관리되는 Entity에 대해 수행된 작업을 관계형 데이터베이스에 강제로 적용하는 방법. EntityManager 메소드로 제공되고 있으며 축적된 미반영 작업을 관계형 데이터베이스에 대해 실행한다. 일반적으로 관계형 데이터베이스에 반영은 트랜잭션 커밋시에 행해지지만 커밋 이전에 반영할 필요가 있는 경우 이 방법을 사용한다. |


### 4) Entity 라이프 사이클 관리
엔티티의 수명 주기는 다음과 같이 관리된다.

* JPA 수명 주기

![jpa-lifecycle](/assets/img/2021-02-22-difference-jdbc-mybatis-jpa-spring-data-jpa/jpa-lifecycle.png)

① EntityManager의 persist 메소드를 호출하면 인수로 전달된 Entity(생성 상태의 Entity)가 관리 상태의 Entity로 PersistenceContext에 저장된다.

② EntityManager의 find 메소드를 호출하면 인수로 전달된 ID를 가진 관리 상태의 Entity가 반환된다. PersistenceContext에 존재하지 않는 경우는 Query를 실행하여 관계형 데이터베이스로부터 매핑할 레코드를 검색하고 관리 상태의 Entity로 저장한다.

③ EntityManager의 merge 메소드를 호출하면 인수로 전달된 Entity(분리 상태의 Entity)의 상태가 관리 상태의 Entity에 병합된다. PersistenceContext에 존재하지 않는 경우는 Query를 발행하여 관계형 데이터베이스로부터 매핑 대상의 레코드를 검색하고 관리 상태의 Entity를 저장한 후 인수로 전달된 Entity의 상태가 병합된다. 이 메소드를 호출하면 persist 메소드와 달리 인수로 전달된 Entity가 관리 상태의 Entity로 저장되는 것은 아니라는 점에 유의해야 한다.

④ EntityManager의 remove 메소드를 호출하면 인수로 전달된 관리 상태의 Entity가 삭제 상태의 Entity가 된다. 이 메소드를 호출하면 삭제 상태가 된 Entity를 얻을 수 없게 된다.

⑤ EntityManager의 flush 메소드를 호출하면 persist, merge, remove 메소드에 의해 축적된 Entity에 작업이 관계형 데이터베이스에 반영된다. 이 메소드를 호출하는 것으로 Entity에 대한 변경 내용은 관계형 데이터베이스의 레코드에 동기화된다. 그러나 관계형 데이터베이스 쪽의 레코드에 대해서만 변경된 내용은 Entity에는 동기화되지 않는다. EntityManager의 find 메소드를 사용하지 않고 Query를 발행하고 Entity를 검색하면 검색 작업을 수행하기 전에 EntityManager 내부의 처리로 flush 메소드와 동일한 처리가 실행되고 축적된 Entity에 작업이 관계형 데이터베이스에 반영되는 구조로 되어 있다.

## 4. Spring Data JPA
Spring Data JPA는 JPA를 사용하여 Repository를 생성할 수 있는 라이브러리를 제공한다.

Spring Data JPA를 사용하면 Query 메소드라는 메소드를 Repository 인터페이스로 정의하면, 지정한 조건에 일치하는 Entity를 얻을 수 있기 때문에, Entity 작업을 위한 구현을 줄일 수 있다.

그러나 Query 메소드로 정의할 있는 것은 어노테이션으로 표현할 수 있는 정적 Query뿐이므로, 동적 Query 등의 어노테이션으로 표현할 수 없는 Query 내용은 사용자 정의 Repository 클래스의 구현이 필요하다.

* Spring Data JPA의 기본 흐름

![jpa-basic-flow](/assets/img/2021-02-22-difference-jdbc-mybatis-jpa-spring-data-jpa/jpa-basic-flow.png)

① Service에서 Repository 인터페이스의 메소드를 호출한다. 메소드의 호출 파라미터로 Entity 객체 Entity의 ID 등이 전달된다. 위 예에서는 Entity를 전달하고 있지만, 원시적인 값이 될 수도 있다.

② Repository 인터페이스를 동적으로 구현 한 Proxy 클래스는 org.springframework.data.jpa.repository.support.SimpleJpaRepository나 사용자 정의 Repository 클래스 처리를 위임한다. Service에서 지정된 파라미터가 전달된다.

③ Repository 구현 클래스는 JPA API를 호출한다. Service에서 지정된 매개 변수와 Repository 구현 클래스에서 생성한 파라미터 등이 전달된다.

④ Hibernate의 JPA 참조 구현은 Hibernate의 코어 API의 처리를 호출한다. Repository 구현 클래스에서 지정된 파라미터나 Hibernate의 JPA 참조 구현 클래스에서 생성한 파라미터 등이 전달된다.

⑤ Hibernate의 코어 API는 지정된 파라미터에서 SQL과 바인딩 값을 생성하여 JDBC 드라이버에 전달한다.(실제 값의 바인딩은 java.sql.PreparedStatement의 API가 사용됨)

⑥ JDBC 드라이버는 전달된 SQL과 바인딩 값을 데이터베이스로 전송하여 SQL을 실행한다.

Spring Data JPA를 사용하여 Repository를 만들 때 JPA API를 직접 호출할 필요는 없지만, Spring Data JPA의 Repository 인터페이스의 메소드가, JPA 어떤 메소드를 호출하는지는 의식 두는 것이 좋다.

다음은 Spring Data JPA의 Repository 인터페이스의 대표적인 메소드가 JPA 어떤 메소드를 호출하고 있는지를 보여준다.

* Spring Data JPA와 JPA의 API 매핑

![jpa-api-mapping](/assets/img/2021-02-22-difference-jdbc-mybatis-jpa-spring-data-jpa/jpa-api-mapping.png)

* JPA, Hibernate, Spring Data JPA의 개념

![jpa-hibernate-spring-data-jpa-concept](/assets/img/2021-02-22-difference-jdbc-mybatis-jpa-spring-data-jpa/jpa-hibernate-spring-data-jpa-concept.png)

## [출처 및 참고]
* <https://terasolunaorg.github.io/guideline/5.6.1.RELEASE/ja/ArchitectureInDetail/DataAccessDetail/DataAccessCommon.html>
* <https://terasolunaorg.github.io/guideline/5.6.1.RELEASE/ja/ArchitectureInDetail/DataAccessDetail/DataAccessMyBatis3.html>
* <https://terasolunaorg.github.io/guideline/5.6.1.RELEASE/ja/ArchitectureInDetail/DataAccessDetail/DataAccessJpa.html>
* <https://suhwan.dev/2019/02/24/jpa-vs-hibernate-vs-spring-data-jpa/>
