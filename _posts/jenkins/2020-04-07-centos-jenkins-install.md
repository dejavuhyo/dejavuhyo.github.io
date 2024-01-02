---
title: CentOS Jenkins 설치
author: dejavuhyo
date: 2020-04-07 12:00:00 +0900
categories: [DevOps, Jenkins]
tags: [centos-jenkins, centos-jenkins-install, jenkins-install, jenkins-설치, 젠킨스, 젠킨스-설치]
---

## 1. Jenkins 설치 확인

```shell
[root@localhost ~]# yum list installed jenkins
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirror.navercorp.com
 * extras: mirror.navercorp.com
 * updates: mirror.navercorp.com
Error: No matching Packages to list
[root@localhost ~]#
```

## 2. Jenkins 다운로드

* /etc/yum.repos.d/jenkins.repo 경로 다운로드

```shell
[root@localhost ~]# wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
--2020-04-07 10:31:42--  https://pkg.jenkins.io/redhat-stable/jenkins.repo
Resolving pkg.jenkins.io (pkg.jenkins.io)... 52.202.51.185
Connecting to pkg.jenkins.io (pkg.jenkins.io)|52.202.51.185|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 85
Saving to: ‘/etc/yum.repos.d/jenkins.repo’
 
100%[==============================================================================>] 85          --.-K/s   in 0s      
 
2020-04-07 10:31:47 (5.78 MB/s) - ‘/etc/yum.repos.d/jenkins.repo’ saved [85/85]
 
[root@localhost ~]#
```

## 3. Key Import

```shell
[root@localhost ~]# rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
[root@localhost ~]#
```

## 4. 설치

```shell
[root@localhost ~]# yum install jenkins
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 
...
 
Installed:
  jenkins.noarch 0:2.222.1-1.1                                                                                                                                                                               
 
Complete!
[root@localhost ~]#
```

## 5. Jenkins 설정
Jenkins는 기본 포트 8080으로 설정되어 있어 Tomcat과 충돌하기 때문에 9090으로 변경한다.

* 편집

```shell
[root@localhost ~]# vi /etc/sysconfig/jenkins
```

* 수정

```text
JENKINS_PORT="9090"
```

## 6. 서비스 시작 및 종료
* 시작

```shell
[root@localhost ~]# service jenkins start
Starting jenkins (via systemctl):                          [  OK  ]
[root@localhost ~]#
```

* 종료

```shell
[root@localhost ~]# service jenkins stop
Stopping jenkins (via systemctl):                          [  OK  ]
[root@localhost ~]#
```

## 7. 방화벽 설정
설정한 포트 9090을 열어준다.

```shell
[root@localhost ~]# firewall-cmd --zone=public --permanent --add-port=9090/tcp
success
[root@localhost ~]# firewall-cmd --reload
success
[root@localhost ~]#
```

## 8. 웹 접속
* **접속 주소:** `http://서버주소:9090`
* **초기 비밀번호:**  /var/lib/jenkins/secrets/initialAdminPassword에 있음

```shell
[root@localhost ~]# vi /var/lib/jenkins/secrets/initialAdminPassword
```

![img001](/assets/img/2020-04-07-centos-jenkins-install/img001.png)

## [출처 및 참고]
* [https://cornswrold.tistory.com/330](https://cornswrold.tistory.com/330)
