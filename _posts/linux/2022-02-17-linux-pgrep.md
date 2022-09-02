---
title: 리눅스 pgrep
author: dejavuhyo
date: 2022-02-17 10:00:00 +0900
categories: [DevOps, Linux]
tags: [linux-pgrep, pgrep, 리눅스-pgrep]
---

## 1. pgrep
ps 명령과 grep 명령을 동시에 실행하는 것과 같은 명령어 이다. 지정한 형식에 맞는 프로세스 번호를 반환한다.

## 2. 주요 옵션

| 옵션 | 내용 |
|:-----:|:-----:|
| -f | 문자열 패턴에 맞는 프로세스 반환 |

## 3. 사용예제

### 1) 이름에 맞는 프로세스 반환

```shell
$ pgrep -f vi
2341

$ pgrep -f  HiveMetaStore
18707

# java 클래스 명으로 찾을 때 
# 플링크 프로세스를 찾을 때 -f옵션 이 없으면 결과가 나타나지 않음 org.apache.flink.runtime.taskexecutor.TaskManagerRunner
$ pgrep org.apache.flink.runtime.taskexecutor.TaskManagerRunner

$ pgrep -f org.apache.flink.runtime.taskexecutor.TaskManagerRunner
24065
```

### 2) 이름에 맞는 프로세스를 바로 죽이는 방법

```shell
# 프로세스 번호를 출력
$ pgrep -f vi | xargs echo
2341

# 프로세스를 바로 죽임 
$ pgrep -f  HiveMetaStore | xargs kill
```

### 3) 프로세스 종료를 대기

```shell
# HiveMetaStore 작업이 종료 될 때까지 대기
while pgrep -f HiveMetaStore > /dev/null; do
  echo "sleep ..."
  sleep 1
done
```

## [출처 및 참고]
* <https://wikidocs.net/85881>
