---
title: 리눅스 tail
author: dejavuhyo
date: 2022-06-16 13:30:00 +0900
categories: [DevOps, Linux]
tags: [linux-tail, tail, 리눅스-tail, 리눅스]
---

## 1. tail
파일의 뒷부분을 출력한다.

## 2. 주요 옵션

| 옵션 | 설명 |
|:---:|:---:|
| `-n 라인번호` | 지정한 라인번호까지 출력한다. |
| `-f` | 파일에 데이터가 추가될 때까지 대기하면서 추가되면 계속 출력한다. |

## 3. 사용예제

```shell
$ cat sample.txt
one
two
three
four

# 마지막에서 2번 라인까지 출력
$ tail -n 2 sample.txt
three
four

# 2번 라인부터 출력
$ tail -n +2 sample.txt
two
three
four
```

## [출처 및 참고]
* <https://wikidocs.net/48003>
