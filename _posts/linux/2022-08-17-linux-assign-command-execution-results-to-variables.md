---
title: Linux 명령어 실행결과 변수 할당
author: dejavuhyo
date: 2022-08-17 10:20:00 +0900
categories: [DevOps, Linux]
tags: [linux-command, command-execution-results, assign-command-results-variables, 리눅스-명령어, 리눅스-변수-할당, 실행결과-변수할당, 리눅스-실행결과-변수할당]
---

## 1. 명령어 실행결과 변수 할당
명령어 실행 결과를 변수에 할당하는 방법은 두 가지가 있다. `$()`를 이용하는 방법과 `로 명령어를 감싸주는 방법이다.

```shell
$ A=$(date)
$ echo $A
Wed Apr 1 05:08:12 UTC 2020

$ B=`date`
$ echo $B
Wed Apr 1 05:08:24 UTC 2020
```

## [출처 및 참고]
* [https://wikidocs.net/54525](https://wikidocs.net/54525)
