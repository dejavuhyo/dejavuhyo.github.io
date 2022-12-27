---
title: Tomcat Manager 설정 (403 Access Denied)
author: dejavuhyo
date: 2020-04-09 13:00:00 +0900
categories: [DevOps, Tomcat]
tags: [tomcat-manager, tomcat-403, tomcat-access-denied, tomcat-403-error]
---

Jenkins에서 war 파일 배포를 위해 manager 접속이 필요하다. manager 기능은 보안 문제 때문에 기본적으로 막혀 있다.
설정 없이 접속을 하면 403 Access Denied 에러가 나기 때문에 아래 설정이 필요하다.

## 1. conf 디렉터리 tomcat-users.xml 설정

* 편집

```shell
[root@localhost apache-tomcat-9.0.34]# vi conf/tomcat-users.xml
```

* 추가

```xml
<tomcat-users xmlns="http://tomcat.apache.org/xml"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://tomcat.apache.org/xml tomcat-users.xsd"
              version="1.0">
 
<role rolename="admin"/>
<role rolename="admin-gui"/>
<role rolename="admin-script"/>
<role rolename="manager"/>
<role rolename="manager-gui"/>
<role rolename="manager-script"/>
<role rolename="manager-jmx"/>
<role rolename="manager-status"/>
<user username="유저명" password="비밀번호" roles="admin,manager,admin-gui,admin-script,manager-gui,manager-script,manager-jmx,manager-status" />
 
</tomcat-users>
```

## 2. manager 디렉터리 context.xml 설정

* 편집

```shell
[root@localhost apache-tomcat-9.0.34]# vi webapps/manager/META-INF/context.xml
```

* 설정

```xml
<Context antiResourceLocking="false" privileged="true" >

  <Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow=".*" />

  <Manager sessionAttributeValueClassNameFilter="java\.lang\.(?:Boolean|Integer|Long|Number|String)|org\.apache\.catalina\.filters\.CsrfPreventionFilter\$LruCache(?:\$1)?|java\.util\.(?:Linked)?HashMap"/>
</Context>
```

## 3. 접속 확인

* http://서버주소:8080/manager/html

![img001](/assets/img/2020-04-09-tomcat-manager/img001.png)

## [출처 및 참고]
* <https://cpdev.tistory.com/110>
