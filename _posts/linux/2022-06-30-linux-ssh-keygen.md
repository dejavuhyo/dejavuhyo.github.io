---
title: 리눅스 ssh-keygen
author: dejavuhyo
date: 2022-06-30 21:50:00 +0900
categories: [DevOps, Linux]
tags: [linux-ssh-keygen, ssh-keygen, 리눅스-ssh-keygen, 리눅스]
---

## 1. ssh-keygen
ssh 연결에 사용할 키를 생성한다. ssh 연결에 사용하는 키를 생성하여 승인된 키로 등록하고, 노드간 연결에 암호를 물어보지 않고 연결할 수 있도록 한다.

## 2. 주요 옵션

| 옵션 | 설명 |
|:-----:|:-----:|
| -t | 암호화 방식을 선택 |

## 3. 사용예제

### 1) RSA 키 생성

* id_rsa: 개인키

* id_rsa.pub: 공개키

```shell
# rsa 형식의 키 생성
$ ssh-keygen -t rsa

# 사용자 홈 디렉토리 아래 .ssh 폴더에 id_rsa, id_rsa.pub 파일이 생성됨
$ ls -alh ~/.ssh
total 72
drwx------   8 kakao_ent  staff    256 10  7 11:01 .
drwxr-xr-x+ 56 kakao_ent  staff   1792 12 28 12:26 ..
-rw-r--r--   1 kakao_ent  staff    110  8 20 16:12 config
-rw-------   1 kakao_ent  staff   2635  9  1 18:41 id_rsa
-rw-r--r--   1 kakao_ent  staff    594  9  1 18:41 id_rsa.pub
```

### 2) 공개키를 다른 노드에 등록
접근하고자 하는 노드의 홈디렉토리 아래 .ssh 폴더에 authorized_keys 파일에 공개키를 등록한다. `id_rsa.pub` 파일을 `cat`으로 읽어서 등록 한다. authorized_keys 파일이 없으면 생성해야 한다.

```shell
$ ll
total 16
-rw-r--r-- 1 deploy deploy 1767 Dec 18 10:41 authorized_keys
```

## [출처 및 참고]
* <https://wikidocs.net/106513>
