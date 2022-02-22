---
title: 리눅스 systemctl
author: dejavuhyo
date: 2022-02-22 09:30:00 +0900
categories: [DevOps, Linux]
tags: [linux-systemctl, systemctl, 리눅스-systemctl]
---

## 1. systemctl
리눅스 시스템이 실행 되고 가장먼저 실행되어 다른 프로세스를 실행하는 역할을 하는 것이 systemd(system daemon)이다.

이 프로세스로 프로그램을 서비스로 등록할 때 사용할 수 있는 명령어가 systemctl이다.

## 2. 사용예제

### 1) 서비스 파일
시스템에 서비스로 등록하기 위해서는 서비스 파일을 생성해야 한다.

주키퍼 프로세스를 시스템 서비스로 등록하기 위해서는 다음과 같은 파일을 생성하고 `/etc/systemd/system/zookeeper.service`에 저장한다.

```text
[Unit]
Description=Zookeeper

[Service]
Type=forking
User=foo
ExecStart=/opt/zookeeper/bin/zkServer.sh start

[Install]
WantedBy=multi-user.target
```

### 2) 서비스 파일에 환경변수 추가

```text
Environment=ZOKEEPER_CONF=/etc/zookeeper/conf
ExecStart=/opt/zookeeper/bin/zkServer.sh --conf ${ZOKEEPER_CONF} start
```

### 3) 서비스 등록
등록된 서비스를 실행, 정지하는 명령은 다음과 같다.

```shell
# 실행
$ systemctl start zookeeper

# 정지
$ systemctl stop zookeeper

# 상태 확인
$ systemctl status zookeeper

# active 상태 확인
$ systemctl is-active zookeeper
```

### 4) 서비스 삭제
서비스 삭제는 서비스 파일을 삭제하고 리로드하면 된다.

```shell
# 서비스 자동 실행을 제거
$ systemctl disable zookeeper

# 서비스 등록 파일 삭제
sudo rm /etc/systemd/system/zookeeper.service

# 서비스 제거
systemctl daemon-reload
systemctl reset-failed
```

### 5) 서비스 목록 확인

```shell
# 등록한 서비스 목록을 확인
systemctl list-units --type service --all

# 상태가 not-found 인 서비스 목록 확인
systemctl --state=not-found --all
```

## 3. 서비스 옵션

### 1) Service Type 옵션

* __simple:__ 자체 백그라운드를 수행하지 않고 셸에 연결된 상태로 유지되는 장기 실행 프로세스이다.

* __forking:__ 자신을 실행한 프로세스에서 분리하여 자신을 효과적으로 배경화하는 일반적인 데몬이다. 자식 프로세스까지 부모가 관리 함.

* __oneshot:__ 종료될 것으로 예상되는 단기 프로세스이다.

* __dbus:__ 간단하지만 프로세스 시작 완료 알림은 dbus를 통해 전송된다.

* __notify:__ 간단하지만 프로세스 시작 완료 알림은 inotify를 통해 전송된다.

* __idle:__ 간단하지만 바이너리는 작업이 발송된 후에 시작된다.

## 4. 서비스 로그
서비스로 실행한 프로그램의 로그를 확인하기 위해서는 jourctl 명령을 이용할 수 있다.

```shell
# -u 옵션으로 서비스 지정
$ journalctl -u zookeeper

# 추가 되는 로그를 확인. -f 옵션 이용
$ journalctl -u zookeeper -f
```

## [출처 및 참고]
* <https://wikidocs.net/134877>
