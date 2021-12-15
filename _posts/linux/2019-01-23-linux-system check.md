---
title: CentOS 시스템 확인
author: dejavuhyo
date: 2019-01-23 17:40:00 +0900
categories: [DevOps, Linux]
tags: [linux-system-check, system-check, 리눅스-시스템-확인, 시스템-확인]
---

## 1. 운영체제 버전 확인

```bash
[root@localhost ~]# cat /etc/*release*
CentOS Linux release 7.6.1810 (Core) 
Derived from Red Hat Enterprise Linux 7.6 (Source)
NAME="CentOS Linux"
VERSION="7 (Core)"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="7"
PRETTY_NAME="CentOS Linux 7 (Core)"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:7"
HOME_URL="https://www.centos.org/"
BUG_REPORT_URL="https://bugs.centos.org/"
 
CENTOS_MANTISBT_PROJECT="CentOS-7"
CENTOS_MANTISBT_PROJECT_VERSION="7"
REDHAT_SUPPORT_PRODUCT="centos"
REDHAT_SUPPORT_PRODUCT_VERSION="7"
 
CentOS Linux release 7.6.1810 (Core) 
CentOS Linux release 7.6.1810 (Core) 
cpe:/o:centos:centos:7
```

## 2. 커널 버전 확인

```bash
[root@localhost ~]# cat /proc/version
Linux version 3.10.0-957.el7.x86_64 (mockbuild@kbuilder.bsys.centos.org) (gcc version 4.8.5 20150623 (Red Hat 4.8.5-36) (GCC) ) #1 SMP Thu Nov 8 23:39:32 UTC 2018
```

## 3. 시스템 비트 확인

```bash
[root@localhost ~]# arch
x86_64
```

## 4. CPU 정보

```bash
[root@localhost ~]# cat /proc/cpuinfo
processor    : 0
vendor_id    : GenuineIntel
cpu family    : 6
model        : 58
model name    : Intel(R) Core(TM) i3-3220 CPU @ 3.30GHz
stepping    : 9
microcode    : 0x1f
cpu MHz        : 3300.000
cache size    : 3072 KB
physical id    : 0
```

## 5. 메모리 정보

```bash
[root@localhost ~]# cat /proc/meminfo
MemTotal:        3861508 kB
MemFree:         1431064 kB
MemAvailable:    2692408 kB
Buffers:            4180 kB
Cached:          1455548 kB
SwapCached:            0 kB
Active:          1020512 kB
Inactive:        1046528 kB
Active(anon):     608544 kB
Inactive(anon):    37092 kB
Active(file):     411968 kB
Inactive(file):  1009436 kB
Unevictable:           0 kB
```

## 6. 파티션 정보

```bash
[root@localhost ~]# cat /proc/partitions
major minor  #blocks  name
 
  11        0    1048575 sr0
   8        0   41943040 sda
   8        1    1048576 sda1
   8        2   40893440 sda2
 253        0   38789120 dm-0
 253        1    2097152 dm-1
```

## 7. 호스트 네임

```bash
[root@localhost ~]# hostname
localhost.localdomain
```

## [출처 및 참고]
* <https://webdir.tistory.com/103>
