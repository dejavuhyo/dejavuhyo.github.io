---
title: Nexus 내부 저장소 설정
author: dejavuhyo
date: 2020-04-13 17:00:00 +0900
categories: [DevOps, Nexus]
tags: [nexus-repository, nexus-public-repositories, nexus-repository-setting, nexus-maven, 넥서스-저장소, 내부-저장소, 넥서스-메이븐, 넥서스-저장소-설정]
---

## 1. Repositories 설정

### 1) Public Repositories 설정

* Public Repositories → Configuration → Ordered Group Repositories 설정
 
![img001](/assets/img/2020-04-13-nexus-private-repository-setting/img001.png)

### 2) Central 설정
Remote Storage Location에 설정된 https://repo1.maven.org/maven2/ 에서 Index 파일을 download 하여 나의 서버에 Index를 만든다.

* Central → Configuration → Download Remote Indexes 설정

![img002](/assets/img/2020-04-13-nexus-private-repository-setting/img002.png)
 
## 2. Scheduled Tasks 확인
Central 레파지토리의 index 진행 완료 여부를 알 수 있다. Index 하는데 시간이 좀 걸린다.

![img003](/assets/img/2020-04-13-nexus-private-repository-setting/img003.png)

## 3. Index 결과 확인

![img004](/assets/img/2020-04-13-nexus-private-repository-setting/img004.png)

![img005](/assets/img/2020-04-13-nexus-private-repository-setting/img005.png)

## 4. 적용
url 부분을 Nexus의 Public Repositories의 Repository Path로 설정

![img006](/assets/img/2020-04-13-nexus-private-repository-setting/img006.png)

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
* <http://blog.naver.com/PostView.nhn?blogId=pky0706&logNo=140146609820>
