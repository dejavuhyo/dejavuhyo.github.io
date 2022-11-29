---
title: Linux date
author: dejavuhyo
date: 2022-05-30 18:30:00 +0900
categories: [DevOps, Linux]
tags: [linux-date, date, 리눅스-date, 리눅스-날짜]
---

## 1. date
일자, 시간을 처리한다.

## 2. 주요 옵션

| 옵션 | 설명 |
|:-----:|:-----:|
| `-d` | 원하는 일자를 지정 |
| `+` | 출력 포맷 지정 |

## 3. 사용예제

### 1) 일자 출력 포맷 지정

```shell
# 년월일 형태
$ date +%Y%m%d
20190212

# %를 지원하지 않는 환경(ex: crontab)에서는 특수기호로 입력
$ date +\%Y\%m\%d
20190212

# 년월일 시간 출력
$ date +"%Y%m%d %H:%M:%S"
20190212 08:46:53

# 주차 계산
$ date +%W
06
```

### 2) 일자 변경

```shell
# 1일 전
$ date +%Y-%m-%d -d '1 days ago'
2019-02-11

# 1일 후
$ date +%Y-%m-%d -d '+1days'
2019-02-13

# 20190101 일자 지정후 1일 추가
$ date +%Y-%m-%d -d '20190101 +1days'
2019-01-02
```

## [출처 및 참고]
* <https://wikidocs.net/30100>
