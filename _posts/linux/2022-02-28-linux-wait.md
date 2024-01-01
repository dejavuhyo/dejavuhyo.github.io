---
title: Linux wait
author: dejavuhyo
date: 2022-02-28 16:30:00 +0900
categories: [DevOps, Linux]
tags: [linux-wait, wait, 리눅스-wait]
---

## 1. wait
실행한 프로세스가 종료되는 것을 대기하게 하는 명령이다. 여러작업을 동시에 실행 할 때 모든 작업이 종료되는 것을 대기하게 하거나, 특정 작업이 종료되는 것을 기다릴때 사용한다.

## 2. 사용예제

### 1) 백그라운드 작업 종료를 대기

```text
# 아래와 같이 사용하면 command1, command2, command3 이 모두 종료 될 때까지 작업을 대기한다. 이 후 모든 작업이 종료되면 command4를 실행한다.
command1 &
command2 &
command3 &
wait

command4
```

### 2) 특정 프로세스가 종료되는 것을 대기
특정 프로세스가 종료되는 것을 대기 할 때는 작업번호를 전달한다.

```text
# 프로세스 번호 100번이 종료되는 것을 대기합니다. 
wait 100
```

## [출처 및 참고]
* [https://wikidocs.net/85880](https://wikidocs.net/85880)
