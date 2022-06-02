---
title: 리눅스 head
author: dejavuhyo
date: 2022-06-02 21:30:00 +0900
categories: [DevOps, Linux]
tags: [linux-head, head, 리눅스-head, 리눅스-헤드]
---

## 1. head
파일의 앞부분을 출력한다.

## 2. 주요 옵션

| 옵션 | 설명 |
|:---:|:---:|
| `-n 라인번호` | 지정한 라인번호까지 출력 |

## 3. 사용예제

```shell
$ cat sample.txt
one
two
three

# 2번 라인까지 출력
$ head -n 2 sample.txt 
one
two
```

## [출처 및 참고]
* <https://wikidocs.net/47998>
