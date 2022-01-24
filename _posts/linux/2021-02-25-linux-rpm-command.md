---
title: Linux rpm 명령어
author: dejavuhyo
date: 2021-02-25 06:10:00 +0900
categories: [DevOps, Linux]
tags: [rpm, linux-rpm, rpm-command, rpm-명령어, 리눅스-rpm, 리눅스-rpm-명령어]
---

## 1. 설치, 확인, 제거

### 1) 설치

```shell
[root@localhost ~]# rpm -ivh 패키지명
```

### 2) 확인

```shell
[root@localhost ~]# rpm -qa | grep 패키지명
```

### 3) 제거

```shell
[root@localhost ~]# rpm -ev 패키지명
```

### 4) 업그레이드
업그레이드 명령어이지만 기존에 설치된 것이 없을 경우 설치된다.

```shell
[root@localhost ~]# rpm -Uvh 패키지명
```

## 2. 기타

### 1) 파일이 속한 패키지 찾기

```shell
[root@localhost ~]# rpm -qf 파일
```

### 2) rpm 패키지 정보 보기(info)

```shell
[root@localhost ~]# rpm -qi 설치된 패키지명
```

```shell
[root@localhost ~]# rpm -qip 파일명.rpm
```

### 3) rpm 내부 파일목록 보기(list)

```shell
[root@localhost ~]# rpm -ql 설치된패 키지명
```

```shell
[root@localhost ~]# rpm -qlp 파일명.rpm
```

### 4) rpm 내부 설정파일 확인

```shell
[root@localhost ~]# rpm -qc 설치된 패키지명
```

```shell
[root@localhost ~]# rpm -qcp 파일명.rpm
```

### 5) rpm 내부 문서파일 확인

```shell
[root@localhost ~]# rpm -qd 설치된 패키지명
```

```shell
[root@localhost ~]# rpm -qdp 파일명.rpm
```

### 6) rpm 내부 스크립트 확인

```shell
[root@localhost ~]# rpm -q --scripts 설치된 패키지명
```

```shell
[root@localhost ~]# rpm -qp --scripts 파일명.rpm
```

## [출처 및 참고]
* <https://zetawiki.com/wiki/%EB%A6%AC%EB%88%85%EC%8A%A4_rpm_%EB%AA%85%EB%A0%B9%EC%96%B4>
