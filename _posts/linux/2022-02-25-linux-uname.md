---
title: 리눅스 uname
author: dejavuhyo
date: 2022-02-25 09:30:00 +0900
categories: [DevOps, Linux]
tags: [linux-uname, uname, 리눅스-uname]
---

## 1. uname
시스템 정보를 표시한다.

## 2. 주요 옵션

| 옵션 | 내용 |
|:---:|:---:|
| -a | 모든 시스템 정보를 표시한다. |
| -n | 호스트 명을 표시한다. |
| -m | 머신타입을 표시한다. |
| -p | 프로세서 타입을 표시한다. |
| -s | OS의 시스템 타입을 표시한다. |
| -v | 커널 정보를 표시한다. |

## 3. 사용예제

```shell
# 시스템 명을 표시 
$ uname
Linux

# 모든 정보를 표시 
$ uname -a
Linux localhost-name 1.2.32-31.23.amzn1.x86_64 #1 SMP Tue Oct 18 22:02:09 UTC 2016 x86_64 x86_64 x86_64 GNU/Linux

# 호스트 명을 표시 
$ uname -n
localhost-name

# 머신 타입을 표시 
$ uname -m
x86_64

# 프로세서 타입을 표시 
$ uname -p
x86_64

$ OS의 시스템 타입을 표시 
$ uname -s
Linux

# 릴리스 번호를 표시 
$ uname -r
1.2.32-31.23.amzn1.x86_64

# 커널정보를 표시/ 커널의 컴파일 일시 정보를 표시 
$ uname -v
#1 SMP Tue Oct 18 22:02:09 UTC 2016
```

## [출처 및 참고]
* <https://wikidocs.net/47983>
