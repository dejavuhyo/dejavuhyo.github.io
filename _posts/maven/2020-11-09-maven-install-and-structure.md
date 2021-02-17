--- 
title: Maven 설치 및 구조
author: dejavuhyo
date: 2020-11-09 10:00:00 +0900
categories: [DevOps, Maven]
tags: [maven, maven-structure, 메이븐, 메이븐-구조]
---

## 1. 대표 기능

### 1) 빌드 툴

* 항상 하나의 컴포넌트로 빌드된다.
* 의존성을 관리한다.
* 빌드 수행 후 리포트 생성

### 2) 패키징

* 배포 가능한 파일 생성: war, jar, exe 등

### 3) 프로젝트 관리

* 버전과 릴리즈를 관리
* 어떤 프로젝트인지 명시하고, 무엇을 만드는지 기록
* javadoc을 쉽게 작성할 수 있다.

### 4) Test

* 단위 테스트 수행
* 빌드 결과가 정상적인지 점검

### 5) 배포

* 실운영서버에 배포가 아니다.
* 해당 프로젝트의 버전을 관리하기위해 특정 원격저장소에 배포하는 것
* 빌드 후 생성된 아티팩트(컴포넌트)를 로컬 혹은 원격 저장소에 배포한다.

## 2. 설치

### 1) Java 설치 필요

* <https://dejavuhyo.blog.me/221454888121>
* <https://dejavuhyo.blog.me/221469375994>

### 2) 다운로드

