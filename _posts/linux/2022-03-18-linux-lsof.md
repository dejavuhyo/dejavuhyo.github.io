---
title: Linux lsof
author: dejavuhyo
date: 2022-03-18 08:50:00 +0900
categories: [DevOps, Linux]
tags: [linux-lsof, lsof, 리눅스-lsof]
---

## 1. lsof
lsof(list open files)의 약자로 열려 있는(사용 중인) 모든 파일을 확인할 수 있다.

## 2. 주요 옵션

| 옵션 | 내용 |
|:-----:|:-----:|
| i | IP 확인 |
| n | 호스트 이름 |
| P | 포트 이름 |

## 3. 사용예제

```shell
$ lsof
COMMAND     PID   TID            USER   FD      TYPE             DEVICE SIZE/OFF       NODE NAME
systemd       1                  root  rtd   unknown                                        /proc/1/root (readlink: Permission denied)
systemd       1                  root  txt   unknown                                        /proc/1/exe (readlink: Permission denied)
systemd       1                  root NOFD                                                  /proc/1/fd (opendir: Permission denied)
kthreadd      2                  root  cwd   unknown                                        /proc/2/cwd (readlink: Permission denied)
lsof      24994                deploy  mem       REG                8,2   464824     262660 /lib/x86_64-linux-gnu/libpcre.so.3.13.3
lsof      24994                deploy  mem       REG                8,2  2030928     262576 /lib/x86_64-linux-gnu/libc-2.27.so
lsof      24994                deploy  mem       REG                8,2   154832     262673 /lib/x86_64-linux-gnu/libselinux.so.1
lsof      24994                deploy  mem       REG                8,2   179152     262552 /lib/x86_64-linux-gnu/ld-2.27.so
```

## [출처 및 참고]
* [https://wikidocs.net/159930](https://wikidocs.net/159930)
