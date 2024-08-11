---
title: Docker 컨테이너 안에 Docker 설치
author: dejavuhyo
date: 2024-08-12 08:50:00 +0900
categories: [DevOps, Docker]
tags: [docker-container, docker-in-docker, docker, docker-install]
---

## 1. Docker Jenkins Container 접속
Jenkins에서 Docker 빌드를 위해 Jenkins Container를 예로 들었다.

Jenkins에 Docker 관련 플러그인은 설치하였으나, Jenkins Container 안에는 Docker가 설치되어 있지 않다.

```shell
$ sudo docker exec -it jenkins /bin/bash
```

## 2. Docker 설치를 위한 Shell 다운로드

```shell
root@dadfe2ea194f:/# curl -fsSL get.docker.com -o get-docker.sh
```

## 3. Docker install shell script 실행

```shell
root@dadfe2ea194f:/# sh get-docker.sh
```

## 4. Docker 버전 확인

```shell
root@dadfe2ea194f:/# docker --version
```

## [출처 및 참고]
* [https://teichae.tistory.com/entry/Jenkins-Pipeline%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-Docker-Image-Build](https://teichae.tistory.com/entry/Jenkins-Pipeline%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-Docker-Image-Build)
