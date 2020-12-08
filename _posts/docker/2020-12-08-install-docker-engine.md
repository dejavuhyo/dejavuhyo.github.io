---
title: Docker Engine 설치
author: Hyosik
date: 2020-12-08 16:50:00 +0900
categories: [Tools, Docker]
tags: [docker-install, docker-engine-install, cnetos-docker-install, centos-docker, docker, docker-설치, 도커-설치, 도커-엔진-설치]
---

## 1. 이전 버전 제거
이전 버전의 docker 또는 docker-engine이 설치되어있는 경우 관련 종속성과 함께 제거한다.

```shell
[root@localhost ~]# sudo yum remove docker
[root@localhost ~]# sudo yum remove docker-client
[root@localhost ~]# sudo yum remove docker-client-latest
[root@localhost ~]# sudo yum remove docker-common
[root@localhost ~]# sudo yum remove docker-latest
[root@localhost ~]# sudo yum remove docker-latest-logrotate
[root@localhost ~]# sudo yum remove docker-logrotate
[root@localhost ~]# sudo yum remove docker-engine
```

/var/lib/docker/에 images, containers, volumes, and networks를 포함한 컨텐츠는 보존됨

## 2. 저장소 설정
필요에 따라 다양한 방법으로 Docker Engine을 설치할 수 있다.

* 대부분 쉬운 설치 및 업그레이드를 위해 Docker 저장소를 설정하고 설치 한다.(권장되는 방식)

* RPM 패키지를 다운로드하여 수동으로 설치하고 업그레이드를 수동으로 관리한다. 인터넷에 액세스할 수 없는 시스템에 Docker를 설치하는 상황에서 유용하다.

### 1) yum 패키지 업데이트

```shell
[root@localhost ~]# sudo yum update
```

### 2) 유틸리티 제공하는 패키지 설치

```shell
[root@localhost ~]# sudo yum install -y yum-utils
```

### 3) yum-config-manager 설정

```shell
[root@localhost ~]# sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
Loaded plugins: fastestmirror, langpacks
adding repo from: https://download.docker.com/linux/centos/docker-ce.repo
grabbing file https://download.docker.com/linux/centos/docker-ce.repo to /etc/yum.repos.d/docker-ce.repo
repo saved to /etc/yum.repos.d/docker-ce.repo
```

## 3. Docker Engine 설치

### 1) 최신 버전 Docker Engine 및 containerd 설치

```shell
[root@localhost ~]# sudo yum install docker-ce docker-ce-cli containerd.io
```

> GPG 키를 수락하라는 메시지가 표시되면 지문이 일치하는지 확인하고, 060A 61C5 1B55 8A7F 742B 77AA C52F EB6B 621E 9F35 일치하는 경우 수락한다.

### 2) 특정 버전의 Docker Engine 설치

* 설치 가능 버전 확인

