---
title: Ubuntu에 Docker 이미지로 Jenkins 설치
author: dejavuhyo
date: 2024-09-03 08:40:00 +0900
categories: [DevOps, Jenkins]
tags: [ubuntu-jenkins, jenkins-docker, jenkins-install, 우분투-젠킨스, 젠킨스-도커, 젠킨스-설치]
---

## 1. Jenkins Docker 이미지 pull

```shell
$ sudo docker pull jenkins/jenkins:lts
```

## 2. Docker 사용

### 1) 이미지 실행

```shell
$ sudo docker run --name jenkins -p 8080:8080 -p 50000:50000 --restart=on-failure -v jenkins_home:/var/jenkins_home jenkins/jenkins:lts
```

## 3. Docker Compose 사용

### 1) docker-compose.yml 생성

```yml
version: '3.9'
services:
  jenkins:
    image: jenkins/jenkins:lts
    privileged: true
    container_name: jenkins
    user: root
    ports:
      - "8080:8080"
      - "50000:50000"
    volumes:
      - ./jenkins:/var/jenkins_home
      - /var/run/docker.sock:/var/run/docker.sock
```

### 2) docker-compose 실행

```shell
$ sudo docker-compose up -d
```

## 4. 접속

```text
http://127.0.0.1:8080
```

## 5. 초기 Administrator password 확인

### 1) jenkins 컨테이너 접속

```shell
$ sudo docker exec -it jenkins /bin/bash
```

### 2) Administrator password 확인

```shell
root@12a53e47f651:/# cat /var/jenkins_home/secrets/initialAdminPassword
```

## [출처 및 참고]
* [https://github.com/jenkinsci/docker/blob/master/README.md](https://github.com/jenkinsci/docker/blob/master/README.md)
* [https://dejavuhyo.github.io/posts/install-jenkins-with-docker/](https://dejavuhyo.github.io/posts/install-jenkins-with-docker/)
