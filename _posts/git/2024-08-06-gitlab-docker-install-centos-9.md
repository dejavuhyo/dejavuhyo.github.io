---
title: CentOS 9에 Docker 이미지로 GitLab 설치
author: dejavuhyo
date: 2024-08-06 10:32:00 +0900
categories: [DevOps, Git]
tags: [centos-gitlab, gitlab-docker, gitlab-docker-compose, gitlab-install, 깃랩-도커, 깃랩-설치]
---

## 1. GitLab Docker 이미지 pull
GitLab 최신 안정된 릴리즈 `latest` 이미지를 pull 한다.

```shell
$ docker pull gitlab/gitlab-ce:latest
```

## 2. Docker 사용

### 1) 이미지 실행

```shell
$ sudo docker run --detach \
    --hostname 127.0.0.1 \
    --env GITLAB_OMNIBUS_CONFIG="external_url 'http://127.0.0.1:8880'" \
    --publish 4443:443 --publish 8880:80 --publish 2224:22 \
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
      TZ: 'Asia/Seoul'
    ports:
      - '8880:80'
      - '4443:443'
      - '2224:22'
    volumes:
      - '~/gitlab/config:/etc/gitlab'
      - '~/gitlab/logs:/var/log/gitlab'
      - '~/gitlab/data:/var/opt/gitlab'
      - '~/gitlab/backups:/var/opt/gitlab/backups'
```

### 2) docker-compose 실행

```shell
$ docker-compose up -d
```

## 4. GitLab Configuration 설정

### 1) GitLab 컨테이너 접속
포트를 변경하였기 때문에 GitLab Configuration도 변경한다. 포트를 변경하지 않았으면 GitLab Configuration 설정을 변경하지 않아도 된다.

포트 번호 설명이다.

* 22: SSH

* 80: HTTP

* 443: HTTPS

```shell
$ docker exec -it gitlab /bin/bash
```

### 2) vim 설치
파일 편집을 위해 apt-get 업테이트 후, vim을 설치한다.

```shell
root@127:/# apt-get update
root@127:/# apt-get install vim
```

### 3) vim으로 gitlab.rb 파일 수정

```shell
root@127:/# vim /etc/gitlab/gitlab.rb
```

### 4) gitlab.rb 내용 추가

```text
external_url 'http://127.0.0.1:8880'
gitlab_rails['gitlab_shell_ssh_port'] = 2224
```

### 5) gitlab.rb 설정 적용

```shell
root@127:/# gitlab-ctl reconfigure
```

### 6) GitLab 컨테이너 접속 종료

```shell
root@127:/# exit
```

### 7) Docker 재시작

```shell
$ docker restart gitlab
```

## 5. GitLab 접속

```text
http://127.0.0.1:8880
```

### 1) 초기 root 비밀번호 확인
초기 비밀번호로 로그인 후 새로운 비밀번호로 변경한다.

```shell
$ docker exec -it gitlab grep 'Password' /etc/gitlab/initial_root_password
```

## [출처 및 참고]
* [https://workshop.infograb.io/setup-gitlab/3_setup_gitlab/](https://workshop.infograb.io/setup-gitlab/3_setup_gitlab/)
* [https://mpolinowski.github.io/docs/DevOps/GitOps/2022-03-16--gitlab-docker-compose-on-localhost/2022-03-16/](https://mpolinowski.github.io/docs/DevOps/GitOps/2022-03-16--gitlab-docker-compose-on-localhost/2022-03-16/)
* [https://docs.gitlab.com/ee/install/docker.html](https://docs.gitlab.com/ee/install/docker.html)
* [https://hub.docker.com/r/gitlab/gitlab-ce/](https://hub.docker.com/r/gitlab/gitlab-ce/)
* [https://dncjf64.tistory.com/384](https://dncjf64.tistory.com/384)
* [https://lovemewithoutall.github.io/it/start-docker/](https://lovemewithoutall.github.io/it/start-docker/)
* [https://dejavuhyo.github.io/posts/install-gitlab-with-docker/](https://dejavuhyo.github.io/posts/install-gitlab-with-docker/)
