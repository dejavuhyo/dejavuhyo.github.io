---
title: Linux useradd
author: dejavuhyo
date: 2022-03-07 12:00:00 +0900
categories: [DevOps, Linux]
tags: [linux-useradd, useradd, 리눅스-useradd]
---

## 1. linux-useradd
사용자 추가를 하는 명령어이다. 사용자 정보는 `/etc/passwd`에서 확인할 수 있다.

> 사용자는 특정 그룹에 반드시 속해야 한다.

## 2. 주요 옵션

| 옵션 | 내용 |
|:-----:|:-----:|
| -g | 사용자 그룹을 추가 |
| -d | 사용자 디렉토리 지정 |
| -s | 쉘 지정 |
| -p | 패스워드 지정 |

## 3. /etc/passwd
`/sbin/nologin`으로 기본쉘이 설정된 계정은 로그인은 안되지만, su 명령으로 계정을 변경하여 명령을 실행할 수 있다.

```shell
# 사용자이름:비밀번호:사용자ID:그룹ID:사용자설명:홈디렉토리:기본쉘
$ cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
```

## 4. 사용예제

### 1) 사용자 추가

```shell
# 사용자 aa를 추가하면서 그룹은 gg로 설정
$ useradd aa -g gg
```

### 2) 사용자를 그룹에 추가

```shell
# user1을 grp1그룹에 추가 
$ usermod -G grp1 -a user1
```

### 3) 사용자를 여러 개의 그룹으로 추가(멀티 그룹)

```shell
# user1을 grp1, grp2에 등록 
$ usermod -a -G grp1,grp2 user1
```

## [출처 및 참고]
* [https://wikidocs.net/104098](https://wikidocs.net/104098)
