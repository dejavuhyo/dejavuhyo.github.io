---
title: CentOS Yum Repository 추가 및 삭제
author: dejavuhyo
date: 2020-12-03 10:33:00 +0900
categories: [DevOps, Linux]
tags: [yum-repository, yum-repository-add, yum-repository-delete, centos-yum, centos-yum-repository, yum-repository-추가, yum-repository-삭제]
---

## 1. Yum Repository란
Yum Repository는 Package를 모아놓은 저장소이다. Yum을 통해 Package 설치 시 활성화된 Yum Repository에서 다운로드하여 설치하기 때문에 Package가 Repository에 없으면 설치를 할 수 없다.

Default Repository가 아닌 EPEL(Extra Package for Enterprise Linux), REMI 등 다른 Repository를 추가하여 원하는 Package 또는 최신 버전의 Package를 설치할 수 있다.

## 2. Yum Repository 확인

### 1) 활성화된 Yum Repository

* yum repolist

```shell
[root@localhost ~]# yum repolist
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirror.kakao.com
 * extras: mirror.kakao.com
 * updates: mirror.kakao.com
repo id                                                              repo name                                                               status
base/7/x86_64                                                        CentOS-7 - Base                                                         10,072
extras/7/x86_64                                                      CentOS-7 - Extras                                                          448
updates/7/x86_64                                                     CentOS-7 - Updates                                                         773
repolist: 11,293
```

### 2) Yum Repository 위치

* `/etc/yum.repos.d`

```shell
[root@localhost ~]# cd /etc/yum.repos.d/
[root@localhost yum.repos.d]# ls -al
합계 52
drwxr-xr-x.   2 root root  220 10월  2 02:03 .
drwxr-xr-x. 139 root root 8192 12월  3 10:42 ..
-rw-r--r--.   1 root root 1664 10월 23 23:53 CentOS-Base.repo
-rw-r--r--.   1 root root 1309 10월 23 23:53 CentOS-CR.repo
-rw-r--r--.   1 root root  649 10월 23 23:53 CentOS-Debuginfo.repo
-rw-r--r--.   1 root root  630 10월 23 23:53 CentOS-Media.repo
-rw-r--r--.   1 root root 1331 10월 23 23:53 CentOS-Sources.repo
-rw-r--r--.   1 root root 8515 10월 23 23:53 CentOS-Vault.repo
-rw-r--r--.   1 root root  314 10월 23 23:53 CentOS-fasttrack.repo
-rw-r--r--.   1 root root  616 10월 23 23:53 CentOS-x86_64-kernel.repo
```

## 3. Yum Repository 추가
Yum Repository는 수동으로 입력하여 추가하는 방법과 RPM을 이용하여 설치하는 방법이 있다.

`/etc/yum.repos.d/[레파지토리 이름].repo` 형태로 파일을 생성한다.

```text
name=저장소 표시 이름
baseurl=저장소 주소
enabled=활성화 여부 (0 or 1)
gpgcheck=gpg 서명키 사용 여부 (0 or 1)
gpgkey=서명키 사용 시 서명키 주소
```

* CentOS-Base.repo의 내용

```shell
[root@localhost yum.repos.d]# vi CentOS-Base.repo
```

```text
# CentOS-Base.repo
#
# The mirror system uses the connecting IP address of the client and the
# update status of each mirror to pick mirrors that are updated to and
# geographically close to the client.  You should use this for CentOS updates
# unless you are manually picking other mirrors.
#
# If the mirrorlist= does not work for you, as a fall back you can try the
# remarked out baseurl= line instead.
#
#

[base]
name=CentOS-$releasever - Base
mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
#baseurl=http://mirror.centos.org/centos/$releasever/os/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

#released updates
[updates]
name=CentOS-$releasever - Updates
mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
#baseurl=http://mirror.centos.org/centos/$releasever/updates/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

#additional packages that may be useful
[extras]
name=CentOS-$releasever - Extras
mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
#baseurl=http://mirror.centos.org/centos/$releasever/extras/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

#additional packages that extend functionality of existing packages
[centosplus]
name=CentOS-$releasever - Plus
mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus&infra=$infra
#baseurl=http://mirror.centos.org/centos/$releasever/centosplus/$basearch/
gpgcheck=1
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
```

## 4. EPEL, REMI 저장소 추가
REMI 저장소는 EPEL 저장소 의존성이 있다. 따라서 EPEL 저장소를 먼저 설치해야 REMI 저장소를 설치할 수 있다.

