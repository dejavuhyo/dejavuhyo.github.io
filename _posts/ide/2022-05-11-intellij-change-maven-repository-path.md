---
title: IntelliJ Maven Repository 경로 변경
author: dejavuhyo
date: 2022-05-11 17:20:00 +0900
categories: [DevOps, IDE]
tags: [intellij-change-maven-repository, intellij-maven, maven-repository, intellij-maven-repository, 인텔리제이-메이븐-레파지토리, 메이븐-레파지토리-변경]
---

## 1. settings.xml 파일
Maven은 maven 설정과 관련한 모든 정보를 settings.xml 이라는 파일로 관리하고 있다.

이 파일에서 여러가지 설정을 해 줄 수 있고, 그 설정중에는 local repository path 를 지정해줄 수 있다. [Apache Maven](https://maven.apache.org/settings.html)에서 관련 내용을 확인할 수 있다.

### 1) settings.xml 참고

```xml
<?xml version="1.0"?>

<settings xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://maven.apache.org/SETTINGS/1.0.0">

    <mirrors>
        <mirror>
            <id>Nexus</id>
            <mirrorOf>*</mirrorOf>
            <name>Nexus repository</name>
            <url>http://localhost:8081/nexus/content/groups/public/</url>
        </mirror>
    </mirrors>

    <servers>
        <server>
            <id>Nexus</id>
            <username>admin</username>
            <password>admin123</password>
        </server>
    </servers>
    <profiles>
        <profile>
            <activation>
                <activeByDefault>true</activeByDefault>
            </activation>
            <repositories>
                <repository>
                    <id>Nexus</id>
                    <name>Nexus Public Repository</name>
                    <url>http://localhost:8081/nexus/content/groups/public/</url>
                    <releases>
                        <enabled>true</enabled>
                    </releases>
                    <snapshots>
                        <enabled>true</enabled>
                        <updatePolicy>always</updatePolicy>
                    </snapshots>
                </repository>
            </repositories>
        </profile>
    </profiles>
</settings>
```

### 2) IntelliJ Maven 설정

* 경로: File → Settings

`User settings file`에서 settings.xml 경로를 설정하고 Override를 체크한다.

repository 경로를 변경하려면 `Local repository`에서 repository 경로를 설정하고 Override를 체크한다.


![initellij-maven](/assets/img/2022-05-11-intellij-change-maven-repository-path/initellij-maven.png)

## [출처 및 참고]
* [https://4urdev.tistory.com/75](https://4urdev.tistory.com/75)
* [https://maven.apache.org/settings.html](https://maven.apache.org/settings.html)
