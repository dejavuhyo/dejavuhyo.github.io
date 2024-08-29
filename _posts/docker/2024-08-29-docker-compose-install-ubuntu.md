---
title: Ubuntu에 Docker Compose 설치
author: dejavuhyo
date: 2024-08-29 09:42:00 +0900
categories: [DevOps, Docker]
tags: [ubuntu-docker-compose, docker-compose, 우분투-도커-컴포즈, 도커-컴포즈]
---

## 1. 다운로드 및 설치
Docker Compose를 사용하려면 먼저 Docker Engine을 설치 하고 제대로 실행되는지 확인해야 한다. Docker가 실행되면 Compose를 다운로드하고 YAML 파일을 만들어 애플리케이션을 구성한다.

다음 명령을 입력하여 공식 GitHub 저장소에서 Docker Compose의 최신 버전을 다운로드한다.

```shell
$ sudo curl -L https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
```

## 2. 실행 권한 부여

```shell
$ sudo chmod +x /usr/local/bin/docker-compose
```

## 3. 설치 확인

```shell
$ docker-compose --version
```

## [출처 및 참고]
* [https://medium.com/@tomer.klein/step-by-step-tutorial-installing-docker-and-docker-compose-on-ubuntu-a98a1b7aaed0](https://medium.com/@tomer.klein/step-by-step-tutorial-installing-docker-and-docker-compose-on-ubuntu-a98a1b7aaed0)
* [https://www.ionos.com/digitalguide/server/configuration/docker-compose-on-ubuntu/](https://www.ionos.com/digitalguide/server/configuration/docker-compose-on-ubuntu/)
