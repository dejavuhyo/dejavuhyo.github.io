---
title: 실행 중인 Container 명령 실행
author: dejavuhyo
date: 2021-03-08 06:00:00 +0900
categories: [DevOps, Docker]
tags: [docker-dontainer-command, dontainer-command, docker-dontainer, 도커-컨테이너-명령, 컨테이너-명령, 도커-컨테이너]
---

## 1. docker exec 설명
실행 중인 Container에 명령 실행

![docker](/assets/img/2021-03-08-docker-container-command/docker.png)

## 2. 실행 중인 docker 이미지 확인

```bash
docker ps -a
```

## 3. Container 접속

```bash
docker exec -it [CONTAINER_ID or NAMES] /bin/bash
```

## [출처 및 참고]
* <https://docs.docker.com/engine/reference/commandline/exec/>
* <https://itholic.github.io/docker-enter-container/>
