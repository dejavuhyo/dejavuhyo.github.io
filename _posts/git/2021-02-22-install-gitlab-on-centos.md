---
title: CentOS GitLab 설치
author: dejavuhyo
date: 2021-02-23 06:05:00 +0900
categories: [DevOps, Git]
tags: [centos-gitlab, centos-gitlab-install, gitlab-install, centos-gitlab-설치, gitlab-설치]
---

## 1. CentOS 8 패키지 업데이트

![centos-8](/assets/img/2021-02-23-install-gitlab-on-centos/centos-8.png)

### 1) CentOS 8 소프트웨어 패키지 업데이트

```shell
[root@localhost ~]# dnf update
```
### 2) 운영 체제 버전 확인

```shell
[root@localhost ~]# cat /etc/redhat-release 
CentOS Linux release 8.3.2011
```

## 2. 설치 및 구성

![gitlab-logo](/assets/img/2021-02-23-install-gitlab-on-centos/gitlab-logo.png)

### 1) 종속성 설치
OpenSSH 패키지 및 Postfix를 포함하여 GitLab에 필요한 패키지 종속성을 설치한다.

```shell
[root@localhost ~]# dnf install -y curl policycoreutils openssh-server perl
[root@localhost ~]# systemctl enable sshd
[root@localhost ~]# systemctl start sshd

# Check if opening the firewall is needed with: systemctl status firewalld
[root@localhost ~]# firewall-cmd --permanent --add-service=http
[root@localhost ~]# firewall-cmd --permanent --add-service=https
[root@localhost ~]# systemctl reload firewalld
```

### 2) 이메일 알림 설정
Postfix를 설치하여 알림 이메일을 보낸다. 다른 솔루션을 사용하여 이메일을 보내려면 이 단계를 건너 뛰고 GitLab을 설치한 후 외부 SMTP 서버를 구성해야 한다.

```shell
[root@localhost ~]# dnf install postfix
[root@localhost ~]# systemctl enable postfix
[root@localhost ~]# systemctl start postfix
```

## 3.패키지 저장소 추가 및 설치
GitLab 패키지 저장소를 추가하고 패키지를 설치한다.

### 1) 저장소 추가

```shell
[root@localhost ~]# curl -sS https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.rpm.sh | sudo bash
```

### 2) 패키지 설치
DNS를 올바르게 설정했는지 확인하고 '도메인 혹은 IP'를 GitLab 인스턴스에 액세스할 URL로 변경한다. 설치시 해당 URL에서 GitLab이 자동으로 구성되고 시작된다.

https:// URL의 경우 GitLab이 암호화를 사용하여 인증서를 자동으로 요청하므로 인바운드 HTTP 액세스와 유효한 호스트 이름이 필요하다. 사용자 자신의 인증서를 사용하거나 http://를 사용할 수도 있다.

```shell
[root@localhost ~]# EXTERNAL_URL="도메인 혹은 IP" dnf install -y gitlab-ce
```

## 4. 로그인
처음 방문하면 비밀번호 재설정 화면으로 리디렉션된다. 초기 관리자 계정의 암호를 제공하면 로그인 화면으로 다시 리디렉션된다. 기본 계정의 사용자 이름 root을 사용하여 로그인한다.

## 출처 및 참고
* <https://about.gitlab.com/install/?version=ce#centos-8>
* <https://www.centos.org/>
