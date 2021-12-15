---
title: CentOS 7에 OpenJDK 설치 및 환경변수 설정
author: dejavuhyo
date: 2019-01-30 17:45:00 +0900
categories: [DevOps, Linux]
tags: [centos-openjdk-install, openjdk-install, centos-java-home, centos-openjdk-설치, openjdk-설치, java-환경변수]
---

## 1. 설치환경

* CentOS 7 (VMWare)

* OpenJDK 1.8

## 2. 설치된 JDK 확인

```bash
[root@localhost ~]# rpm -qa | grep jdk
```

## 3. 설치된 JDK 삭제

```bash
[root@localhost ~]# yum remove java*
```

## 4. 설치 가능한 JDK 버전 확인

```bash
[root@localhost ~]# yum list java*jdk-devel
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: ftp.kaist.ac.kr
 * extras: ftp.kaist.ac.kr
 * updates: ftp.kaist.ac.kr
Available Packages
java-1.6.0-openjdk-devel.x86_64                     1:1.6.0.41-1.13.13.1.el7_3                     base   
java-1.7.0-openjdk-devel.x86_64                     1:1.7.0.201-2.6.16.1.el7_6                     updates
java-1.8.0-openjdk-devel.i686                       1:1.8.0.191.b12-1.el7_6                        updates
java-1.8.0-openjdk-devel.x86_64                     1:1.8.0.191.b12-1.el7_6                        updates
java-11-openjdk-devel.i686                          1:11.0.1.13-3.el7_6                            updates
java-11-openjdk-devel.x86_64                        1:11.0.1.13-3.el7_6                            updates
```

## 5. 설치

```bash
[root@localhost ~]# yum install java-1.8.0-openjdk-devel.x86_64
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: ftp.neowiz.com
 * extras: ftp.neowiz.com
 * updates: ftp.neowiz.com
Resolving Dependencies
--> Running transaction check
---> Package java-1.8.0-openjdk-devel.x86_64 1:1.8.0.191.b12-1.el7_6 will be installed
--> Finished Dependency Resolution
 
Dependencies Resolved
 
==========================================================================================================
 Package                          Arch           Version                            Repository       Size
==========================================================================================================
Installing:
 java-1.8.0-openjdk-devel         x86_64         1:1.8.0.191.b12-1.el7_6            updates         9.8 M
 
Transaction Summary
==========================================================================================================
Install  1 Package
 
Total download size: 9.8 M
Installed size: 40 M
Is this ok [y/d/N]: y
Downloading packages:
java-1.8.0-openjdk-devel-1.8.0.191.b12-1.el7_6.x86_64.rpm                          | 9.8 MB  00:00:03     
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : 1:java-1.8.0-openjdk-devel-1.8.0.191.b12-1.el7_6.x86_64                                1/1 
  Verifying  : 1:java-1.8.0-openjdk-devel-1.8.0.191.b12-1.el7_6.x86_64                                1/1 
 
Installed:
  java-1.8.0-openjdk-devel.x86_64 1:1.8.0.191.b12-1.el7_6                                                 
 
Complete!
```

## 6. 설치 확인

```bash
[root@localhost ~]# javac -version
javac 1.8.0_191
```

```bash
[root@localhost ~]# rpm -qa java*jdk-devel
java-1.8.0-openjdk-devel-1.8.0.191.b12-1.el7_6.x86_64
```

## 7. JAVA_HOME 설정 확인

* 설정되어 있지 않음

```bash
[root@localhost ~]# echo $JAVA_HOME
 
[root@localhost ~]#
```

## 8. javac 위치 확인

### 1) javac 명령어의 위치 확인

```bash
[root@localhost ~]# which javac
/usr/bin/javac
```

## 2) 원본 파일 위치 확인
```/usr/bin/javac```는 심볼릭 링크 이므로, 원본 파일 위치 확인

```bash
[root@localhost ~]# readlink -f /usr/bin/javac
/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.191.b12-1.el7_6.x86_64/bin/javac
```

## 9. JAVA_HOME 설정

### 1) 환경변수 등록
javac의 원본파일 경로를 이용하여 ```JAVA_HOME``` 환경변수 등록

```bash
[root@localhost ~]# vi /etc/profile
```

### 2) profile 파일 편집
환경변수를 설정하는 profile 파일 편집

```bash
[root@localhost ~]# vi /etc/profile
```

* 제일 아랫줄에 아래 내용 추가

```text
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.191.b12-1.el7_6.x86_64
```

### 3) profile 적용

```bash
[root@localhost ~]# source /etc/profile
```

## 10. JAVA_HOME 확인

```bash
[root@localhost ~]# echo $JAVA_HOME
/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.191.b12-1.el7_6.x86_64
```

```bash
[root@localhost ~]# $JAVA_HOME/bin/javac -version
javac 1.8.0_191
```

## [출처 및 참고]
* <https://blog.hanumoka.net/2018/04/30/centOs-20180430-centos-install-jdk/>
