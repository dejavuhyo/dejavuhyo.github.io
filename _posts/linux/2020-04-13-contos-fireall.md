---
title: CentOS 방화벽(firewall) 설정
author: Hyosik
date: 2020-04-13 11:00:00 +0900
categories: [OS, Linux]
tags: [centos-firewall, firewall-setting, firewall, centos-방화벽, 방화벽-설정]
---

## 1. 설치

```shell
[root@localhost ~]# yum list install firewalld
[root@localhost ~]# systemctl start firewalld
[root@localhost ~]# systemctl enable firewalld
```

## 2. 설정

### 1) 기본 설정
/usr/lib/firewalld/ 에 있으며, 미리 설정된 zone과 zone 별 허용하는 서비스등은 이 폴더를 보면 확인 할 수 있다.

### 2) 시스템 개별 설정
/etc/firewalld/ 에 있으며 default zone 등 firewall 의 동작은 /etc/firewalld/firewalld.conf 에서 지정할 수 있으며, 예로 default zone을 dmz로 변경하려면 아래와 같이 설정하고 firewalld 를 재시작하면 된다.

* 편집

```shell
[root@localhost ~]# vi /etc/firewalld/firewalld.conf
```

* 수정

```text
# default zone
# The default zone used if an empty zone string is used.
# Default: public
DefaultZone=dmz
```

### 3)  커스터마이징 zone 설정
/etc/firewalld/zones/[ZONE].xml 에서 확인할 수 있으며, 예로 public zone은 /etc/firewalld/zones/public.xml 에 설정 한다.

* 편집

```shell
[root@localhost ~]# vi /etc/firewalld/zones/public.xml
```

* 수정

```xml
<?xml version="1.0" encoding="utf-8"?>
<zone>
  <short>Public</short>
  <description>For use in public areas. You do not trust the other computers on networks to not harm your computer. Only selected incoming connections are accepted.</description>
  <service name="ssh"/>
  <service name="dhcpv6-client"/>
  <service name="http"/>
  <service name="https"/>
  <port protocol="tcp" port="3690"/>
  <port protocol="tcp" port="9090"/>
  <port protocol="tcp" port="8001"/>
</zone>
```

## 3. 재시작
서비스 재구동시 firewall-cmd 명령어 사용

```shell
[root@localhost ~]# firewall-cmd --reload
```

## 4. 포트

### 1) 포트 추가

```shell
[root@localhost ~]# firewall-cmd --permanent --zone=public --add-port=8080/tcp
```

포트를 범위로 지정하려면 - 구분자를 넣어서 설정 (4000 ~ 4100 포트 오픈)

```shell
[root@localhost ~]# firewall-cmd --permanent --zone=public --add-port=4000-4100/tcp
```

### 2) 포트 삭제

```shell
[root@localhost ~]# firewall-cmd --permanent --zone=public --remove-port=8080/tcp
```

### 3) 허용 IP 추가

```shell
[root@localhost ~]# firewall-cmd --permanent --zone=public --add-source=192.168.1.0/24 --add-port=22/tcp
```

## 5. Zone
Zone은 서버의 용도에 맞게 사전에 정의된 네트워크의 신뢰 레벨을 의미

### 1) Zone 목록 보기

```shell
[root@localhost ~]# firewall-cmd --get-zones
```

### 2) 전체 Zone 목록 상세 보기

```shell
[root@localhost ~]# firewall-cmd --list-all-zone
```

### 3) 기본(default) Zone 보기

```shell
[root@localhost ~]# firewall-cmd --get-default-zone
```

### 4) 활성화된 Zone 보기

```shell
[root@localhost ~]# firewall-cmd --get-active-zone
```

### 5) 새로운 Zone 추가

```shell
[root@localhost ~]# firewall-cmd --permanent --new-zone=webserver
```

### 6) Zone 삭제

```shell
[root@localhost ~]# firewall-cmd --permanent --delete-zone=webserver
```

## 6. 서비스 목록

### 1) 사전에 정의된 목록 보기

```shell
[root@localhost ~]# firewall-cmd --get-services
```

### 2) permanent로 등록된 서비스 목록 보기
dmz 존에 영구적으로 등록된 서비스 목록 확인

```shell
[root@localhost ~]# firewall-cmd --permanent --list-all --zone=dmz
```

### 3) 서비스 추가

```shell
[root@localhost ~]# firewall-cmd --permanent --zone=dmz --add-service=http
```

### 4) 서비스 삭제

```shell
[root@localhost ~]# firewall-cmd --permanent --zone=dmz --remove-service=http
```

## 출처 및 참고
* <https://www.lesstif.com/system-admin/linux-unix/rhel-centos-tips-tricks/rhel-centos-7-firewalld>
