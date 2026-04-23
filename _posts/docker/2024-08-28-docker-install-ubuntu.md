---
title: Ubuntu에 Docker 설치
author: dejavuhyo
date: 2024-08-28 09:06:00 +0900
categories: [DevOps, Docker]
tags: [ubuntu-docker, docker, ubuntu-docker-install, docker-install]
---

## 1. 충돌하는 모든 패키지 제거

```bash
$ sudo apt remove $(dpkg --get-selections docker.io docker-compose docker-compose-v2 docker-doc podman-docker containerd runc | cut -f1)
```

## 2. apt 저장소 설정

```bash
# Add Docker's official GPG key:
$ sudo apt update
$ sudo apt install ca-certificates curl
$ sudo install -m 0755 -d /etc/apt/keyrings
$ sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
$ sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
$ sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF

$ sudo apt update
```

## 3. Docker 패키지 설치

```bash
$ sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Docker 서비스는 설치 후 자동으로 시작된다. Docker가 실행 중인지 확인하려면 다음 명령어를 사용한다.

```bash
$ sudo systemctl status docker
```

일부 시스템에서는 이 기능이 비활성화되어 있을 수 있으며, 수동으로 시작해야 할 수 있다.

```bash
$ sudo systemctl start docker
```

### 4. Docker Engine 설치 성공 확인

```bash
$ sudo docker run hello-world
```

## [출처 및 참고]
* [https://docs.docker.com/engine/install/ubuntu/](https://docs.docker.com/engine/install/ubuntu/)
