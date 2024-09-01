---
title: Ubuntu에 Docker 이미지로 GitLab 설치
author: dejavuhyo
date: 2024-09-02 08:33:00 +0900
categories: [DevOps, Git]
tags: [ubuntu-gitlab, gitlab-docker, gitlab-install, 우분투-깃랩, 깃랩-도커, 깃랩-설치]
---

## 1. GitLab Docker 이미지 pull
GitLab 최신 안정된 릴리즈 `latest` 이미지를 pull 한다.

```shell
$ sudo docker pull gitlab/gitlab-ce:latest
```

## 2. Docker 사용

### 1) 이미지 실행

```shell
$ sudo docker run --detach \
    --hostname 127.0.0.1 \
    --env GITLAB_OMNIBUS_CONFIG="external_url 'http://127.0.0.1'" \
    --publish 443:443 --publish 80:80 --publish 2222:22 \
    --name gitlab \
    --restart always \
    --volume ~/gitlab/config:/etc/gitlab \
    --volume ~/gitlab/logs/var/log/gitlab \
    --volume ~/gitlab/data:/var/opt/gitlab \
    --volume ~/gitlab/backups:/var/opt/gitlab/backups \
    gitlab/gitlab-ce:latest
```

## 3. Docker Compose 사용

### 1) docker-compose.yml 생성

```yml
version: '3.9'
services:
  gitlab:
    image: gitlab/gitlab-ce:latest
    container_name: gitlab
    restart: always
    hostname: '127.0.0.1'
    environment:
      GITLAB_OMNIBUS_CONFIG: |
        # Add any other gitlab.rb configuration here, each on its own line
        external_url 'http://127.0.0.1:8880'
        gitlab_rails['gitlab_shell_ssh_port'] = 2224
        nginx['listen_port'] = 80
        nginx['enable'] = true
        nginx['redirect_http_to_https'] = true
        registry_nginx['redirect_http_to_https'] = true
        mattermost_nginx['redirect_http_to_https'] = true
    ports:
      - '80:80'
      - '443:443'
      - '2222:22'
    volumes:
      - '~/gitlab/config:/etc/gitlab'
      - '~/gitlab/logs:/var/log/gitlab'
      - '~/gitlab/data:/var/opt/gitlab'
      - '~/gitlab/backups:/var/opt/gitlab/backups'
```

### 2) docker-compose 실행

```shell
$ sudo docker-compose up -d
```

## 4. GitLab 접속

```text
http://127.0.0.1
```

### 1) 초기 root 비밀번호 확인
초기 비밀번호로 로그인 후 새로운 비밀번호로 변경한다.

```shell
$ sudo docker exec -it gitlab grep 'Password' /etc/gitlab/initial_root_password
```

## [출처 및 참고]
* [https://shape.host/resources/how-to-install-gitlab-with-docker-on-ubuntu-22-04](https://shape.host/resources/how-to-install-gitlab-with-docker-on-ubuntu-22-04)
