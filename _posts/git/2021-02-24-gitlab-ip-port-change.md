---
title: GitLab IP, Port 변경
author: dejavuhyo
date: 2021-02-24 06:10:00 +0900
categories: [DevOps, Git]
tags: [gitlab-ip-change, gitlab-port-change, ip-port-change, gitlab-ip-변경, gitlab-port-변경, ip-port-변경]
---

## 1. 편집

```bash
[root@localhost ~]# vi /etc/gitlab/gitlab.rb 
```

## 2. 수정

### 1) external_url 변경

```text
## GitLab URL
##! URL on which GitLab will be reachable.
##! For more details on configuring external_url see:
##! https://docs.gitlab.com/omnibus/settings/configuration.html#configuring-the-external-url-for-gitlab
##!
##! Note: During installation/upgrades, the value of the environment variable
##! EXTERNAL_URL will be used to populate/replace this value.
##! On AWS EC2 instances, we also attempt to fetch the public hostname/IP
##! address from AWS. For more details, see:
##! https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instancedata-data-retrieval.html
external_url 'http://localhost:8001'
```

### 2) IP, Port 변경

```text
################################################################################
## GitLab Unicorn
##! Tweak unicorn settings.
##! Docs: https://docs.gitlab.com/omnibus/settings/unicorn.html
################################################################################

### Advanced settings
unicorn['listen'] = 'localhost'
unicorn['port'] = 8001
# unicorn['socket'] = '/var/opt/gitlab/gitlab-rails/sockets/gitlab.socket'
# unicorn['pidfile'] = '/opt/gitlab/var/unicorn/unicorn.pid'
# unicorn['tcp_nopush'] = true
# unicorn['backlog_socket'] = 1024

###! **Make sure somaxconn is equal or higher then backlog_socket**
# unicorn['somaxconn'] = 1024
```

## 3. 설정 적용

```bash
[root@localhost ~]# gitlab-ctl reconfigure
```

## 4. 방화벽 설정

### 1) 포트 추가

```bash
[root@localhost ~]# firewall-cmd --permanent --zone=public --add-port=8001/tcp
```

### 2) Open 확인

```bash
[root@localhost ~]# firewall-cmd --list-all
```

### 3) 방화벽 재시작

```bash
[root@localhost ~]# systemctl restart firewalld
```

## [출처 및 참고]
* <https://uxgjs.tistory.com/161>
