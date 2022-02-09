---
title: 리눅스 htop
author: dejavuhyo
date: 2022-02-09 09:30:00 +0900
categories: [DevOps, Linux]
tags: [linux-htop, htop, 리눅스-htop]
---

## 1. htop
htop은 top 보다 상세하게 운영체제의 상태를 모니터링할 수 있는 도구 이다. 설치되어 있지 않은 경우 따로 설치를 해야한다.

## 2. 설치

```shell
# centos
yum install htop

# ubuntu
apt install htop
```

## 3. 실행
펑션키를 이용하여 트리구조로 프로세스를 확인하거나 목록으로 확인할 수 있다. 메모리, CPU 사용률로 프로세스를 정렬할 수도 있다.

![htop](/assets/img/2022-02-09-linux-htop/htop.png)

## [출처 및 참고]
* <https://wikidocs.net/145642>
