---
title: Docker Compose를 이용하여 애플리케이션 설치
author: dejavuhyo
date: 2021-05-20 05:00:00 +0900
categories: [DevOps, Docker]
tags: [docker-compose, docker, 도커-컴포즈, 도커, docker-compose-애플리케이션-설치]
---

## 1. Compose 파일에 설치 서비스 정의

### 1) Jenkins, GitLab 설정

* docker-compose.yml

```yaml
version: "3.9"
services:
  jenkins:
    image: 'jenkins/jenkins:latest'
    restart: always
    container_name: 'jenkins'
    ports:
      - '9080:8080'
      - '50000:50000'
    volumes:
      - '/var/jenkins_home/data:/var/jenkins_home'
      - '/var/run/docker.sock:/var/run/docker.sock'
    environment:
      TZ: "Asia/Seoul"

  gitlab:
    image: 'gitlab/gitlab-ce:latest'
    restart: always
    container_name: 'gitlab'
    hostname: '127.0.0.1'
    environment:
      GITLAB_OMNIBUS_CONFIG: |
        external_url 'http://127.0.0.1:8929'
        gitlab_rails['gitlab_shell_ssh_port'] = 2224
    ports:
      - '8929:8929'
      - '2224:22'
    volumes:
      - '/srv/gitlab/config:/etc/gitlab'
      - '/srv/gitlab/logs:/var/log/gitlab'
      - '/srv/gitlab/data:/var/opt/gitlab'
```

## 2. 실행

### 1) 서비스 실행

```bash
[root@localhost ~]# docker-compose up
```

### 2) 백그라운드 실행

```bash
[root@localhost ~]# docker-compose up -d
```

### 3) 실행중인 항목 확인

```bash
[root@localhost ~]# docker-compose ps
```

### 4) 컨테이너 중지

```bash
[root@localhost ~]# docker-compose stop
```

## 3. 접속

### 1) Jenkins

* <http://localhost:9080>

![jenkins](/assets/img/2021-05-20-install-application-using-docker-compose/jenkins.png)

### 2) GitLab

* <http://localhost:8929>

![gitlab](/assets/img/2021-05-20-install-application-using-docker-compose/gitlab.png)

## [출처 및 참고]
* <https://docs.docker.com/compose/gettingstarted/>
* <https://docs.gitlab.com/omnibus/docker/>
* <https://docs.docker.com/compose/compose-file/compose-file-v3/>
