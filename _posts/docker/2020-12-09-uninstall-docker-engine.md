---
title: Docker Engine 제거
author: dejavuhyo
date: 2020-12-09 10:00:00 +0900
categories: [DevOps, Docker]
tags: [docker-uninstall, docker-engine-uninstall, cnetos-docker-uninstall, centos-docker, docker, docker-제거, 도커-제거, 도커-엔진-제거]
---

## 1. Docker Engine 제거

### 1) Docker Engine, CLI, Containerd 패키지 제거

```bash
[root@localhost ~]# yum remove docker-ce docker-ce-cli containerd.io
```

### 2) 모든 이미지, 컨테이너 및 볼륨 삭제
호스트 이미지, 컨테이너, 볼륨 및 사용자 정의 구성 파일은 자동으로 제거되지 않는다. 모든 이미지, 컨테이너 및 볼륨을 삭제한다.

```bash
[root@localhost ~]# rm -rf /var/lib/docker
```

## [출처 및 참고]
* <https://docs.docker.com/engine/install/centos/>
