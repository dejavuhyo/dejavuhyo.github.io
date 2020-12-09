--- 
title: Linux 시작, 종료 Shell Script
author: Hyosik
date: 2020-11-25 09:45:00 +0900
categories: [OS, Linux]
tags: [linux-shellscript, start-shellscript, stop-shellscript, 시작-쉘스크립트, 종료-쉘스크립트]
---

## 1. 시작 Shell Script

### 1) 파일생성

```shell
[root@localhost ~]# touch start.sh
```

### 2) 편집

```shell
[root@localhost ~]# vi start.sh
```

### 3) 추가
spring-boot-application에 로그 설정이 되어 있으면 nohup을 사용하지 않고 실행

> 로그설정:
>
> <https://dejavuhyo.github.io/posts/spring-boot-logback/>
>
> <https://blog.naver.com/dejavuhyo/222129041829>

```shell
#!/bin/bash

java -jar spring-boot-application.jar &
```

## 2. 종료 Shell Script

### 1) 파일생성

```shell
[root@localhost ~]# touch stop.sh
```

### 2) 편집

```shell
[root@localhost ~]# vi stop.sh
```

### 3) 추가

```shell
#!/bin/bash

APP_PID=$(ps -ef | grep java | grep spring-boot-application.jar | grep -v grep | awk '{print $2}')

kill -KILL $APP_PID
```