* Maven [다운로드](https://maven.apache.org/download.cgi) 페이지에서 압축 파일을 받고 특정위치에 압축을 푼다.

### 3) PATH 설정

* Windows: 제어판 → 시스템 등록 정보 → 고급 → 시스템 환경 변수 추가 Path 항목에 Java와 Maven 위치 추가
* Mac & Linux: bash 정보 파일에서 Path Java와 Maven 위치 추가
* 경로: jdk설치경로/bin, 메이븐설치경로/bin

### 4) 설치 확인

```shell
[root@localhost ~]# java -version
[root@localhost ~]# mvn --version
```

## 3. 디렉토리 구조
Maven 기본 설정 파일인 pom.xml 파일이 프로젝트 root에 위치

### 1) 기본 소스코드 디렉토리

* src/main/언어명
  - java: src/main/java
  - groovy: src/main/groovy

### 2) 테스트 코드 디렉토리

* src/test/java

### 3) compile 결과 디렉토리

* target

![img001](/assets/img/2020-11-09-maven-install-and-structure/img001.png)

## 4. pom.xml 파일 구조

### 1) 프로젝트 정보

* modelVersion
* groupId
* artifactId
* version
* packaging

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.blidkaga.group</groupId>
    <artifactId>group-test</artifactId>
    <name>test</name>
    <packaging>war</packaging>
    <version>1.0.0-SNAPSHOT</version>
</project>
```

### 2) properties
maven 내부에서 반복적으로 사용될 상수 값을 정의할 때 사용

```xml
<properties>
    <java-version>1.6</java-version>
    <org.springframework-version>3.1.1.RELEASE</org.springframework-version>
    <org.aspectj-version>1.6.10</org.aspectj-version>
    <org.slf4j-version>1.6.6</org.slf4j-version>
</properties>
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>${org.springframework-version}</version>
        <exclusions>
            <exclusion>
                <groupId>commons-logging</groupId>
                <artifactId>commons-logging</artifactId>
            </exclusion>
        </exclusions>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>${org.springframework-version}</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-tx</artifactId>
        <version>${org.springframework-version}</version>
        <scope>provided</scope>
    </dependency>
</dependencies>
```

### 3) Dependencies(의존성 라이브러리 정보)
project 태그 바로 하위에 있으며 의존성 라이브러리 정보를 포함함. 최소 groupId, artifactId, version 정보가 필요하며 dependencies 색션 아래 세부 dependency가 기술됨

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>${org.springframework-version}</version>
        <exclusions>
            <exclusion>
                <groupId>commons-logging</groupId>
                <artifactId>commons-logging</artifactId>
            </exclusion>
        </exclusions>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>${org.springframework-version}</version>
        <scope>provided</scope>
    </dependency>
</dependencies>
```

### 4) 빌드 정보
project 태그 바로 하위에 있으며 plugins은 빌드에서 사용할 플러그인이며 디렉토리 구조이다.

```xml
<build>
    <sourceDirectory>src/main/java</sourceDirectory>
    <testSourceDirectory>src/test/java</testSourceDirectory>
    <outputDirectory>${project.basedir}/target/classes</outputDirectory>
    <resources>
        <resource>
            <directory>src/main/resources</directory>
            <filtering>true</filtering>
        </resource>
        <resource>
            <directory>resources-${deploy.phase}/</directory>
            <filtering>true</filtering>
        </resource>
    </resources>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>2.5.1</version>
            <configuration>
                <source>1.6</source>
                <target>1.6</target>
                <compilerArgument>-Xlint:all</compilerArgument>
                <showWarnings>true</showWarnings>
                <showDeprecation>true</showDeprecation>
            </configuration>
        </plugin>
        </plugin>
    </plugins>
</build>
```

### 5) Repository
의존성을 다운로드 받을 위치의 repository로 기술되지 않을 시 기본적인 위치는 <http://repo.maven.apache.org/maven2>이다. 다수의 repository 기술 가능하며 회사 내부의 repository를 기술 하기도 한다.

* nexus, artifactory를 이용

```xml
<repositories>
    <repository>
        <id>spring-snapshot</id>
        <name>Spring Snapshot Repository</name>
        <url>https://repo.spring.io/snapshot</url>
        <snapshots>
            <enabled>true</enabled>
        </snapshots>
        <releases>
            <enabled>false</enabled>
        </releases>
    </repository>
</repositories>
```

### 6) Plugin Repository
maven plugin을 다운로드 받을수 있는 저장소 위치를 기술

```xml
<pluginRepositories>
    <pluginRepository>
        <id>acme corp</id>
        <name>Acme Internal Corporate Repository</name>
        <url>http://acmecorp.com/plugins</url>
        <snapshots>
            <enabled>true</enabled>
        </snapshots>
        <releases>
            <enabled>false</enabled>
        </releases>
    </pluginRepository>
</pluginRepositories>
```

### 7) 배포
deploy goal을 실행했을 시 배포되는 위치를 기술하고 url 항목에는 일반적으로 회사 내부에 설치한 repository url이 기술된다.(nexus, artifactory 등)

```xml
<distributionManagement>
    <repository>
        <id>releases-repo</id>
        <name>Releases Repository</name>
        <url>일반적인 회사 내부 repository url</url>
    </repository>
    <snapshotRepository>
        <id>snapshots-repo</id>
        <name>Snapshots Repository</name>
        <url>일반적인 회사 내부 repository url</url>
    </snapshotRepository>
</distributionManagement>
```

## 5. Goal
Maven이 행할수 있는 여러가지 동작을 수행하는 명령을 Goal이라고하며, 실행 방식, 체인 형태로 실행가능

```shell
[root@localhost ~]# mvn [goal명령어] [goal명령어] [goal명령어] 
```

### 1) goal 종류

* **clean:** 컴파일 결과물인 target 디렉토리 삭제
* **compile:** 모든 소스코드 컴파일, 리소스파일을 target/classes 디렉토리에 복사
* **package:** compile 수행 후, 테스트 수행, packaging 정보에 따라 패키징 수행
* **install:** package 수행 후, local repo에 install 수행
* **deploy:** install 수행 후, 배포 수행, 여기서 배포는 웹서버에 배포가 아니라 회사 repo에 배포다.

_아래와 같이 distributionManagement 항목이 기술되어야 한다._

```xml
<distributionManagement>
    <repository>
        <id>releases-repo</id>
        <name>Releases Repository</name>
        <url>회사repository주소(넥서스)</url>
    </repository>
    <snapshotRepository>
        <id>snapshots-repo</id>
        <name>Snapshots Repository</name>
        <url>회사repository주소(넥서스)</url>
    </snapshotRepository>
</distributionManagement>
```

## 6. Repository(저장소)
Maven은 Repository라는 개념이 있으며, Local Repository, Repository, Plugin Repository 3가지로 구분된다.

### 1) Local Repository(로컬 저장소)
Maven은 dependency 및 packaging한 프로젝트 결과물을 local에 특정 위치에 저장

* Linux & Mac: [홈디렉토리]/.m2
* Windows: C:\Users\[유저이름]/.m2

.m2 디렉토리 하위에 dependency 및 packaging한 결과물들을 groupId, artifactId, versions값으로 하위 디렉토리구조가 생성이 되며 저장된다.

![img002](/assets/img/2020-11-09-maven-install-and-structure/img002.png)

* 여러 프로젝트에서 중복된 라이브러리 다운로드를 피하기위한 목적을 가진다.

### 2) Repository
의존성을 다운로드 받을 위치의 repository로 기술되지 않을 시 기본적인 위치는 <http://repo.maven.apache.org/maven2>이다. 다수의 repository 기술 가능하며 회사 내부의 repository를 기술 하기도 한다.(nexus, artifactory를 이용)

```xml
<repositories>
    <repository>
        <id>spring-snapshot</id>
        <name>Spring Snapshot Repository</name>
        <url>https://repo.spring.io/snapshot</url>
        <snapshots>
            <enabled>true</enabled>
        </snapshots>
        <releases>
            <enabled>false</enabled>
        </releases>
    </repository>
</repositories>
```

### 3) Plugin Repository
maven plugin을 다운로드 받을수 있는 저장소 위치를 기술하며 다수 pluginRepository 기술 가능

```xml
<pluginRepositories>
    <pluginRepository>
        <id>acme corp</id>
        <name>Acme Internal Corporate Repository</name>
        <url>http://acmecorp.com/plugins</url>
        <snapshots>
            <enabled>true</enabled>
        </snapshots>
        <releases>
            <enabled>false</enabled>
        </releases>
    </pluginRepository>
</pluginRepositories>
```

## 7. Version

### 1) SNAPSHOT
개발진행 중임을 나타낸다. version이 변경되지 않았더라도 항상 변경사항을 다운로드 한다. 소문자로는 작동하지 않는다. ex) 1.0-SNAPSHOT

