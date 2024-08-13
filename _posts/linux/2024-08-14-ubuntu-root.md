---
title: Ubuntu root 계정
author: dejavuhyo
date: 2024-08-14 08:33:00 +0900
categories: [DevOps, Linux]
tags: [ubuntu-root, ubuntu-su-root,ubuntu-user, 우분투-루트, 우분투-사용자]
---

## 1. root 계정
우분투는 보안문제로 설치시 root 계정이 비활성화 되어 있고 `sudo` 명령을 실행할 수 있는 사용자 계정으로 설치가 된다.

관리자로 로그인을 하기 위해서는 root 계정을 활성화 해야 한다.

```shell
$ sudo passwd root
```

root 계정에 패스워드(password)를 설정하면 관리자 계정이 활성화 된다.

## [출처 및 참고]
* [https://duvallee.tistory.com/12](https://duvallee.tistory.com/12)
