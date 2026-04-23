---
title: Ubuntu에 Docker Compose 설치
author: dejavuhyo
date: 2024-08-29 09:42:00 +0900
categories: [DevOps, Docker]
tags: [ubuntu-docker-compose, docker-compose, 우분투-도커-컴포즈, 도커-컴포즈]
---

## 1. 패키지 인덱스 업데이트 및 Docker Compose 최신 버전 설치

Ubuntu 및 Debian의 경우 다음 명령을 실행한다.

```bash
$ sudo apt-get update
$ sudo apt-get install docker-compose-plugin
```

RPM 기반 배포판의 경우 다음 명령을 실행한다.

```bash
$ sudo yum update
$ sudo yum install docker-compose-plugin
```

## 2. 설치 확인

```bash
$ docker compose version
```

## 3. Docker Compose 업데이트
Docker Compose 플러그인을 업데이트하려면 다음 명령을 실행한다.

Ubuntu 및 Debian의 경우 다음 명령을 실행한다.

```bash
$ sudo apt-get update
$ sudo apt-get install docker-compose-plugin
```

RPM 기반 배포판의 경우 다음 명령을 실행한다.

```bash
$ sudo yum update
$ sudo yum install docker-compose-plugin
```

## [출처 및 참고]
* [https://docs.docker.com/engine/install/ubuntu/](https://docs.docker.com/engine/install/ubuntu/)
