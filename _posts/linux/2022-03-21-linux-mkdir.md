---
title: 리눅스 mkdir
author: dejavuhyo
date: 2022-03-21 10:30:00 +0900
categories: [DevOps, Linux]
tags: [linux-mkdir, mkdir, 리눅스-mkdir]
---

## 1. mkdir
디렉토리를 생성한다.

## 2. 주요 옵션

| 옵션 | 내용 |
|:-----:|:-----:|
| -p | 지정된 경로가 존재하지 않으면 모두 생성한다. |

## 3. 사용예제

```shell
# dir1 디렉토리를 생성한다.
$ mkdir dir1

# par1 디렉토리를 생성하고, 하위에 dir1을 생성한다.
$ mkdir -p par1/dir1
```

## [출처 및 참고]
* <https://wikidocs.net/47982>
