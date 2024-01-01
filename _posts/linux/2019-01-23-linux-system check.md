---
title: CentOS 시스템 확인
author: dejavuhyo
date: 2019-01-23 17:40:00 +0900
categories: [DevOps, Linux]
tags: [linux-system-check, system-check, 리눅스-시스템-확인, 시스템-확인]
---

## 1. 운영체제 버전 확인

```shell
[root@localhost ~]# cat /etc/*release*
```

## 2. 커널 버전 확인

```shell
[root@localhost ~]# cat /proc/version
```

## 3. 시스템 비트 확인

```shell
[root@localhost ~]# arch
```

## 4. CPU 정보

```shell
[root@localhost ~]# cat /proc/cpuinfo
```

## 5. 메모리 정보

```shell
[root@localhost ~]# cat /proc/meminfo
```

## 6. 파티션 정보

```shell
[root@localhost ~]# cat /proc/partitions
```

## 7. 호스트 네임

```shell
[root@localhost ~]# hostname
```

## [출처 및 참고]
* [https://webdir.tistory.com/103](https://webdir.tistory.com/103)
