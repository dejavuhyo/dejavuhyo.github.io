---
title: CentOS 계정 생성 및 삭제
author: dejavuhyo
date: 2020-04-07 13:00:00 +0900
categories: [DevOps, Linux]
tags: [centos-add-user, centos-delete-user, centos-계정-추가, centos-계정-삭제]
---

## 1. 계정 생성

```bash
[root@localhost ~]# useradd 계정명
```

## 2. 계정 삭제

* 계정과 홈 디렉터리 모두 삭제

```bash
[root@localhost ~]# userdel -r 계정명
```

* 계정만 삭제

```bash
[root@localhost ~]# userdel 계정명
```
