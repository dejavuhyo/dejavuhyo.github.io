---
title: MyBatis selectKey 컬럼 여러개
date: 2021-08-11 06:00:00 +0900
categories: [DevOps, MyBatis]
tags: [mybatis-multiple-selectkey, multiple-selectkey, selectkey, mybatis-selectkey-컬럼-여러개, selectkey-컬럼-여러개]
---

## 1. selectKey 컬럼 여러 개 사용
Mybatis 3.2.6 버전부터는 selectKey에 여러 개 컬럼의 데이터를 조회할 수 있다. 여러 개의 컬럼을 가져오기 위해서는 keyColumn이라는 속성을 설정해주어야 한다.

데이터가 삽입된 이후에 특정한 조건에 맞는 컬럼들의 데이터를 조회한다. 주의할 점은 selectKey를 통해서 나오는 데이터의 row 수는 무조건 1개여야 한다.

## [출처 및 참고]
* <https://deeplify.dev/back-end/spring/select-key>
