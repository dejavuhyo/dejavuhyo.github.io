---
title: Linux nslookup
author: dejavuhyo
date: 2022-06-24 13:00:00 +0900
categories: [DevOps, Linux]
tags: [linux-nslookup, nslookup, 리눅스-nslookup, 리눅스]
---

## 1. nslookup
도메인 서버에 호스트명을 검색하기 위한 명령어 이다. Name Server LOOKUP의 약어이다.

## 2. 사용예제

```shell
$ nslookup www.naver.com
Server:     10.20.30.60
Address:    10.20.30.60#53

Non-authoritative answer:
www.naver.com   canonical name = www.naver.com.nheos.com.
Name:   www.naver.com.nheos.com
Address: 125.209.222.142
Name:   www.naver.com.nheos.com
Address: 210.89.160.88
```

## [출처 및 참고]
* [https://wikidocs.net/92662](https://wikidocs.net/92662)
