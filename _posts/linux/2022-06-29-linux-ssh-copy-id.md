---
title: Linux ssh-copy-id
author: dejavuhyo
date: 2022-06-29 14:30:00 +0900
categories: [DevOps, Linux]
tags: [linux-ssh-copy-id, ssh-copy-id, 리눅스-ssh-copy-id, 리눅스]
---

## 1. ssh-copy-id
현재 호스트의 공개키를 원격 호스트의 authorized_keys에 등록한다. 공개키를 등록하면 암호나 인증키를 이용하지 않아도 접속할 수 있다.

## 2. 사용예제

```shell
# 127.0.0.1 호스테에 22번 포트로 접속
$ ssh-copy-id user@127.0.0.1

# host에 user 명으로 1000번 포트로 접속
$ ssh-copy-id -p 1000 user@hostname
```

## [출처 및 참고]
* [https://wikidocs.net/122417](https://wikidocs.net/122417)
