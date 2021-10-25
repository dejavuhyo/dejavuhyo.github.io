---
title: CentOS 7 Nginx 설치
author: dejavuhyo
date: 2021-08-25 06:00:00 +0900
categories: [DevOps, WAS]
tags: [centos-nginx-install, nginx-install, centos-nginx, nginx-설치, centos-nginx-설치]
---

## 1. 외부 저장소 추가
yum 저장소에는 nginx가 없기 때문에 외부 저장소를 추가 한다.

```bash
[root@localhost ~]# vi /etc/yum.repos.d/nginx.repo
```

```/etc/yum.repos.d``` 경로에 nginx.repo 파일을 추가하고 아래와 같이 작성한다.

* CentOS 7

```text
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/centos/7/$basearch/
gpgcheck=0
enabled=1
```

* CentOS

```text
[nginx]
name=nginx repo
baseurl=https://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=0
enabled=1
```

* Linux

```text
[nginx]
name=nginx repo
baseurl=https://nginx.org/packages/rhel/$releasever/$basearch/
gpgcheck=0
enabled=1
```

> CentOS, RHEL 및 Scientific Linux가 $releasever 변수를 채우는 방법 간의 차이로 인해 OS 버전에 따라 $releasever를 수동으로 5(5.x의 경우) 또는 6(6.x의 경우)으로 교체해야 한다.

## 2. yum install
yum install 명령어를 사용하여 nginx를 설치한다.

```bash
[root@localhost ~]# yum install -y nginx
```

## 3. 방화벽 포트 개방
포트 개방, 방화벽 재시작, 개방된 포트 목록 확인이다. 8089번 포트를 개방하고 nginx에서 사용 예정이다.

```bash
[root@localhost ~]# firewall-cmd --permanent --zone=public --add-port=8089/tcp
[root@localhost ~]# firewall-cmd --reload
[root@localhost ~]# firewall-cmd --list-ports
```

## 4. Nginx 포트 설정
nginx 설정 파일의 Listen 포트 80을 8089로 변경한다.

```bash
[root@localhost ~]# vi /etc/nginx/conf.d/default.conf
```

## 5. Nginx 실행
서버 부팅 시 자동으로 nginx 서비스를 구동하기 위해서 systemctl enable 명령어를 실행한다.

```bash
[root@localhost ~]# systemctl start nginx
[root@localhost ~]# systemctl enable nginx
```

## 6. 오류
데몬 실행 시 다음과 같은 오류가 발생할 수 있다.

```text
Job for nginx.service failed because the control process exited with error code. See "systemctl status nginx.service" and "journalctl -xe" for details.
```

오류 상세 정보를 확인한다.

```bash
[root@localhost ~]# journalctl -xe
```

8089포트가 권한이 없다고 하는 내용이다. SElinux 사용 시 포트 등록이 안되어 있어서 발생하는 오류이다.

semanage로 포트를 등록한다.

```bash
[root@localhost ~]# semanage port -a -t http_port_t -p tcp 8089
```

## [출처 및 참고]
* <https://cofs.tistory.com/412>
* <https://www.nginx.com/resources/wiki/start/topics/tutorials/install/>
