---
title: CentOS 9에 Docker 이미지로 Jenkins 설치
author: dejavuhyo
date: 2024-08-05 09:00:00 +0900
categories: [DevOps, Jenkins]
tags: [centos-jenkins, jenkins-docker, jenkins-install, 젠킨스-도커, 젠킨스-설치]
---

## 1. Jenkins Docker 이미지 pull

```shell
$ docker pull jenkins/jenkins:lts
```

## 2. Docker 사용

### 1) 이미지 실행

```shell
$ docker run --name jenkins -p 8080:8080 -p 50000:50000 --restart=on-failure -v jenkins_home:/var/jenkins_home jenkins/jenkins:lts
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
$ docker-compose up -d
```

## 4. 접속

```text
http://127.0.0.1:8080
```

## 5. 초기 Administrator password 확인

### 1) jenkins 컨테이너 접속

```shell
$ docker exec -it jenkins /bin/bash
```

### 2) Administrator password 확인

```shell
root@12a53e47f651:/# cat /var/jenkins_home/secrets/initialAdminPassword
```

## 6. Permission denied 오류

* 에러 메시지

```text
touch: cannot touch '/var/jenkins_home/copy_reference_file.log': Permission denied
Can not write to /var/jenkins_home/copy_reference_file.log. Wrong volume permissions?
```

* 해결 방법

```shell
$ chown -R 1000:1000 /var/jenkins_home/
```

## [출처 및 참고]
* [https://github.com/jenkinsci/docker/blob/master/README.md](https://github.com/jenkinsci/docker/blob/master/README.md)
* [https://dejavuhyo.github.io/posts/install-jenkins-with-docker/](https://dejavuhyo.github.io/posts/install-jenkins-with-docker/)
