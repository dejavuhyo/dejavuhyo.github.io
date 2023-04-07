---
title: MyBatis Apache Commons를 이용하여 null 체크
author: dejavuhyo
date: 2022-11-28 21:00:00 +0900
categories: [Framework, MyBatis]
tags: [mybatis-null, mybatis-null-check, null-check, apache-commons, mybatis-apache-commons, mybatis-null-체크, null-체크]
---

## 1. String null 체크

### 1) dependency 추가

```xml
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-lang3</artifactId>
    <version>3.12.0</version>
</dependency>
```

### 2) 활용

```xml
<if test="@org.apache.commons.lang3.StringUtils@isNotBlank(param)">
</if>
```

## 2. List null 체크

### 1) dependency 추가

```xml
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-collections4</artifactId>
    <version>4.4</version>
</dependency>
```

### 2) 활용

```xml
<if test="@org.apache.commons.collections4.CollectionUtils@isNotEmpty(paramList)">
</if>
```

## [출처 및 참고]
* [https://bol-bbang.tistory.com/13](https://bol-bbang.tistory.com/13)
