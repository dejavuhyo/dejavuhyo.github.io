---
title: Linux less, more
author: dejavuhyo
date: 2022-06-03 09:20:00 +0900
categories: [DevOps, Linux]
tags: [linux-less-more, linux-less, linux-more, less-more, 리눅스-less-more, 리눅스]
---

## 1. less, more
텍스트파일을 열람한다.

텍스트파일을 화면 단위로 열람한다. 명령어를 실행하고 화면을 이동하는 명령어는 vi 명령어와 동일하다. less가 more 보다 화면을 자유롭게 이동할 수 있는 기능이 있어서 편리하다.

## 2. 주요 옵션

| 옵션 | 설명 |
|:-----:|:-----:|
| `-N` | 라인번호를 표시 |

## 3. 사용예제

```shell
# sample.txt 파일을 열람
$ less sample.txt
$ more sample.txt

# sample.txt파일의 라인번호를 출력하면서 열람
$ less -N sample.txt
$ more -N sample.txt
```

## [출처 및 참고]
* <https://wikidocs.net/48005>
