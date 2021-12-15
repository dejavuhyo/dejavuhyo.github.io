---
title: VMWare에서 yum 명령어 에러 (Could not retrieve mirrorlist)
author: dejavuhyo
date: 2019-01-23 18:05:00 +0900
categories: [DevOps, Linux]
tags: [yum-command-error, vmware-yum-error, could-not-retrieve-mirrorlist, vmware-yum-명령어-에러, yum-명령어-에러]
---

## 1. 에러 메시지

```bash
[root@localhost ~]# yum list java*jdk-devel
Loaded plugins: fastestmirror, langpacks
Determining fastest mirrors
Could not retrieve mirrorlist http://mirrorlist.centos.org/?release=7&arch=x86_64&repo=os&infra=stock error was
14: curl#6 - "Could not resolve host: mirrorlist.centos.org; 알 수 없는 오류"
 
 
 One of the configured repositories failed (Unknown),
 and yum doesn't have enough cached data to continue. At this point the only
 safe thing yum can do is fail. There are a few ways to work "fix" this:
     1. Contact the upstream for the repository and get them to fix the problem.
     2. Reconfigure the baseurl/etc. for the repository, to point to a working
        upstream. This is most often useful if you are using a newer
        distribution release than is supported by the repository (and the
        packages for the previous distribution release still work).
     3. Run the command with the repository temporarily disabled
            yum --disablerepo=<repoid> ...
     4. Disable the repository permanently, so yum won't use it by default. Yum
        will then just ignore the repository until you permanently enable it
        again or use --enablerepo for temporary usage:
 
            yum-config-manager --disable <repoid>
        or
            subscription-manager repos --disable=<repoid>
 
     5. Configure the failing repository to be skipped, if it is unavailable.
        Note that yum will try to contact the repo. when it runs most commands,
        so will have to try and fail each time (and thus. yum will be be much
        slower). If it is a very temporary problem though, this is often a nice
        compromise:
 
            yum-config-manager --save --setopt=<repoid>.skip_if_unavailable=true
 
Cannot find a valid baseurl for repo: base/7/x86_64
```

## 2. 해결 방법
```ONBOOT=no```를 yes로 변경 후 저장

```bash
[root@localhost ~]# vi /etc/sysconfig/network-scripts/ifcfg-ens33
```

```text
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=dhcp
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=ens33
UUID=35a546f9-fe9e-4a4c-aa25-a9ca5c3b8987
DEVICE=ens33
ONBOOT=yes
```

## [출처 및 참고]
* <https://skysoo1111.tistory.com/36>
