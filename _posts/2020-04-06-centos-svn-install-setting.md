---
title: CentOS SVN 설치 및 설정
author: Hyosik
date: 2020-04-06
categories: [Tools, SCM]
tags: [centos_svn, centos_svn_install_setting, centos_svn_install, centos_svn_setting, svn_설치, svn_설정]
---

## 1. SVN 설치 확인

```bash
[root@localhost ~]# yum list installed subversion
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirror.navercorp.com
 * extras: mirror.navercorp.com
 * updates: mirror.navercorp.com
Error: No matching Packages to list
[root@localhost ~]#
```

## 2. SVN 설치가능 목록 확인

```bash
[root@localhost ~]# yum list subversion
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirror.navercorp.com
 * extras: mirror.navercorp.com
 * updates: mirror.navercorp.com
Available Packages
subversion.i686                                  1.7.14-14.el7                    base
subversion.x86_64                                1.7.14-14.el7                    base
[root@localhost ~]#
```

## 3. SVN 설치

```bash
[root@localhost ~]# yum install subversion.x86_64
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirror.navercorp.com
 * extras: mirror.navercorp.com
 * updates: mirror.navercorp.com
Resolving Dependencies
--> Running transaction check
---> Package subversion.x86_64 0:1.7.14-14.el7 will be installed
--> Processing Dependency: subversion-libs(x86-64) = 1.7.14-14.el7 for package: subversion-1.7.14-14.el7.x86_64
--> Processing Dependency: libsvn_wc-1.so.0()(64bit) for package: subversion-1.7.14-14.el7.x86_64
 
...
 
Installed:
  subversion.x86_64 0:1.7.14-14.el7                                                                                                                                                                          
 
Dependency Installed:
  apr.x86_64 0:1.4.8-5.el7          apr-util.x86_64 0:1.5.2-6.el7          subversion-libs.x86_64 0:1.7.14-14.el7                                    
 
Complete!
[root@localhost ~]#
```

## 4. SVN 디렉터리 및 Repository 생성

```bash
[root@localhost ~]# mkdir /svn/repos
[root@localhost ~]# 
[root@localhost ~]# cd /svn/repos/
[root@localhost svn]#
[root@localhost svn]# svnadmin create --fs-type fsfs repos
[root@localhost svn]# 
[root@localhost svn]# ls
repos
[root@localhost svn]#
```

## 5. SVN 계정 설정

```bash
[root@localhost conf]# cd /svn/repos/conf/
```

### 1) 인증 설정
* svnserve.conf

```bash
[root@localhost conf]# vi svnserve.conf 
[root@localhost conf]#
```

* 설정

```bash
[general]
anon-access = none #익명 사용자 권한 none 권한 없음 
auth-access = write #인증 사용자 권한 write 읽기/쓰기 권한

password-db = passwd #패스워드 설정 파일 passwd

authz-db = authz #권한 설정 파일 authz

realm = My First Repository #인증시 사용자에게 보여질 인증 메세지
```

### 2) 계정 설정
* passwd

```bash
[root@localhost conf]# vi passwd 
[root@localhost conf]#
```

* 추가

```bash
[users]
# harry = harryssecret
# sally = sallyssecret
 
user = 1234
```

### 3) 계정 권한설정
* authz

```bash
[root@localhost conf]# vi authz 
[root@localhost conf]#
```

* 추가

```bash
# [repository:/baz/fuz]
# @harry_and_sally = rw
# * = r
 
[/]
user = rw
```

## 6. SVN 서버 설정
* svnserve

```bash
[root@localhost conf]# vi /etc/sysconfig/svnserve 
[root@localhost conf]#
```

* 설정

```bash
# OPTIONS is used to pass command-line arguments to svnserve.
#
# Specify the repository location in -r parameter:
#OPTIONS="-r /var/svn"
OPTIONS="--threads --root /svn/repos"
```

## 7. 방화벽 설정

### 1) SVN 기본 포트 3690을 열어준다.

```bash
[root@localhost conf]# firewall-cmd --zone=public --permanent --add-port=3690/tcp
success
[root@localhost conf]# 
```

### [참고] 3690 포트 닫기

```bash
[root@localhost conf]# firewall-cmd --zone=public --permanent --remove-port=3690/tcp
success
[root@localhost conf]#
```

### 2) 방화벽 재시작 (설정된 내용 적용)

```bash
[root@localhost ~]# firewall-cmd --reload
success
[root@localhost conf]#
```

### 3) 방화벽 설정 확인

```bash
[root@localhost conf]# firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp2s0
  sources: 
  services: dhcpv6-client ssh
  ports: 3690/tcp
  protocols: 
  masquerade: no
  forward-ports: 
  source-ports: 
  icmp-blocks: 
  rich rules: 
    
[root@localhost conf]#
```

## 8. SVN 서비스 실행 및 설정

### 1) svnserve 시작(재시작)

```bash
[root@localhost ~]# systemctl restart svnserve.service
[root@localhost ~]#
```

### 2) 재부팅시 SVN 자동시작 설정

```bash
[root@localhost conf]# systemctl enable /usr/lib/systemd/system/svnserve.service 
[root@localhost conf]#
```

### 3) SVN 상태 확인

```bash
[root@localhost conf]# ps -ef | grep svnserve
root     30481     1  0 13:48 ?        00:00:00 svnserve -d -r /home/svn/
root     30655 28556  0 13:59 pts/0    00:00:00 grep --color=auto svnserve
[root@localhost conf]#
```

## 출처 및 참고
* <https://sangchul.kr/645>
