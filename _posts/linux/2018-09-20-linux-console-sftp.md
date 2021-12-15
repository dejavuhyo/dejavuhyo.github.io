---
title: 리눅스 콘솔 sftp
author: dejavuhyo
date: 2018-09-20 11:18:00 +0900
categories: [DevOps, Linux]
tags: [linux-console-sftp, console-sftp, linux-sftp, 리눅스-콘솔-sftp, 콘솔-sftp, 리눅스-sftp]
---

## 1. 접속

```bash
$ sftp [ID]@[접속 도메인]
```

## 2. 명령어

* 폴더 이동

```bash
$ cd [폴더명]
```

* 폴더 조회

```bash
$ ls
```

* 파일 다운로드

```bash
$ get [파일명]
```

* 파일 업로드

```bash
$ put [파일명]
```

* 전체 파일 다운로드

```bash
$ mget *
```

* 전체 파일 업로드

```bash
$ mput *
```
