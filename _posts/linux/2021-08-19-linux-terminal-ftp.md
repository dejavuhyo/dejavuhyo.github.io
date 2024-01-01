---
title: Linux 터미널 FTP
author: dejavuhyo
date: 2021-08-19 06:00:00 +0900
categories: [DevOps, Linux]
tags: [linux-terminal-ftp, linux-ftp, terminal-ftp, 리눅스-터미널-ftp, 터미널-ftp, 리눅스-ftp]
---

## 1. FTP 접속방법

```shell
[root@localhost ~]# ftp IP PORT
```

예시

```shell
[root@localhost ~]# ftp 192.168.0.1 21
```

## 2. FTP 전용명령어
FTP 모드에서 사용할 수 있는 전용 명령어는 일반 유닉스 명령어와 별도로 존재한다. 즉, FTP로 접속했을 때 사용할 수 있는 명령어를 확인해 보려면 `ftp>?`을 입력해 보면 FTP 명령어 리스트가 디스플레이된다.

또한 `ftp>?` 명령어 형식으로 해당 명령어의 도움말을 볼 수 있다. 다음은 이들 FTP 명령어 중 자주 사용하는 명령어에 대한 설명이다. FTP 명령어는 시스템에 따라 조금씩 차이는 있다.

* ascii: 전송모드를 ASCII 모드로 설정한다. (ascii또는 as)

* binary: 전송모드를 BINARY 모드로 설정한다. (binary또는 bi)

* bell: 명령어 완료시에 벨소리를 나게한다. (bell)

* bye: ftp 접속을 종료하고 빠져나간다. (bye)

* cd: remote 시스템의 디렉토리를 변경한다. (cd 디렉토리명)

* cdup: remote 시스템에서 한단계 상위 디렉토리로 이동한다. (cdup)

* chmod: remote 시스템의 파일 퍼미션을 변경한다. (chmod 755 index.html)

* close: ftp 접속을 종료한다. (close)

* delete: remote 시스템의 파일을 삭제한다. (delete index.old)

* dir: remote 시스템의 디렉토리 내용을 디스플레이한다. (dir)

* disconnect: ftp 접속을 종료한다. (disconnect)

* exit: ftp 접속을 종료하고 빠져나간다. (exit)

* get: 지정된 파일 하나를 가져온다. (get index.html)

* hash: 파일전송 도중에 '#'표시를 하여 전송 중임을 나타낸다. (hash)

* help: ftp 명령어 도움말을 볼 수 있다. (help 또는 help 명령어)

* lcd: local 시스템의 디렉토리를 변경한다. (lcd 디렉토리명)

* ls: remote 시스템의 디렉토리 내용을 디스플레이한다. (ls 또는 ls -l)

* mdelete: 여러개의 파일을 한꺼번에 지울 때 사용한다. (mdelete *.old)

* mget: 여러개의 파일을 한꺼번에 가져오려 할 때 사용한다. (mget *.gz)

* mput: 한꺼번에 여러개의 파일을 remote 시스템에 올린다. (mput *.html)

* open: ftp 접속을 시도한다. (open 168.126.72.51 또는 open ftp.kornet.net)

* prompt: 파일 전송시에 확인 과정을 거친다. on/off 토글 (prompt)

* put: 하나의 파일을 remote 시스템에 올린다. (put index.html)

* pwd: remote 시스템의 현재 작업 디렉토리를 표시한다. (pwd)

* quit: ftp 접속을 종료하고 빠져나간다. (quit)

* rstatus: remote 시스템의 상황(version, 어디서, 접속ID 등)을 표시한다. (rstatus)

* rename: remote 시스템의 파일명을 바꾼다. (remote 현재파일명 바꿀파일명)

* rmdir: remote 시스템의 디렉토리을 삭제한다. (rmdir 디렉토리명)

* size: remote 시스템에 있는 파일의 크기를 byte 단위로 표시한다. (size index.html)

* status: 현재 연결된 ftp 세션가지 모드에 대한 설정을 보여준다. (status)

* type: 전송 모드를 설정한다. (type 또는 type ascii 또는 type binary)

## [출처 및 참고]
* [https://www.linux.co.kr/home/lecture/?leccode=300](https://www.linux.co.kr/home/lecture/?leccode=300)
