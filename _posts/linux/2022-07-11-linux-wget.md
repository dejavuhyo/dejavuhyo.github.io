---
title: 리눅스 wget
author: dejavuhyo
date: 2022-07-11 14:30:00 +0900
categories: [DevOps, Linux]
tags: [linux-wget, wget, 리눅스-wget, 리눅스]
---

## 1. wget
웹상의 파일을 다운로드한다.

## 2. 주요 옵션

| 옵션 | 설명 |
|:-----:|:-----:|
| `-O` | 파일 저장위치를 설정한다. |
| `-nv` | 다운로드 프로그레스 바를 출력하지 않는다. |

## 3. 사용예제

### 1) 파일 다운로드

```shell
# oozie 4.3.1 파일을 다운로드
$ wget http://apache.mirror.cdnetworks.com/oozie/4.3.1/oozie-4.3.1.tar.gz
--2019-09-18 03:57:17--  http://apache.mirror.cdnetworks.com/oozie/4.3.1/oozie-4.3.1.tar.gz
Resolving apache.mirror.cdnetworks.com... 14.0.101.165
Connecting to apache.mirror.cdnetworks.com|14.0.101.165|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 2431778 (2.3M) [application/x-gzip]
Saving to: “oozie-4.3.1.tar.gz”

100%[===========================>] 2,431,778   --.-K/s   in 0.07s

2019-09-18 03:57:17 (32.9 MB/s) - “oozie-4.3.1.tar.gz” saved [2431778/2431778]
```

### 2) 파일 다운로드 위치를 지정

```shell
# tmp/oozie.tar.gz 으로 파일 저장
$ wget -O ./tmp/oozie.tar.gz http://apache.mirror.cdnetworks.com/oozie/4.3.1/oozie-4.3.1.tar.gz
```

## [출처 및 참고]
* <https://wikidocs.net/48024>
