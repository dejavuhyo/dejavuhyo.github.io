---
title: CentOS Nexus 설치
author: Hyosik
date: 2020-04-07
categories: [Tools, Nexus]
tags: [centos_nexus, centos_nexus_install, nexus_repository, nexus, sonatype_nexus, centos_nexus_설치, nexus_레파지토리, 넥서스]
---

## 1. jdk 설치

* <https://dejavuhyo.blog.me/221454888121>
* <https://dejavuhyo.blog.me/221469375994>

## 2. 계정 생성
* Nexus를 root 계정으로 설치하는 것을 권장하지 않기 때문에 nexus 계정을 만든다.

```bash
[root@localhost ~]# useradd nexus
[root@localhost ~]# 
[root@localhost ~]# su - nexus
[nexus@localhost ~]$ 
[nexus@localhost ~]$ pwd
/home/nexus
[nexus@localhost ~]$
```

## 3. Nexus 다운로드

```bash
[nexus@localhost ~]$ wget http://www.sonatype.org/downloads/nexus-latest-bundle.tar.gz --no-check-certificate
--2020-04-07 09:03:02--  http://www.sonatype.org/downloads/nexus-latest-bundle.tar.gz
Resolving www.sonatype.org (www.sonatype.org)... 34.196.253.84, 34.205.66.250
Connecting to www.sonatype.org (www.sonatype.org)|34.196.253.84|:80... connected.
HTTP request sent, awaiting response... 301 Moved Permanently
Location: http://download.sonatype.com/nexus/oss/nexus-latest-bundle.tar.gz [following]
 
...
 
HTTP request sent, awaiting response... 200 OK
Length: 82705237 (79M) [application/x-gzip]
Saving to: ‘nexus-latest-bundle.tar.gz’
 
100%[=============================================================================>] 82,705,237   172KB/s   in 7m 51s 
 
2020-04-07 09:10:57 (171 KB/s) - ‘nexus-latest-bundle.tar.gz’ saved [82705237/82705237]
 
[nexus@localhost ~]$
[nexus@localhost ~]$ ls
nexus-latest-bundle.tar.gz
[nexus@localhost ~]$
```

## 4. 압축 해제

```bash
[nexus@localhost ~]$ tar zxvf nexus-latest-bundle.tar.gz 
nexus-2.14.16-01/
nexus-2.14.16-01/nexus/
nexus-2.14.16-01/nexus/LICENSE.html
 
...
 
nexus-2.14.16-01/nexus/WEB-INF/plugin-repository/nexus-ruby-plugin-2.14.16-01/nexus-ruby-plugin-2.14.16-01.jar
nexus-2.14.16-01/nexus/WEB-INF/plugin-repository/nexus-ruby-plugin-2.14.16-01/META-INF/
nexus-2.14.16-01/nexus/WEB-INF/plugin-repository/nexus-ruby-plugin-2.14.16-01/META-INF/MANIFEST.MF
[nexus@localhost ~]$ 
[nexus@localhost ~]$ exit
logout
[root@localhost ~]#
```

## 5. 서비스 등록 및 설정
* 복사 및 편집

```bash
[root@localhost ~]# cp /home/nexus/nexus-2.14.16-01/bin/nexus /etc/init.d/nexus
[root@localhost ~]# vi /etc/init.d/nexus
```

* 설정

```bash
#NEXUS_HOME=".."
NEXUS_HOME="/home/nexus/nexus-2.14.16-01"
 
#RUN_AS_USER=
RUN_AS_USER=nexus
 
#PIDDIR="."
PIDDIR="/home/nexus"
```

## 6. 서비스 시작 및 종료
* 시작

```bash
[root@localhost ~]# service nexus start
Starting Nexus OSS...
Started Nexus OSS.
[root@localhost ~]#
```

* 종료

```bash
[root@localhost ~]# service nexus stop
Stopping Nexus OSS...
Stopped Nexus OSS.
[root@localhost ~]#
```

## 7. 확인

```bash
[root@localhost ~]# ps -ef | grep nexus
nexus    30285     1  0 09:35 ?        00:00:00 /home/nexus/nexus-2.14.16-01/bin/jsw/linux-x86-64/wrapper /home/nexus/nexus-2.14.16-01/bin/jsw/conf/wrapper.conf wrapper.syslog.ident=nexus wrapper.pidfile=/home/nexus/nexus.pid wrapper.daemonize=TRUE
nexus    30287 30285 99 09:35 ?        00:01:12 java -Djava.io.tmpdir=./tmp -Djava.net.preferIPv4Stack=true -Dcom.sun.jndi.ldap.connect.pool.protocol=plain ssl -Xms256m -Xmx768m -Djava.library.path=bin/jsw/lib -classpath bin/jsw/lib/wrapper-3.2.3.jar:./lib/logback-access-1.2.3.jar:./lib/jetty-http-8.1.16.v20140903.jar:./lib/jetty-xml-8.1.16.v20140903.jar:./lib/jetty-security-8.1.16.v20140903.jar:./lib/jetty-client-8.1.16.v20140903.jar:./lib/nexus-bootstrap-2.14.16-01.jar:./lib/plexus-interpolation-1.16.jar:./lib/metrics-core-2.2.0.jar:./lib/jetty-continuation-8.1.16.v20140903.jar:./lib/logback-core-1.2.3.jar:./lib/slf4j-api-1.7.25.jar:./lib/jetty-io-8.1.16.v20140903.jar:./lib/jetty-webapp-8.1.16.v20140903.jar:./lib/jetty-servlet-8.1.16.v20140903.jar:./lib/jul-to-slf4j-1.7.25.jar:./lib/logback-classic-1.2.3.jar:./lib/jetty-server-8.1.16.v20140903.jar:./lib/jetty-rewrite-8.1.16.v20140903.jar:./lib/jetty-deploy-8.1.16.v20140903.jar:./lib/javax.servlet-3.0.0.v201112011016.jar:./lib/jetty-jmx-8.1.16.v20140903.jar:./lib/jetty-util-8.1.16.v20140903.jar:./lib/metrics-jetty-2.2.0.jar:./lib/metrics-logback-2.2.0.jar:./conf/ -Dwrapper.key=dMSUlHe58EfDlloK -Dwrapper.port=32000 -Dwrapper.jvm.port.min=31000 -Dwrapper.jvm.port.max=31999 -Dwrapper.pid=30285 -Dwrapper.version=3.2.3 -Dwrapper.native_library=wrapper -Dwrapper.service=TRUE -Dwrapper.cpu.timeout=10 -Dwrapper.jvmid=1 org.sonatype.nexus.bootstrap.jsw.JswLauncher ./conf/jetty.xml ./conf/jetty-requestlog.xml
root     30456 21608  0 09:35 pts/0    00:00:00 grep --color=auto nexus
[root@localhost ~]#
```

## 8. 방화벽 설정
* Nexus 기본 포트 8081을 열어준다.

```bash
[root@localhost ~]# firewall-cmd --zone=public --permanent --add-port=8081/tcp
success
[root@localhost ~]# firewall-cmd --reload
success
[root@localhost ~]#
```

## 9. 웹 접속
* 접속 주소: http://서버주소:8081/nexus
* 초기 ID/PW: admin / admin123

![img001](/assets/img/2020-04-07-centos-nexus-install/img001.png)

## 출처 및 참고
* <https://zetawiki.com/wiki/CentOS_Nexus_%EC%84%A4%EC%B9%98>
