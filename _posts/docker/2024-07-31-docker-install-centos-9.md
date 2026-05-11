---
title: CentOS 9에 Docker 설치
author: dejavuhyo
date: 2024-07-31 08:33:00 +0900
categories: [DevOps, Docker]
tags: [centos-docker, docker, centos-docker-install, docker-install]
---

## 1. 이전 버전 제거

```bash
sudo dnf remove docker \
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

```bash
sudo dnf install -y yum-utils
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

## 3. Docker 엔진 설치

```bash
sudo dnf install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

GPG 키 수락 메시지가 표시되면 지문이 `060A 61C5 1B55 8A7F 742B 77AA C52F EB6B 621E 9F35`로 일치하는지 확인하고 일치하면 수락한다.

## 4. Docker 시작

```bash
sudo systemctl start docker
sudo systemctl enable docker
```

* 버전 확인

```bash
docker --version
```

### 5. Docker Engine 설치 성공 확인

```bash
sudo docker run hello-world
```

## [출처 및 참고]
* [https://docs.docker.com/engine/install/centos/](https://docs.docker.com/engine/install/centos/)
* [https://workshop.infograb.io/setup-gitlab/1_setup_docker/](https://workshop.infograb.io/setup-gitlab/1_setup_docker/)
