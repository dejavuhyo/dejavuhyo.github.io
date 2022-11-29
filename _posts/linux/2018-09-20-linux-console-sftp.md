---
title: Linux 콘솔 sftp
author: dejavuhyo
date: 2018-09-20 11:18:00 +0900
categories: [DevOps, Linux]
tags: [linux-console-sftp, console-sftp, linux-sftp, 리눅스-콘솔-sftp, 콘솔-sftp, 리눅스-sftp]
---

## 1. 접속

```shell
$ sftp [ID]@[접속 도메인]
```

## 2. 명령어

* 폴더 이동

```shell
$ cd [폴더명]
```

* 폴더 조회

```shell
$ ls
```

* 파일 다운로드

```shell
$ get [파일명]
```

* 파일 업로드

```shell
$ put [파일명]
```

* 전체 파일 다운로드

```shell
$ mget *
```

* 전체 파일 업로드

```shell
$ mput *
```
