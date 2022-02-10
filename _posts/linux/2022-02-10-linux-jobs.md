---
title: 리눅스 jobs
author: dejavuhyo
date: 2022-02-10 10:15:00 +0900
categories: [DevOps, Linux]
tags: [linux-jobs, jobs, 리눅스-jobs]
---

## 1. jobs
현재 계정에서 실행중인 작업을 표시한다.

## 2. 주요 옵션

| 옵션 | 설명 |
|:---:|:---:|
| -l | 프로세스 ID 표시 |

## 3. 사용예제
프로세스 상태

| 상태 | 내용 |
|:---:|:---:|
| Running | 실행 중 |
| Stopped | 일시 중단(Ctrl + Z) |
| Terminated | 강제 종료(kill 명령 종료) |
| Done | 정상 종료 |

`[]`로 표시되는 것은 작업의 순서이다. `-`는 이전 프로세스, `+`는 현재 프로세스이다.

```shell
# 실행중인 프로세스를 표시 
$ jobs 
[1]   Stopped                 watch date
[2]   Stopped                 watch date
[3]   Stopped                 watch date
[4]-  Stopped                 watch date
[5]+  Stopped                 watch date

# 실행 중인 프로세스의 PID 확인 
$ jobs -l
[1]  18129 Stopped                 watch date
[2]  18188 Stopped                 watch date
[3]  19726 Stopped                 watch date
[4]- 19741 Stopped                 watch date
[5]+ 19751 Stopped                 watch date
```

## [출처 및 참고]
* <https://wikidocs.net/44362>