### 1) EPEL 저장소
EPEL 저장소는 확장된 패키지의 저장소로 기본 저장소에는 없는 패키지들이 저장되어 있다.

* 설치

```shell
[root@localhost ~]# yum install epel-release
```

* 확인

```shell
[root@localhost ~]# yum repolist
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
epel/x86_64/metalink                                                                                                        | 3.5 kB  00:00:00     
 * base: mirror.kakao.com
 * epel: ftp.riken.jp
 * extras: mirror.kakao.com
 * updates: mirror.anigil.com
epel                                                                                                                        | 4.7 kB  00:00:00     
(1/3): epel/x86_64/group_gz                                                                                                 |  95 kB  00:00:00     
(2/3): epel/x86_64/updateinfo                                                                                               | 1.0 MB  00:00:00     
(3/3): epel/x86_64/primary_db                                                                                               | 6.9 MB  00:00:00     
repo id                                                repo name                                                                             status
base/7/x86_64                                          CentOS-7 - Base                                                                       10,072
epel/x86_64                                            Extra Packages for Enterprise Linux 7 - x86_64                                        13,483
extras/7/x86_64                                        CentOS-7 - Extras                                                                        448
updates/7/x86_64                                       CentOS-7 - Updates                                                                       773
repolist: 24,776
```

### 2) REMI 저장소
REMI 저장소는 최신 버전의 패키지가 저장되어있다. 기본 저장소에는 오래된 버전이 올라가 있는 경우가 많다. 최신 버전의 패키지가 필요할 경우 REMI 저장소를 설치하게 된다.

Yum 저장소에는 REMI 저장소 패키지가 없기 때문에 rpm 파일을 따로 다운로드받아서 설치해야한다.

* OS 버전별 패키지 파일 URL
  - CentOS 5: `http://rpms.famillecollet.com/enterprise/remi-release-5.rpm`
  - CentOS 6: `http://rpms.famillecollet.com/enterprise/remi-release-6.rpm`
  - CentOS 7: `http://rpms.famillecollet.com/enterprise/remi-release-7.rpm`

* 설치

```shell
[root@localhost ~]# rpm -ivh http://rpms.famillecollet.com/enterprise/remi-release-7.rpm
http://rpms.famillecollet.com/enterprise/remi-release-7.rpm(을)를 복구합니다
경고: /var/tmp/rpm-tmp.YfjNjp: Header V4 DSA/SHA1 Signature, key ID 00f97f56: NOKEY
준비 중...                         ################################# [100%]
Updating / installing...
   1:remi-release-7.8-1.el7.remi      ################################# [100%]
```

서명키를 통과하지 못해 `warning: /var/tmp/rpm-tmp.ronYwh: Header V3 DSA/SHA1 Signature` 에러가 나지만 설치된 목록을 조회해보면 설치되어있다고 나온다.

```shell
[root@localhost ~]# rpm -qa | grep remi-release
remi-release-7.8-1.el7.remi.noarch
```

저장소 디렉토리에도 EPEL, REMI 저장소 파일들이 생성되어있다.

```shell
[root@localhost ~]# ls -al /etc/yum.repos.d/
합계 120
drwxr-xr-x.   2 root root 4096 12월  3 11:57 .
drwxr-xr-x. 139 root root 8192 12월  3 10:42 ..
-rw-r--r--.   1 root root 1664 10월 23 23:53 CentOS-Base.repo
-rw-r--r--.   1 root root 1309 10월 23 23:53 CentOS-CR.repo
-rw-r--r--.   1 root root  649 10월 23 23:53 CentOS-Debuginfo.repo
-rw-r--r--.   1 root root  630 10월 23 23:53 CentOS-Media.repo
-rw-r--r--.   1 root root 1331 10월 23 23:53 CentOS-Sources.repo
-rw-r--r--.   1 root root 8515 10월 23 23:53 CentOS-Vault.repo
-rw-r--r--.   1 root root  314 10월 23 23:53 CentOS-fasttrack.repo
-rw-r--r--.   1 root root  616 10월 23 23:53 CentOS-x86_64-kernel.repo
-rw-r--r--.   1 root root 1050 10월  3  2017 epel-testing.repo
-rw-r--r--.   1 root root  951 10월  3  2017 epel.repo
-rw-r--r--.   1 root root  446  8월 17 19:29 remi-glpi91.repo
-rw-r--r--.   1 root root  446  8월 17 19:29 remi-glpi92.repo
-rw-r--r--.   1 root root  446  8월 17 19:29 remi-glpi93.repo
-rw-r--r--.   1 root root  446  8월 17 19:29 remi-glpi94.repo
-rw-r--r--.   1 root root  855  8월 17 19:29 remi-modular.repo
-rw-r--r--.   1 root root  456  8월 17 19:29 remi-php54.repo
-rw-r--r--.   1 root root 1314  8월 17 19:29 remi-php70.repo
-rw-r--r--.   1 root root 1314  8월 17 19:29 remi-php71.repo
-rw-r--r--.   1 root root 1314  8월 17 19:29 remi-php72.repo
-rw-r--r--.   1 root root 1314  8월 17 19:29 remi-php73.repo
-rw-r--r--.   1 root root 1314  8월 17 19:29 remi-php74.repo
-rw-r--r--.   1 root root 1314  8월 17 19:29 remi-php80.repo
-rw-r--r--.   1 root root  750  8월 17 19:29 remi-safe.repo
-rw-r--r--.   1 root root 2605  8월 17 19:29 remi.repo
```

