---
title: Tomcat 인코딩
author: dejavuhyo
date: 2021-09-01 06:00:00 +0900
categories: [DevOps, Tomcat]
tags: [tomcat-encoding, encoding, tomcat-인코딩, 인코딩]
---

## 1. Tomcat UTF-8 인코딩 문제

![nslookup](/assets/img/2021-09-01-tomcat-encoding/tomcat.png)

### 1) sever.xml 수정
`apache-tomcat-9.0.52\conf\server.xml`에서 `URIEncding="UTF-8"` 속성을 추가한다.

```xml
<Connector port="8080" protocol="HTTP/1.1"
           connectionTimeout="20000"
           redirectPort="8443"
           URIEncoding="UTF-8" />
```

```xml
<Connector protocol="AJP/1.3"
           address="::1"
           port="8009"
           redirectPort="8443"
           URIEncoding="UTF-8" />
```

### 2) web.xml 수정
`apache-tomcat-9.0.52\conf\web.xml`에서 encoding 필터를 추가한다.

```xml
<filter>
    <filter-name>setCharacterEncodingFilter</filter-name>
    <filter-class>org.apache.catalina.filters.SetCharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
    <async-supported>true</async-supported>
</filter>
<filter-mapping>
    <filter-name>setCharacterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

## [출처 및 참고]
* [https://itworldyo.tistory.com/105](https://itworldyo.tistory.com/105)
