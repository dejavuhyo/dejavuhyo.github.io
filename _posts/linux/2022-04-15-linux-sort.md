---
title: Linux sort
author: dejavuhyo
date: 2022-04-15 10:00:00 +0900
categories: [DevOps, Linux]
tags: [linux-sort, sort, 리눅스-sort, 리눅스-문자열]
---

## 1. sort
텍스트를 정렬 한다.

## 2. 주요 옵션

| 옵션 | 설명 |
|:-----:|:-----:|
| -k | 정렬할 포지션을 지정 |
| -t | 필드를 구분하는 구분자로 기본값은 공백과 탭 |
| -r | 역순으로 정렬 |

## 3. 사용예제

```shell
# 원본 파일 내용
$ cat s
a 1
d 4
a 2
c 2
a 3
b 3
c 4

# 정렬
$ cat s | sort
a 1
a 2
a 3
b 3
c 2
c 4
d 4

# 2번째 데이터로 정렬
$ cat s | sort -k 2
a 1
a 2
c 2
a 3
b 3
c 4
d 4
```

## [출처 및 참고]
* <https://wikidocs.net/33770>