```shell
[root@localhost ~]# yum list docker-ce --showduplicates | sort -r
docker-ce.x86_64            3:19.03.9-3.el7                    docker-ce-stable 
docker-ce.x86_64            3:19.03.8-3.el7                    docker-ce-stable 
docker-ce.x86_64            3:19.03.7-3.el7                    docker-ce-stable 
docker-ce.x86_64            3:19.03.6-3.el7                    docker-ce-stable 
docker-ce.x86_64            3:19.03.5-3.el7                    docker-ce-stable 
docker-ce.x86_64            3:19.03.4-3.el7                    docker-ce-stable 
docker-ce.x86_64            3:19.03.3-3.el7                    docker-ce-stable 
docker-ce.x86_64            3:19.03.2-3.el7                    docker-ce-stable 
docker-ce.x86_64            3:19.03.14-3.el7                   docker-ce-stable 
docker-ce.x86_64            3:19.03.14-3.el7                   @docker-ce-stable
docker-ce.x86_64            3:19.03.13-3.el7                   docker-ce-stable 
docker-ce.x86_64            3:19.03.12-3.el7                   docker-ce-stable 
docker-ce.x86_64            3:19.03.11-3.el7                   docker-ce-stable 
docker-ce.x86_64            3:19.03.10-3.el7                   docker-ce-stable 
docker-ce.x86_64            3:19.03.1-3.el7                    docker-ce-stable 
docker-ce.x86_64            3:19.03.0-3.el7                    docker-ce-stable 
docker-ce.x86_64            3:18.09.9-3.el7                    docker-ce-stable 
docker-ce.x86_64            3:18.09.8-3.el7                    docker-ce-stable 
docker-ce.x86_64            3:18.09.7-3.el7                    docker-ce-stable 
docker-ce.x86_64            3:18.09.6-3.el7                    docker-ce-stable 
docker-ce.x86_64            3:18.09.5-3.el7                    docker-ce-stable 
docker-ce.x86_64            3:18.09.4-3.el7                    docker-ce-stable 
docker-ce.x86_64            3:18.09.3-3.el7                    docker-ce-stable 
docker-ce.x86_64            3:18.09.2-3.el7                    docker-ce-stable 
docker-ce.x86_64            3:18.09.1-3.el7                    docker-ce-stable 
docker-ce.x86_64            3:18.09.0-3.el7                    docker-ce-stable 
docker-ce.x86_64            18.06.3.ce-3.el7                   docker-ce-stable 
docker-ce.x86_64            18.06.2.ce-3.el7                   docker-ce-stable 
docker-ce.x86_64            18.06.1.ce-3.el7                   docker-ce-stable 
docker-ce.x86_64            18.06.0.ce-3.el7                   docker-ce-stable 
docker-ce.x86_64            18.03.1.ce-1.el7.centos            docker-ce-stable 
docker-ce.x86_64            18.03.0.ce-1.el7.centos            docker-ce-stable 
docker-ce.x86_64            17.12.1.ce-1.el7.centos            docker-ce-stable 
docker-ce.x86_64            17.12.0.ce-1.el7.centos            docker-ce-stable 
docker-ce.x86_64            17.09.1.ce-1.el7.centos            docker-ce-stable 
docker-ce.x86_64            17.09.0.ce-1.el7.centos            docker-ce-stable 
docker-ce.x86_64            17.06.2.ce-1.el7.centos            docker-ce-stable 
docker-ce.x86_64            17.06.1.ce-1.el7.centos            docker-ce-stable 
docker-ce.x86_64            17.06.0.ce-1.el7.centos            docker-ce-stable 
docker-ce.x86_64            17.03.3.ce-1.el7                   docker-ce-stable 
docker-ce.x86_64            17.03.2.ce-1.el7.centos            docker-ce-stable 
docker-ce.x86_64            17.03.1.ce-1.el7.centos            docker-ce-stable 
docker-ce.x86_64            17.03.0.ce-1.el7.centos            docker-ce-stable 
Loading mirror speeds from cached hostfile
Loaded plugins: fastestmirror, langpacks
Installed Packages
Available Packages
 * updates: mirror.kakao.com
 * extras: mirror.kakao.com
 * base: mirror.kakao.com
```

* 설치

```shell
[root@localhost ~]# sudo yum install docker-ce-<VERSION_STRING> docker-ce-cli-<VERSION_STRING> containerd.io
```

## 4. 실행 및 확인

### 1) 실행 및 등록

```shell
[root@localhost ~]# sudo systemctl start docker
[root@localhost ~]# sudo systemctl enable docker
Created symlink from /etc/systemd/system/multi-user.target.wants/docker.service to /usr/lib/systemd/system/docker.service.
```

### 2) 버전 확인

```shell
[root@localhost ~]# docker --version
Docker version 19.03.14, build 5eb3275d40
```

### 3) 정상 설치 확인

```shell
[root@localhost ~]# sudo docker run hello-world

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
* <https://docs.docker.com/engine/install/centos/>
* <https://blog.naver.com/PostView.nhn?blogId=wideeyed&logNo=222052394245&parentCategoryNo=&categoryNo=&viewDate=&isShowPopularPosts=false&from=postView>
