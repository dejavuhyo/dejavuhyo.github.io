---
title: Ubuntu 호스트네임(hostname) 변경
author: dejavuhyo
date: 2024-08-22 08:34:00 +0900
categories: [DevOps, Linux]
tags: [ubuntu-hostname, ubuntu-terminal, 우분투-호스트네임, 우분투-호스트명, 우분투-터미널]
---

## 1. 호스트네임 확인

```shell
# 호스트명 확인
$ hostname

# 상세 정보
$ hostnamectl
```

## 2. 변경

### 1) 호스트네임 변경
변경 후 재부팅 한다.

```shell
$ sudo vim /etc/hostname
```

### 2) IP 주소와 호스트네임 매핑

```shell
$ sudo vim /etc/hosts
```

## 3. 변경 확인

```shell
$ cat /etc/hosts
```

## [출처 및 참고]
* [https://iter.kr/우분투-호스트네임-변경-방법/](https://iter.kr/우분투-호스트네임-변경-방법/)
