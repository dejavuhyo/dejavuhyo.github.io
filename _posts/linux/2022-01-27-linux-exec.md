---
title: 리눅스 exec
author: dejavuhyo
date: 2022-01-27 09:30:00 +0900
categories: [DevOps, Linux]
tags: [linux-exec, exec, 리눅스-exec]
---

## 1. exec
주어진 명령어를 실행하는데 새로운 프로세스를 생성하지 않고, 쉘 프로세스를 대체한다. 예를 들어 bash 쉘에서 자바 프로그램을 실행하면 자바 프로그램의 ppid가 bash 쉘이 되고, 자바 프로그램이 bash 쉘의 하위 프로세스로 실행된다.

exec 커맨드로 실행하면 bash쉘의 프로세스가 자바 프로그램이 된다. ppid가 따로 없다. 그리고 자바 프로그램이 종료되면 프로세스가 종료된다. bash 쉘로 돌아오지 않는다.

다음의 상황을 살펴보면 macbook에서 masternode로 접근하여 echo를 실행하면 masternode의 bash쉘이다. exec로 명령을 실행하면 bash쉘이 echo 명령으로 대체되고, 종료되면 bash쉘이 없기 때문에 다시 macbook으로 돌아온다.

```shell
➜  macbook ✗ ssh root@masternode
[root@masternode ~]# echo "aa"
aa
[root@masternode ~]# exec echo "aa"
aa
➜  macbook ✗
```

## 2. 주요 옵션

| 옵션 | 설명 |
|:---:|:---:|
| `-c` | 환경 변수가 없는 상태로 실행 |
| `-a [name]` | 0번째 인수로 이름을 전달 |
| `-l` | 0번째 인수로 대쉬를 전달 |

## 3. 사용예제

```shell
$ exec echo "aa"
aa

$ exec java -cp '/etc/hadoop/conf' org.apache.hadoop.hdfs.server.datanode.SecureDataNodeStarter
```

## [출처 및 참고]
* <https://wikidocs.net/111050>
