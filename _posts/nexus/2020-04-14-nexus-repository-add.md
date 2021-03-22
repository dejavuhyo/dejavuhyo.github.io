---
title: Nexus Repository 추가
author: dejavuhyo
date: 2020-04-14 10:30:00 +0900
categories: [DevOps, Nexus]
tags: [nexus-maven-repository, nexus-repository, maven-repository, nexus-repository-add, 넥서스-메이븐-레파지토리, 넥서스-레파지토리, 메이븐-레파지토리, 넥서스-레파지토리-추가]
---

## 1. hosted Repository Add

![img001](/assets/img/2020-04-14-nexus-repository-add/img001.png)

![img002](/assets/img/2020-04-14-nexus-repository-add/img002.png)

## 2. Artifact Upload

![img003](/assets/img/2020-04-14-nexus-repository-add/img003.png)

![img004](/assets/img/2020-04-14-nexus-repository-add/img004.png)

### 1) From POM

![img005](/assets/img/2020-04-14-nexus-repository-add/img005.png)

![img006](/assets/img/2020-04-14-nexus-repository-add/img006.png)

![img007](/assets/img/2020-04-14-nexus-repository-add/img007.png)

![img008](/assets/img/2020-04-14-nexus-repository-add/img008.png)

![img009](/assets/img/2020-04-14-nexus-repository-add/img009.png)

![img010](/assets/img/2020-04-14-nexus-repository-add/img010.png)

### 2) GAV Parameters

![img011](/assets/img/2020-04-14-nexus-repository-add/img011.png)

![img012](/assets/img/2020-04-14-nexus-repository-add/img012.png)

## 3. 새로 생성된 Repository에 추가된 Index 확인

![img013](/assets/img/2020-04-14-nexus-repository-add/img013.png)

## 4. Public Repositories에 추가

![img014](/assets/img/2020-04-14-nexus-repository-add/img014.png)

## 5. Public Repositories에 추가된 Index 확인

![img015](/assets/img/2020-04-14-nexus-repository-add/img015.png)

## 6. 적용

* pom.xml

```xml
<repositories>
    <repository>
        <id>central</id>
        <url>http://서버주소:8081/nexus/content/groups/public</url>
        <releases>
            <enabled>true</enabled>
        </releases>
        <snapshots>
            <enabled>true</enabled>
        </snapshots>
    </repository>
 
    <repository>
        <id>egovframe</id>
        <!-- <url>http://maven.egovframe.kr:8080/maven/</url> -->
        <url>http://서버주소:8081/nexus/content/groups/public</url>
        <releases>
            <enabled>true</enabled>
        </releases>
        <snapshots>
            <enabled>false</enabled>
        </snapshots>
    </repository>
 
    <repository>
        <id>egovframe2</id>
        <!-- <url>http://www.egovframe.go.kr/maven/</url> -->
        <url>http://서버주소:8081/nexus/content/groups/public</url>
        <releases>
            <enabled>true</enabled>
        </releases>
        <snapshots>
            <enabled>false</enabled>
        </snapshots>
    </repository>
 
</repositories>
 
<pluginRepositories>
    <pluginRepository>
        <id>central</id>
        <url>http://서버주소:8081/nexus/content/groups/public</url>
        <releases>
            <enabled>true</enabled>
        </releases>
        <snapshots>
            <enabled>true</enabled>
        </snapshots>
    </pluginRepository>
</pluginRepositories>
```

## [출처 및 참고]
* <http://longbe00.blogspot.com/2015/06/nexus-maven-repository.html>
