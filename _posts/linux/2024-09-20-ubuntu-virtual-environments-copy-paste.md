---
title: VMware Workstation에서 Ubuntu 가상환경 복사 붙여넣기 활성화 방법
author: dejavuhyo
date: 2024-09-20 08:53:00 +0900
categories: [DevOps, Linux]
tags: [vmware-workstation, vmware-copy-paste, vmware-ubuntu, 가상환경-복사-붙여넣기]
---

## 1. 문제점
VMware Workstation에서 Host PC인 윈도우 환경과 Ubuntu 가상 환경에서 복사-붙여넣기가 안되는 문제가 있다.

## 2. 해결방법

### 1) Vm Tools 설치
vm-tools를 설치해서 Host PC와 Ubuntu 가상 환경이 통신할 수 있는 환경을 설치한다.

```shell
$ sudo apt-get update
$ sudo apt-get install open-vm-tools-desktop
```

### 2) Options 설정

![settings](/assets/img/2024-09-20-ubuntu-virtual-environments-copy-paste/settings.png)

![options](/assets/img/2024-09-20-ubuntu-virtual-environments-copy-paste/options.png)

## [출처 및 참고]
* [https://pgh268400.tistory.com/515](https://pgh268400.tistory.com/515)
