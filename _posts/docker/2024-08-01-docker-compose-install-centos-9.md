---
title: CentOS 9에 Docker Compose 설치
author: dejavuhyo
date: 2024-08-01 08:55:00 +0900
categories: [DevOps, Docker]
tags: [centos-docker-compose, docker-compose, docker-compose-install]
---

## 1. Docker Compose 설치

```shell
$ sudo curl -L https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
```

## 2. Docker Compose 실행 권한 부여

```shell
$ sudo chmod +x /usr/local/bin/docker-compose
```

## 3. 설치된 Docker Compose 확인

```shell
$ docker-compose --version
Docker Compose version v2.28.1
```

## [출처 및 참고]
* [https://jsonobject.tistory.com/8](https://jsonobject.tistory.com/8)
* [https://workshop.infograb.io/setup-gitlab/2_install_docker_compose/](https://workshop.infograb.io/setup-gitlab/2_install_docker_compose/)
