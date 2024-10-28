---
title: Tomcat 서브도메인 설정
author: dejavuhyo
date: 2024-10-28 10:13:00 +0900
categories: [DevOps, Tomcat]
tags: [tomcat, tomcat-subdomain, subdomain, 톰캣, 톰캣-서브도메인, 서브도메인]
---

![apache-tomcat-logo](/assets/img/2024-10-28-tomcat-subdomain-setting/apache-tomcat-logo.jpg)

## 1. server.xml에 Host 추가
`$CATALINA_HOME/conf/server.xml`를 편집한다.

```shell
$ sudo vi server.xml
```

* server.xml

```xml
...
<Engine name="Catalina" defaultHost="localhost">

    <!-- 기본 호스트 -->
    <Host name="domain.com"  appBase="webapps"
            unpackWARs="true" autoDeploy="true">
    </Host>

    <!-- 서브도메인 호스트 추가 -->
    <Host name="sub.domain.com"  appBase="sub_webapps"
            unpackWARs="true" autoDeploy="true">
    </Host>

</Engine>
...
```

* name: 추가할 서브도메인명

* appBase: 웹어플리케이션이 위치할 디렉터리 ($CATALINA_HOME 기준 디렉터리패스 설정)
  - 예) 위의 `server.xml` 설정에서 `domain.com` 디렉터리는 `$CATALINA_HOME/webapps`이고 `sub.domain.com` 디렉터리는 `$CATALINA_HOME/sub_webapps`이다.

## 2. 서브도메인 호스트 컨텍스트 설정
`$CATALINA_HOME/conf/Catalina` 하위에 추가한 서브도메인명과 동일한 폴더를 만든 후 `ROOT.xml`을 추가한다.

* ROOT.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Context docBase="www/webapps/sub" privileged="true">
</Context>
```

* 설정된 디렉터리 구조

```text
$CATALINA_HOME
    ㄴ conf
        ㄴ Catalina
            ㄴ sub.domain.com
                ㄴ ROOT.xml
        ㄴ server.xml
    ㄴ webapps
        ㄴ ROOT
            ㄴ www.domain.com 웹어플리케이션
    ㄴ sub
        ㄴ ROOT
            ㄴ sub.domain.com 웹어플리케이션
```

## [출처 및 참고]
* [https://thereclub.tistory.com/11](https://thereclub.tistory.com/11)