### 2) 특별 네이밍이 없는 경우
특별 네이밍이 없는 경우는 release 상태를 의미 ex) 1.0.1

### 3) 일반적인 관용어구
일반적인 관용어구는 maven에 특별한 기능을 부여하지 않는다. 즉 SNAPSHOT에만 특별한 동작을 한다.

* ex) 1.0-RC1 (release candidate)
* ex) 1.0-M1 (milestone release)
* ex) 1.0-RELEASE (release)

## 8. Scope
dependency 하위에 포함되는 항목으로 해당 dependency가 포함되는 범위에 대한 타입

### 1) scope 종류

* compile
  - 기본 scope
  - 미입력시에도 기본 적용
  - 모든 상황에서 포함됨
* provided
  - compile과 유사하게 모든 상황에서 수행된다.
  - 하지만, 다른 외부 컨테이너에서 기본 제공되는 API인경우 provided로 지정 시 마지막 패키징할 때 포함되지 않음
  - 예를 들면 tomcat에서 기본적으로 servlet api를 제공하기 때문에 servlet api를 provided로 지정하면 패키징시 제외된다.
* runtime
  - 컴파일 시에는 불필요 실행시에 필요한 경우
  - 런타임 및 테스트 시 classpath에 추가 되지만, 컴파일시에는 추가 되지 않음
* test
  - 테스트시에만 사용
* system
  - provided와 유사
  - system의 특정 path를 참조하도록 지정
  - Maven의 central repository를 사용하지 않음
* import
  - scope는 dependencyManagement 섹션에서 pom의 의존관계에 대해 사용

## 9. Plugin

### 1) Goal
Maven에서 사용되는 clean, compile, package 등의 goal 명령어들도 사실상 plugin으로 내장되어 있다.

