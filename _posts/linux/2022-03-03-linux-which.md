---
title: 리눅스 which
author: dejavuhyo
date: 2022-03-03 08:00:00 +0900
categories: [DevOps, Linux]
tags: [linux-which, which, 리눅스-which]
---

## 1. which
명령어의 경로/앨리어스를 표시한다. 지정한 명령어의 절대경로나 앨리어스를 표시한다.

## 2. 주요 옵션

| 옵션 | 내용 |
|-----|-----|
| -a | 환경변수 PATH에서 지정한 모든 경로를 표시한다. |

## 3. 사용예제

```shell
# rm 명령어의 위치를 확인 
$ which rm
/bin/rm

# ll 앨리어스의 정보를  확인 
$ which ll
alias ll='ls -l --color=auto'
    /bin/ls
```

## [출처 및 참고]
* <https://wikidocs.net/47989>
