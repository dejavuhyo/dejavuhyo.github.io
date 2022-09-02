---
title: 리눅스 groupadd
author: dejavuhyo
date: 2022-03-04 11:30:00 +0900
categories: [DevOps, Linux]
tags: [linux-groupadd, groupadd, 리눅스-groupadd]
---

## 1. groupadd
그룹을 추가 하는 명령어입니다. 그룹 정보는 `/etc/group`에서 확인할 수 있다.

## 2. 주요 옵션

| 옵션 | 내용 |
|:-----:|:-----:|
| -g | gid 할당 |

## 3. /etc/group
시스템의 그룹정보를 확인할 수 있다.

```shell
# 그룹명:비밀번호:그룹ID:그룹사용자
$ cat /etc/group
root:x:0:
daemon:x:1:
groupa:x:10001:usera,userb
```

## 4. 사용예제

### 1) 그룹 추가

```shell
# 그룹 groupa 추가
$ groupadd groupa
```

### 2) 사용자 그룹 확인
groups 명령으로 현재 사용자, 특정 사용자의의 그룹을 확인할 수 있다.

```shell
# 현재 사용자의 그룹 확인
$ groups
groupa

# 특정 사용자의 그룹 확인
$ groups usera
usera : groupa, groupb
```

## [출처 및 참고]
* <https://wikidocs.net/160884>