* clean, compile, test, install, package, deploy

### 2) Compiler plugin
기본적으로 maven은 jdk 1.5 기준으로 compile을 시도한다. 1.5버전보다 더 높은 버전으로 compile 하기위해서는 다음과 같은 조치가 필요하다.

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>2.5.1</version>
    <configuration>
        <source>1.6</source>
        <target>1.6</target>
        <compilerArgument>-Xlint:all</compilerArgument>
        <showWarnings>true</showWarnings>
        <showDeprecation>true</showDeprecation>
    </configuration>
</plugin>
```

### 3) Jar Plugin
jar 패키징된 파일을 실행하기 위해서 ManifestFile을 생성해주는 plugin

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-jar-plugin</artifactId>
    <version>3.1.0</version>
    <configuration>
      <archive>
        <manifest>
          <addClasspath>true</addClasspath>
          <mainClass>HelloWorld</mainClass>
        </manifest>
      </archive>
    </configuration>
</plugin>
```

### 4) Source Plugin
package 시에 Source 코드를 포함시킨다. 아래의 코드를 추가하면 package 수행 및 install 수행 시 기본jar명-sources.jar 파일이 추가로 하나 생성된다. 내부에 goal 설정은 plugin 별로 고유의 goal이 있다.

* Plugin Documentation: <https://maven.apache.org/plugins/maven-source-plugin/plugin-info.html>

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-source-plugin</artifactId>
    <version>3.0.1</version>
    <executions>
        <execution>
            <id>attach-resources</id>
            <phase>package</phase>
            <goals>
                    <goal>jar</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

### 5) Javadoc Plugin
java doc 문서를 자동 발행해주는 플러그인으로 java 코드에 문서화 주석을 html문서로 떨구어 준다.

```java
public class HelloWorld{
    public static void main(String args[]){
        System.out.println("Maven Hello World!");
    }
}
```

javadoc plugin 을 설정하는 코드로 configuration 항목은 한글 관련 설정

```xml
<plugin>
   <groupId>org.apache.maven.plugins</groupId>
   <artifactId>maven-javadoc-plugin</artifactId>
   <version>2.9.1</version>
   <configuration>
    <locale>ko_kr</locale>
    <encoding>utf-8</encoding>
   </configuration>
 </plugin>
```

* javadoc goal 명령어 모음 및 goal 과 관련된 내용: <https://maven.apache.org/plugins/maven-javadoc-plugin/plugin-info.html>

* remote에 자동 업로드 참고: <https://www.lesstif.com/display/JAVA/maven+site+plugin>

```text
mvn javadoc:javadoc
mvn javadoc:jar
mvn javadoc:aggregate
mvn javadoc:aggregate-jar
mvn javadoc:test-javadoc
mvn javadoc:test-jar
mvn javadoc:test-aggregate
mvn javadoc:test-aggregate-jar
```

### 6) Tomcat 배포 플러그인

* 참고: <http://blog.iotinfra.net/?p=1201>
* 참고: <https://www.lesstif.com/java/tomcat7-maven-plugin-app-deploy-14090451.html>

* tomcat-user.xml

```xml
<tomcat-users>
  <role rolename="manager-gui"/>
  <role rolename="manager-script"/>
  <user username="tomcat" password="tomcat" roles="manager-gui,manager-script"/>
</tomcat-users>
```

* pom.xml

```xml
<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-war-plugin</artifactId>
  <version>2.1.1</version>
  <configuration>
    <warSourceDirectory>src/main/webapp</warSourceDirectory>
    <webXml>src/main/webapp/WEB-INF/web.xml</webXml>
  </configuration>
</plugin>

<plugin>
  <groupId>org.codehaus.mojo</groupId>
  <artifactId>tomcat-maven-plugin</artifactId>
  <version>1.1</version>
  <configuration>
    <url>http://localhost:8080/manager/text</url>
    <path>/simple</path>
    <username>tomcat</username>
    <password>tomcat</password>
  </configuration>
</plugin>
```

## [출처 및 참고]
* <https://wikidocs.net/book/1910>
