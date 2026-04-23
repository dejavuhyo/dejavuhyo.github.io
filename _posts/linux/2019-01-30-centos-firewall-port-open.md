---
title: CentOS 방화벽 포트 열기
author: dejavuhyo
date: 2019-01-30 15:16:00 +0900
categories: [DevOps, Linux]
tags: [centos-firewall-open, firewall-open, port-open, centos-방화벽-열기, 방화벽-열기, 포트-열기]
---

## 1. 포트 열기

* 예시: 8080 포트 열기

```bash
[root@localhost ~]# firewall-cmd --permanent --zone=public --add-port=8080/tcp
```

## 2. 방화벽 재실행

```bash
[root@localhost ~]# firewall-cmd --reload
```