EPEL 저장소 하나밖에 등록되지 않는 것을 볼 수 있다. 저장소 파일은 있어도 실제로는 REMI 저장소가 등록되어있지 않는 것이다.

REMI 저장소를 새로 설치하면 활성화되지 않은 채 설치된다. 이 경우 REMI 저장소 파일에서 활성화시켜주면 된다.

```shell
[root@localhost ~]# vi /etc/yum.repos.d/remi.repo
```

* enabled=0을 1로 변경

```text
# Repository: http://rpms.remirepo.net/
# Blog:       http://blog.remirepo.net/
# Forum:      http://forum.remirepo.net/

[remi]
name=Remi's RPM repository for Enterprise Linux 7 - $basearch
#baseurl=http://rpms.remirepo.net/enterprise/7/remi/$basearch/
#mirrorlist=https://rpms.remirepo.net/enterprise/7/remi/httpsmirror
mirrorlist=http://cdn.remirepo.net/enterprise/7/remi/mirror
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-remi
```

* 확인

```shell
[root@localhost ~]# yum repolist
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirror.kakao.com
 * epel: ftp.jaist.ac.jp
 * extras: mirror.kakao.com
 * remi: ftp.riken.jp
 * remi-safe: ftp.riken.jp
 * updates: mirror.navercorp.com
remi                                                                                                                                                    | 3.0 kB  00:00:00     
remi/primary_db                                                                                                                                         | 2.8 MB  00:00:00     
repo id                                                        repo name                                                                                                 status
base/7/x86_64                                                  CentOS-7 - Base                                                                                           10,072
epel/x86_64                                                    Extra Packages for Enterprise Linux 7 - x86_64                                                            13,483
extras/7/x86_64                                                CentOS-7 - Extras                                                                                            448
remi                                                           Remi's RPM repository for Enterprise Linux 7 - x86_64                                                      6,464
remi-safe                                                      Safe Remi's RPM repository for Enterprise Linux 7 - x86_64                                                 4,033
updates/7/x86_64                                               CentOS-7 - Updates                                                                                           773
repolist: 35,273
```

## 5. Yum Repository 삭제
`rm -rf [레파지토리 파일]` 명령어로 삭제 한다. base.repo 파일은 삭제하면 안된다.

* 삭제

```shell
[root@localhost yum.repos.d]# rm -rf remi.* remi-*
```

* Cache 파일 삭제

```shell
[root@localhost yum.repos.d]# cd /var/cache/yum/
[root@localhost yum]# ls 
x86_64
[root@localhost yum]# rm -rf x86_64/
```

* Yum headers, packages, metadata 삭제

```shell
[root@localhost yum]# yum clean headers
Loaded plugins: fastestmirror, langpacks
Cleaning repos: base epel extras updates
0 header files removed
[root@localhost yum]# yum clean packages
Loaded plugins: fastestmirror, langpacks
Cleaning repos: base epel extras updates
0 package files removed
[root@localhost yum]# yum clean metadata
Loaded plugins: fastestmirror, langpacks
Cleaning repos: base epel extras updates
0 metadata files removed
0 sqlite files removed
0 metadata files removed
```

## [출처 및 참고]
* [https://conory.com/blog/42596](https://conory.com/blog/42596)
* [https://ossian.tistory.com/80](https://ossian.tistory.com/80)
