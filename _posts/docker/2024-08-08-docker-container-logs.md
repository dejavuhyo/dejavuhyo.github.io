---
title: Docker 컨테이너 로그
author: dejavuhyo
date: 2024-08-08 09:28:00 +0900
categories: [DevOps, Docker]
tags: [docker-container-logs, docker-logs, container-logs, 도커-컨테이너-로그, 도커-로그, 컨테이너-로그]
---

## 1. docker logs 명령
Docker는 로그를 보기 위해 두 가지 유형의 로그 명령을 제공한다.

* `docker logs`

* `docker service logs`

`docker logs`명령은 컨테이너 내부에서 생성된 로그 메시지를 보여주고, 이 docker service logs명령은 컨테이너 내의 서비스에서 생성된 로그를 보여준다.

```shell
$ docker logs <options> <containerID>
```

## 2. Docker 컨테이너 로그 옵션

### 1) 실시간 로그 보기

```shell
$ docker logs -f <containerID>
```

### 2) 마지막 N개 로그 보기

```shell
docker logs <containerID> --tail <last_number-of_lines>
```

### 3) error 명령을 사용하여 오류 보기

```shell
$ docker logs <containerID> | grep -i error
```

### 4) 특정 로그 보기

```shell
$ docker logs <containerID> | grep <pattern>
```

### 5) 특정 시간 범위 내의 로그 보기

```shell
docker logs --since 10h <containerID> 
```

### 6) 마지막 지정된 시간 내에 생성된 로그 보기
다음 명령은 마지막 2시간 동안 생성된 로그를 제외한 전체 로그 추적을 출력한다.

```shell
$ docker logs  <containerID> --until 2h 
```

### 7) 시간 범위 내에서 로그 보기
다음 명령은 2023년 8월 3일 오전 10시와 2023년 8월 4일 오전 9시에 생성된 로그를 보여준다.

```shell
$ docker logs <containerID>  --since 2023-08-03T10:00:00 --until 2023-08-04T09:00:00
```

## [출처 및 참고]
* [https://www.cherryservers.com/blog/docker-container-logs](https://www.cherryservers.com/blog/docker-container-logs)
