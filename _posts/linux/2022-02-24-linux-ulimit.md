---
title: 리눅스 ulimit
author: dejavuhyo
date: 2022-02-24 13:00:00 +0900
categories: [DevOps, Linux]
tags: [linux-ulimit, ulimit, 리눅스-ulimit]
---

## 1. ulimit
ulimit은 사용자가 사용할 수 있는 자원의 한계를 확인하고 설정할 수 있다.

자원 한계는 소프트(soft limit), 하드(hard limit) 두 가지가 있다. 사용 자원이 소프트가 최대 임계치는 아니어서 하드를 넘어서기 전까지는 자원을 유지할 수 있다.

## 2. 사용 예제

### 1) Soft limit

```shell
$ ulimit -a
core file size          (blocks, -c) 0
data seg size           (kbytes, -d) unlimited
scheduling priority             (-e) 0
file size               (blocks, -f) unlimited
pending signals                 (-i) 64058
max locked memory       (kbytes, -l) 65536
max memory size         (kbytes, -m) unlimited
open files                      (-n) 65536
pipe size            (512 bytes, -p) 8
POSIX message queues     (bytes, -q) 819200
real-time priority              (-r) 0
stack size              (kbytes, -s) 8192
cpu time               (seconds, -t) unlimited
max user processes              (-u) 65536
virtual memory          (kbytes, -v) unlimited
file locks                      (-x) unlimited
```

### 2) hard limit

```shell
$ ulimit -aH
core file size          (blocks, -c) unlimited
data seg size           (kbytes, -d) unlimited
scheduling priority             (-e) 0
file size               (blocks, -f) unlimited
pending signals                 (-i) 64058
max locked memory       (kbytes, -l) 65536
max memory size         (kbytes, -m) unlimited
open files                      (-n) 65536
pipe size            (512 bytes, -p) 8
POSIX message queues     (bytes, -q) 819200
real-time priority              (-r) 0
stack size              (kbytes, -s) unlimited
cpu time               (seconds, -t) unlimited
max user processes              (-u) 65536
virtual memory          (kbytes, -v) unlimited
file locks                      (-x) unlimited
```

## 3. 영구 설정 변경
`/etc/security/limits.conf` 파일을 수정하여 영구적인 설정을 변경할 수 있다.

```shell
$ cat /etc/security/limits.conf
# /etc/security/limits.conf
```

```text
#Each line describes a limit for a user in the form:
#
#<domain>        <type>  <item>  <value>
#
#Where:
#<domain> can be:
#        - an user name
#        - a group name, with @group syntax
#        - the wildcard *, for default entry
#        - the wildcard %, can be also used with %group syntax,
#                 for maxlogin limit
#
#<type> can have the two values:
#        - "soft" for enforcing the soft limits
#        - "hard" for enforcing hard limits
#
#<item> can be one of the following:
#        - core - limits the core file size (KB)
#        - data - max data size (KB)
#        - fsize - maximum filesize (KB)
#        - memlock - max locked-in-memory address space (KB)
#        - nofile - max number of open files
#        - rss - max resident set size (KB)
#        - stack - max stack size (KB)
#        - cpu - max CPU time (MIN)
#        - nproc - max number of processes
#        - as - address space limit (KB)
#        - maxlogins - max number of logins for this user
#        - maxsyslogins - max number of logins on the system
#        - priority - the priority to run user process with
#        - locks - max number of file locks the user can hold
#        - sigpending - max number of pending signals
#        - msgqueue - max memory used by POSIX message queues (bytes)
#        - nice - max nice priority allowed to raise to values: [-20, 19]
#        - rtprio - max realtime priority
#
#<domain>      <type>  <item>         <value>
#

#*               soft    core            0
#*               hard    rss             10000
#@student        hard    nproc           20
#@faculty        soft    nproc           20
#@faculty        hard    nproc           50
#ftp             hard    nproc           0
#@student        -       maxlogins       4

# nofile
*               soft    nofile          65536
*               hard    nofile          65536
root            soft    nofile          65536
root            hard    nofile          65536

# nproc
*               soft    nproc           65536
*               hard    nproc           65536
root            soft    nproc           65536
root            hard    nproc           65536
# End of file
```

## [출처 및 참고]
* <https://wikidocs.net/156944>
