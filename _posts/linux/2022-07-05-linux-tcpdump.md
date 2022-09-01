---
title: 리눅스 tcpdump
author: dejavuhyo
date: 2022-07-05 13:00:00 +0900
categories: [DevOps, Linux]
tags: [linux-tcpdump, tcpdump, 리눅스-tcpdump, 리눅스]
---

## 1. tcpdump
`tcpdump`는 인바운드, 아웃바운드 트래픽 현황을 모니터링 한다.

## 2. 주요 옵션

| 옵션 | 설명 |
|-----|-----|
| `-w` | 로그를 파일로 저장 |
| `-r` | 저장된 로그를 읽음 |

## 3. 사용예제

```shell
# 파일로 저장
$ tcpdump -w tcp.log

# 파일 확인
$ tcpdump -r tcp.log
14:56:04.225373 IP server1.52817 > 123.22.1.4.https: Flags [.], ack 7104, win 11, options [nop,nop,TS val 3226640684 ecr 4145336950], length 0
14:56:04.233006 IP server1.37618 > server2.8030: Flags [P.], seq 19379:19491, ack 8524, win 11, options [nop,nop,TS val 1000036842 ecr 3876743482], length 112
14:56:04.233264 IP server2.8030 > server1.37618: Flags [P.], seq 8524:8574, ack 19491, win 11, options [nop,nop,TS val 3876743733 ecr 1000036842], length 50
```

## [출처 및 참고]
* <https://wikidocs.net/124139>
