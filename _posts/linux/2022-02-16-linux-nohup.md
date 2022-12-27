---
title: Linux nohup
author: dejavuhyo
date: 2022-02-16 09:30:00 +0900
categories: [DevOps, Linux]
tags: [linux-nohup, nohup, 리눅스-nohup]
---

## 1. nohup
리눅스에서 프로그램을 실행할 때 사용자의 세션이 끊어지면(hangup) 프로그램도 함께 종료된다. 처리에 오랜 시간이 걸리는 프로그램을 실행 할 때 nohup을 이용하여 사용자의 세션이 끊어져도 프로그램은 계속 실행되도록 할 수 있다.

## 2. 사용예제
nohup 명령을 이용할 때 포어그라운드로 실행하게 되면 다른 작업을 처리할 수 없기 때문에 `&`를 이용하여 백그라운드로 실행하는 것이 좋다.

```shell
# test.sh 실행, 표준 출력이 nohup.out 으로 저장
$ nohup test.sh &

# test.sh를 실행하고 표준 출력을 test.log에 저장 
$ nohup test.sh > test.log &
```

## [출처 및 참고]
* <https://wikidocs.net/155602>
