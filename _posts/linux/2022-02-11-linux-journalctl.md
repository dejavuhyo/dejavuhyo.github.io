---
title: Linux journalctl
author: dejavuhyo
date: 2022-02-11 09:30:00 +0900
categories: [DevOps, Linux]
tags: [linux-journalctl, journalctl, 리눅스-journalctl]
---

## 1. journalctl
systemctl로 실행한 systemd의 로그를 확인할 수 있는 명령어 이다.

## 2. 주요 옵션

| 옵션 | 내용 |
|:-----:|:-----:|
| -u | 로그를 출력할 유닛을 지정 |
| -o | 출력 형식을 지정 (short, short-iso) |
| -f | 신규로 추가 되는 로그를 출력 |

## 3. 출력 형식

| 옵션 | 내용 |
|:-----:|:-----:|
| short | 기본값. 한 행에 하나의 Log만 출력 |
| short-iso | 기본값에 ISO 8601의 시간 형식으로 출력 |
| short-precise | 기본값에 마이크로 초 단위로 시간 출력 |
| short-monotonic | 기본값에 단조로운 시간 형식으로 출력 |
| verbose | 전체 Log를 모두 자세하게 출력 |
| json | json 형식 |
| json-pretty | json 형식을 보기 편하게 출력 |
| json-see | json 형식을 Server-Sent Events에 적합한 형식으로 출력 |
| cat | 간결하게 출력 |

## 4. 사용예제

```shell
# 시스템 데몬 로그 출력
$ sudo journalctl -u zookeeper.service -o short-iso
-- Logs begin at Thu 2021-12-30 10:19:49 KST, end at Sun 2022-01-30 08:20:43 KST. --
2021-12-30T13:10:02+0900 hadoop-master-1 systemd[1]: Starting Zookeeper...
2021-12-30T13:10:02+0900 hadoop-master-1 zkServer.sh[10186]: ZooKeeper JMX enabled by default
2021-12-30T13:10:02+0900 hadoop-master-1 zkServer.sh[10186]: Using config: /opt/zookeeper/bin/../conf/zoo.cfg
2021-12-30T13:10:03+0900 hadoop-master-1 zkServer.sh[10186]: Starting zookeeper ... STARTED
```

## [출처 및 참고]
* [https://wikidocs.net/160181](https://wikidocs.net/160181)
