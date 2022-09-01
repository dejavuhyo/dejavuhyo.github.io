---
title: 리눅스 watch
author: dejavuhyo
date: 2022-03-02 10:00:00 +0900
categories: [DevOps, Linux]
tags: [linux-watch, watch, 리눅스-watch]
---

## 1. watch
주기적으로 명령어를 실행하고, 결과를 화면에 출력한다.

## 2. 주요 옵션

| 옵션 | 내용 |
|-----|-----|
| -n | 반복적으로 실행할 시간 간격. 초단위로 기본값은 2초 |
| -d | 변경되는 곳을 표시. 변경되는 문자를 밝게 표시 |

## 3. 사용예제

```shell
# date 명령어를 반복하여 출력. 반복 간격은 기본값 2초 
$ watch date
Every 2.0s: date                                                                                                                                                                                                                                      Thu Aug  1 01:37:33 2019


# 반복 간격 1초로 실행 
$ watch -n 1 date
Every 1.0s: date                                                                                                                                                                                                                                      Thu Aug  1 01:38:33 2019


# 이전 결과와 바뀐부분을 하일라이팅 처리 
$ watch -d date
```

## [출처 및 참고]
* <https://wikidocs.net/44360>
