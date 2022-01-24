---
title: Podman 설치
author: dejavuhyo
date: 2021-08-09 06:00:00 +0900
categories: [DevOps, Podman]
tags: [podman-install, podman, 포드맨-설치, 포드맨]
---

## 1. 설치
Podman은 Linux container를 구동하기 위해 설계했으므로 OS X나 Windows에서는 podman 엔진을 구동할 수 없다. 대신 client를 설치하고 Linux podman engine에 network로 접속해서 관리할 수 있다.

### 1) Linux
CentOS 8에서는 기본 제공되므로 다음 명령어로 podman을 설치할 수 있다.

* RHEL/CentOS 8

```shell
sudo yum -y install podman
```

* RHEL/CentOS 7
CentOS 7을 사용할 경우 최신 app을 제공하는 [Kubic project 저장소](https://build.opensuse.org/project/show/devel:kubic:libcontainers:stable)를 등록하면 된다.

```shell
sudo curl -L -o /etc/yum.repos.d/devel:kubic:libcontainers:stable.repo https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/CentOS_7/devel:kubic:libcontainers:stable.repo

sudo yum -y install podman
```

> "Requires: container-selinux" 에러가 나면 아래 패키지를 설치한다.

```shell
sudo yum install http://mirror.centos.org/centos/7/extras/x86_64/Packages/container-selinux-2.119.2-1.911c772.el7_8.noarch.rpm
```

### 2) Ubuntu
Ubuntu 20.10 이상은 podman이 포함되어 있으므로 아래 명령으로 설치한다.

* Ubuntu 20.10 이상

```shell
sudo apt -y update

sudo apt -y install podman
```

* Ubuntu 20.04 이하
Ubuntu 20.04 이하는 다음 스크립트로 kubic 저장소를 등록하고 podman을 설치한다.

```shell
. /etc/os-release

echo "deb https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/xUbuntu_${VERSION_ID}/ /" | sudo tee etc/apt/sources.list.d/devel:kubic:libcontainers:stable.list

curl -L https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/xUbuntu_${VERSION_ID}/Release.key | sudo apt-key add -

sudo apt-get update

sudo apt-get -y upgrade 

sudo apt-get -y install podman
```

> 다른 리눅스 배포판에서 설치 정보는 [Podman Installation Instructions](https://podman.io/getting-started/installation#linux-distributions)에서 찾을 수 있다.

### 3) OS X
OS X에서 podman client는 brew로 설치하면 된다.

```shell
brew install podman
```

### 4) Windows
Windows도 client만 제공하며 [Latest remote client for Windows](https://github.com/containers/podman/releases/latest/download/podman-remote-release-windows.zip) 에서 다운받아서 설치하면 된다.

또는 [WSL 2](https://www.lesstif.com/software-architect/wsl-2-windows-subsystem-for-linux-2-89555812.html)은 Ubuntu Linux 이므로 container engine 설치가 가능하므로 podman을 설치하고 사용해도 된다.

## 2. 사용
podman은 docker와 명령어가 완전히 호환되므로 docker를 사용할 줄 안다면 별도로 podman 명령어를 배울 필요가 없다. docker를 podman으로 대체하려면 다음과 같이 alias만 하나 적어주면 된다.

```shell
alias docker=podman
```

만약 이미 docker를 설치했고 docker daemon도 구동되었다면 먼저 중지해야 한다.

```shell
sudo systemctl stop docker

sudo systemctl disable docker
```

## [출처 및 참고]
* <https://www.lesstif.com/container/podman-98926748.html>
* <https://podman.io/getting-started/installation#linux-distributions>
