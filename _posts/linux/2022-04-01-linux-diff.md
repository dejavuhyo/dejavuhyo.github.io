---
title: Linux diff
author: dejavuhyo
date: 2022-04-01 15:50:00 +0900
categories: [DevOps, Linux]
tags: [linux-diff, diff, 리눅스-diff, 리눅스-문자열]
---

## 1. diff
파일 두개를 비교하여 다른 부분을 출력한다.

## 2. 주요 옵션

| 옵션 | 설명 |
|:-----:|:-----:|
| -i | 대소문자 무시 |

## 3. 사용예제

| 행번호 | 파일 a | 파일 b |
|:-----:|:-----:|:-----:|
| 1 | a | a |
| 2 | b | d |
| 3 | c | c |
| 4 |  | b |

```shell
$ cat a
a
b
c

$ cat b
a
d
c
b

$ diff a b
2c2 # 파일1의 2행, 비교(c), 파일2의 2행
< b # 파일 1(<) = b
---
> d # 파일 2(>) = d
3a4 # 파일1의 3행, 추가(a), 파일2의 4행
> b # 파일 2(>) = b
```

## [출처 및 참고]
* <https://wikidocs.net/33651>
