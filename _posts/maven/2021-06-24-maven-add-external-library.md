---
title: Maven 외부 라이브러리 추가
author: dejavuhyo
date: 2021-06-24 06:00:00 +0900
categories: [DevOps, Maven]
tags: [maven-add-external-library, maven-external-library, add-external-library, 메이븐-외부-라이브러리-추가, 메이븐-외부-라이브러리, maven-외부-라이브러리-추가, maven-외부-라이브러리]
---

## 1. 단순 참조
dependency 정의 시 scope 노드와 systemPath 노드를 사용하여 프로젝트에 포함된 jar 파일을 지정하여 줄 수 있다.

groupId, artifactId, version은 임의로 정의해도 되며, ```${project.basedir}```는 프로젝트의 ROOT 패스이다.

* pom.xml

```xml
<dependency>
    <groupId>com.kicc</groupId>
    <artifactId>easypay</artifactId>
    <version>1.8</version>
    <scope>system</scope>
    <systemPath>${project.basedir}/src/main/webapp/WEB-INF/lib/easypay_client18.jar</systemPath>
</dependency>
```

## 2. 로컬 Repository
단순 참조를 할 경우 scrop의 system이기 때문에 maven 빌드 시 해당 jar 파일이 포함되지 않는다. 빌드 결과물에 해당 jar 파일을 포함해야 하는 경우 로컬 repositoy를 활용한다.

* pom.xml

```xml
<repository>
    <id>local-repository</id> 
    <name>local repository</name> 
    <url>file://${project.basedir}/repo</url> 
</repository>

<dependency>
    <groupId>custom</groupId>
    <artifactId>sleepyeyes-custom</artifactId>
    <version>1.0</version>
</dependency>
```

* 디렉토리 구성

![repository](/assets/img/2021-06-24-maven-add-external-library/repository.png)

## [출처 및 참고]
* [https://itnp.kr/post/maven-local-repository](https://itnp.kr/post/maven-local-repository)
* [https://maven.apache.org/guides/introduction/introduction-to-dependency-mechanism.html](https://maven.apache.org/guides/introduction/introduction-to-dependency-mechanism.html)
* [https://sleepyeyes.tistory.com/43](https://sleepyeyes.tistory.com/43)
