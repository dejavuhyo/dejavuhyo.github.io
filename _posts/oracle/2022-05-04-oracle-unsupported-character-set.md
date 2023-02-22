---
title: Oracle 지원되지 않는 문자 집합(클래스 경로에 orai18n.jar 추가) 에러
author: dejavuhyo
date: 2022-05-04 14:10:00 +0900
categories: [Database, Oracle]
tags: [oracle-orai18n, orai18n, unsupported-character-set, 지원되지-않는-문자-집합]
---

## 1. 오류
DB에 세팅된 인코딩 방식이 ojdbc8.jar에서 지원하지 않는 인코딩 방식이라 에러난다.

![orai18n-error](/assets/img/2022-05-04-oracle-unsupported-character-set/orai18n-error.png)

## 2. 해결 방법
직접 [orai18n.jar](https://www.oracle.com/database/technologies/appdev/jdbc-downloads.html) 파일을 다운받아서 추가하거나, dependency를 추가한다.

```xml
<dependency>
    <groupId>com.oracle.database.nls</groupId>
    <artifactId>orai18n</artifactId>
    <version>21.5.0.0</version>
</dependency>
```

## [출처 및 참고]
* [https://sillutt.tistory.com/entry/Oracle-%EC%A7%80%EC%9B%90%EB%90%98%EC%A7%80-%EC%95%8A%EB%8A%94-%EB%AC%B8%EC%9E%90-%EC%A7%91%ED%95%A9%ED%81%B4%EB%9E%98%EC%8A%A4-%EA%B2%BD%EB%A1%9C%EC%97%90-orai18njar-%EC%B6%94%EA%B0%80-KO16MSWIN949](https://sillutt.tistory.com/entry/Oracle-%EC%A7%80%EC%9B%90%EB%90%98%EC%A7%80-%EC%95%8A%EB%8A%94-%EB%AC%B8%EC%9E%90-%EC%A7%91%ED%95%A9%ED%81%B4%EB%9E%98%EC%8A%A4-%EA%B2%BD%EB%A1%9C%EC%97%90-orai18njar-%EC%B6%94%EA%B0%80-KO16MSWIN949)
