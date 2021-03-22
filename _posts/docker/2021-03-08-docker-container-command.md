---
title: 실행 중인 Docker Container 명령 실행
author: dejavuhyo
date: 2021-03-08 06:00:00 +0900
categories: [DevOps, Docker]
tags: [docker-dontainer-command, dontainer-command, docker-dontainer, 도커-컨테이너-명령, 컨테이너-명령, 도커-컨테이너]
---

## 1. docker exec 설명
실행 중인 Docker Container에 명령 실행

![docker](/assets/img/2021-03-08-docker-container-command/docker.png)

## 2. 실행 중인 docker 이미지 확인

```bash
[root@localhost ~]# docker ps -a
```

## 3. Container 접속

```bash
[root@localhost ~]# docker exec -it [CONTAINER_ID or NAMES] /bin/bash
```

## 4. 옵션

| 옵션 | 설명 |
|:---:|:---:|
| --detach , -d | 분리 모드: 백그라운드에서 명령 실행 |
| --detach-keys | 컨테이너를 분리하기 위한 키 시퀀스 재정의 |
| --env , -e | 환경 변수 설정 |
| --env-file | 환경 변수 파일에서 읽기 |
| --interactive , -i | 부착되지 않은 경우에도 SDDIN을 열어 둔다 |
| --privileged | 명령에 확장 권한 부여 |
| --tty , -t | pseudo-TTY 할당 |
| --user , -u | 사용자 이름 또는 UID (형식: ```<name|uid>[:<group|gid>])``` |
| --workdir , -w | 컨테이너 내부의 작업 디렉터리 |

## [출처 및 참고]
* <https://docs.docker.com/engine/reference/commandline/exec/>
