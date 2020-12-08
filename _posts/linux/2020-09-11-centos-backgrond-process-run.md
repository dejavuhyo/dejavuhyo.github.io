---
title: CentOS Background 프로세스 실행
author: Hyosik
date: 2020-09-11 14:00:00 +0900
categories: [OS, Linux]
tags: [background-process-run, background-process, nohup, 백그라운드-프로세스-실행, 백그라운드-프로세스]
---

## 1. Background 실행
프로세스를 백그라운드(background)로 실행하기 위해서는 일반적으로 뒤에 & 붙여 실행한다.

```shell
[root@localhost ~]# java -jar spring-boot-application.jar &
```

&로 실행하면 shell이 종료되거나 사용자가 로그아웃을 하게 되면 프로세스가 중단(hangup) 된다.

## 2. nohup Background 실행
프로그램을 Background에서 지속적으로 실행되고 있는 상태로 만들기 위해서 nohup 명령을 사용한다.

```shell
[root@localhost ~]# nohup java -jar spring-boot-application.jar &
```

## 3. 로그 출력
프로세스가 실행되면 console에 출력되는 내용은 기본적으로 nohup.out 이라는 파일로 출력 된다.

### 1) 다른 파일명으로 출력

```shell
[root@localhost ~]# nohup java -jar spring-boot-application.jar > log.out
```

### 2) 출력하지 않음

```shell
[root@localhost ~]# nohup java -jar spring-boot-application.jar > /dev/null
```

## 4. 실행중인 프로세스 종료

### 1) 실행중인 프로세스 찾기

```shell
[root@localhost ~]# ps -ef | grep spring-boot-application
```

### 2) 프로세스 종료

```shell
[root@localhost ~]# kill -9 {PID}
```

## 출처 및 참고
* <https://dptablo.tistory.com/236>
