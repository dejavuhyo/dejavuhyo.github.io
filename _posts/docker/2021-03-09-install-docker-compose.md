---
title: Linux에 Docker Compose 설치
author: dejavuhyo
date: 2021-03-09 06:05:00 +0900
categories: [DevOps, Docker]
tags: [docker-compose-install, docker-compose, compose-install, 도커-compose]
---

## 1. 설치
Docker Compose는 작업을 위해 Docker Engine을 사용하므로 로컬 또는 원격에 Docker Engine이 설치되어 있어야 한다.

![docker-compose](/assets/img/2021-03-09-install-docker-compose/docker-compose.png)

### 1) Docker Compose 다운로드

```bash
[root@localhost ~]# sudo curl -L "https://github.com/docker/compose/releases/download/1.28.5/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

### 2) 실행 권한 적용

```bash
[root@localhost ~]# chmod +x /usr/local/bin/docker-compose
```

### 3) 심볼릭 링크 설정

```bash
[root@localhost ~]# ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
```

### 4) 설치 확인

```bash
[root@localhost ~]# docker-compose --version
docker-compose version 1.28.5, build c4eb3a1f
```

## 2. 업그레이드
Composite 1.2 이전 버전에서 업그레이드하는 경우 Composite를 업그레이드한 후 기존 컨테이너를 제거하거나 마이그레이션한다. 그 이유는 1.3 버전부터는 Composite가 Docker 레이블을 사용하여 컨테이너를 추적하고, 레이블을 추가하려면 컨테이너를 다시 만들어야 하기 때문이다.

Composite가 레이블 없이 생성된 컨테이너를 탐지하면 실행을 거부하여 두 세트의 레이블이 생성되지 않는다. 기존 컨테이너를 계속 사용하려면(보존하려는 데이터 볼륨이 있기 때문) Composite 1.5.x를 사용하여 다음 명령을 사용하여 컨테이너를 마이그레이션 할 수 있다.

```bash
[root@localhost ~]# docker-compose migrate-to-labels
```

## 3. 제거 
Docker Compose를 제거

```bash
[root@localhost ~]# rm /usr/local/bin/docker-compose
```

## 출처 및 참고
* <https://docs.docker.com/compose/install/>
