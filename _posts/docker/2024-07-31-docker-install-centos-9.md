---
title: CentOS 9 Docker 설치
author: dejavuhyo
date: 2024-07-31 08:33:00 +0900
categories: [DevOps, Docker]
tags: [centos-docker, docker, centos-docker-install, docker-install]
---

## 1. 이전 버전 제거

```shell
$ sudo dnf remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```

## 2. Docker 저장소 설정
`yum-utils` 패키지(`yum-config-manager` 유틸리티 제공)를 설치하고 저장소를 설정한다.

```shell
$ sudo dnf install -y yum-utils
$ sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

## 3. Docker 엔진 설치

```shell
$ sudo dnf install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

GPG 키 수락 메시지가 표시되면 지문이 `060A 61C5 1B55 8A7F 742B 77AA C52F EB6B 621E 9F35`로 일치하는지 확인하고 일치하면 수락한다.

## 4. Docker 시작

```shell
$ sudo systemctl start docker
$ sudo systemctl enable docker
Created symlink /etc/systemd/system/multi-user.target.wants/docker.service → /usr/lib/systemd/system/docker.service.
```

* 버전 확인

```shell
$ docker --version
Docker version 27.0.3, build 7d4bcd8
```

### 5. Docker Engine 설치 성공 확인

```shell
$ sudo docker run hello-world
Unable to find image 'hello-world:latest' locally
^[latest: Pulling from library/hello-world
c1ec31eb5944: Pull complete 
Digest: sha256:1408fec50309afee38f3535383f5b09419e6dc0925bc69891e79d84cc4cdcec6
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

## [출처 및 참고]
* [https://docs.docker.com/engine/install/centos/](https://docs.docker.com/engine/install/centos/)
* [https://workshop.infograb.io/setup-gitlab/1_setup_docker/](https://workshop.infograb.io/setup-gitlab/1_setup_docker/)
