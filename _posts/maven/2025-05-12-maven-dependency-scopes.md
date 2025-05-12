---
title: Maven Dependency Scopes
author: dejavuhyo
date: 2025-05-12 09:00:00 +0900
categories: [DevOps, Maven]
tags: [maven, maven-dependency-scopes, maven-dependency, 메이븐, 메이븐-종속성-범위, 메이븐-종속성]
---

## 1. 전이적 종속성
Maven에는 직접 종속성과 전이적 종속성이라는 두 가지 유형의 종속성이 있다.

직접적인 종속성은 프로젝트에 명시적으로 포함되는 종속성이다.

`<dependency>` 태그를 사용하여 포함할 수 있다.

```xml
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
</dependency>
```

반면, 전이적 종속성은 직접 종속성에 의해 요구된다. Maven은 프로젝트에 필요한 전이적 종속성을 자동으로 포함한다.

`mvn dependency:tree` 명령을 사용하면 프로젝트의 전이적 종속성을 포함한 모든 종속성을 나열할 수 있다.

## 2. 종속성 범위
종속성 범위는 종속성의 전이성을 제한하는데 도움이 될 수 있다. 또한 다양한 빌드 작업에 대한 클래스 경로를 수정한다. Maven에는 6가지 기본 종속성 범위가 있다.

그리고 각 범위(import 제외)가 전이적 종속성에 영향을 미친다.

### 1) Compile
다른 범위가 제공되지 않을 때의 기본 범위이다.

이 범위의 종속성은 모든 빌드 작업에서 프로젝트의 클래스 경로에 존재한다. 또한 종속된 프로젝트에도 전파된다.

더 중요한 점은 이러한 종속성이 전이적이라는 것이다.

```xml
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-lang3</artifactId>
    <version>3.14.0</version>
</dependency>
```

### 2) Provided
이 범위는 JDK나 컨테이너가 런타임에 제공해야 하는 종속성을 표시하는데 사용된다.

이 범위의 좋은 사용 사례는 컨테이너에 배포된 웹 애플리케이션인데, 컨테이너가 이미 일부 라이브러리를 제공하는 경우이다. 예를 들어, 런타임에 서블릿 API를 이미 제공하는 웹 서버가 여기에 해당한다.

```xml
<dependency>
    <groupId>jakarta.servlet</groupId>
    <artifactId>jakarta.servlet-api</artifactId>
    <version>6.0.0</version>
    <scope>provided</scope>
</dependency>
```

제공된 종속성은 컴파일 시점과 프로젝트의 테스트 클래스 경로에서만 사용할 수 있다. 또한 이러한 종속성은 전이적이지 않다.

### 3) Runtime
이 범위의 종속성은 런타임에 필수이다. 하지만 프로젝트 코드 컴파일에는 필요하지 않다. 따라서 런타임 범위로 표시된 종속성은 런타임 및 테스트 클래스 경로에는 존재하지만, 컴파일 클래스 경로에는 존재하지 않다.

JDBC 드라이버는 런타임 범위를 사용해야 하는 종속성의 좋은 예이다.

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.28</version>
    <scope>runtime</scope>
</dependency>
```

### 4) Test
이 범위는 애플리케이션의 표준 런타임에서는 종속성이 필요하지 않지만 테스트 목적으로만 사용된다는 것을 나타낸다.

테스트 종속성은 전이적이지 않으며 테스트 및 실행 클래스 경로에만 존재한다.

이 범위의 표준 사용 사례는 JUnit과 같은 테스트 라이브러리를 애플리케이션에 추가하는 것이다.

```xml
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
    <scope>test</scope>
</dependency>
```

### 5) System
시스템 범위는 제공된 범위와 매우 유사하다. 가장 큰 차이점은 시스템의 경우 시스템의 특정 jar 파일을 직접 가리켜야 한다는 것이다.

시스템 범위는 더 이상 사용되지 않는다.

중요한 점은 종속성이 존재하지 않거나 systemPath가 가리키는 것과 다른 위치에 있는 경우 시스템 범위 종속성이 있는 프로젝트를 빌드하면 다른 컴퓨터에서 실패할 수 있다는 것이다.

```xml
<dependency>
    <groupId>com.baeldung</groupId>
    <artifactId>custom-dependency</artifactId>
    <version>1.3.2</version>
    <scope>system</scope>
    <systemPath>${project.basedir}/libs/custom-dependency-1.3.2.jar</systemPath>
</dependency>
```

### 6) Import
pom 종속성 유형에만 사용할 수 있다.

import는 이 종속성이 POM에 선언된 모든 유효한 종속성으로 대체되어야 함을 나타낸다.

여기에서, 아래의 사용자 정의 프로젝트 종속성은 사용자 정의 프로젝트의 `pom.xml` `<dependencyManagement>` 섹션에 선언된 모든 종속성으로 대체된다.

```xml
<dependency>
    <groupId>com.baeldung</groupId>
    <artifactId>custom-project</artifactId>
    <version>1.3.2</version>
    <type>pom</type>
    <scope>import</scope>
</dependency>
```

## 3. 범위와 전이성
각 종속성 범위는 고유한 방식으로 전이 종속성에 영향을 미친다. 즉, 서로 다른 전이 종속성이 프로젝트에서 서로 다른 범위를 가질 수 있다.

그러나 제공된 범위와 테스트가 있는 종속성은 결코 메인 프로젝트에 포함되지 않는다.

* Compile scope: 런타임 범위가 있는 모든 종속성은 프로젝트의 런타임 범위로 끌어오고, 컴파일 범위가 있는 모든 종속성은 프로젝트의 컴파일 범위로 끌어온다.

* Provided scope: 런타임 및 컴파일 범위 종속성이 모두 제공된 범위와 함께 프로젝트에 가져온다.

* Test scope: 런타임 및 컴파일 범위의 전이적 종속성이 모두 프로젝트의 테스트 범위와 함께 가져온다.

* Runtime scope: 런타임 및 컴파일 범위의 전이적 종속성이 모두 프로젝트의 런타임 범위와 함께 가져온다.

## [출처 및 참고]
* [https://www.baeldung.com/maven-dependency-scopes](https://www.baeldung.com/maven-dependency-scopes)
