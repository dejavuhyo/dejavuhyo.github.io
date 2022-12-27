---
title: Java Apache Commons를 이용하여 null 체크
author: dejavuhyo
date: 2022-11-29 21:40:00 +0900
categories: [Language, Java]
tags: [java-null, java-null-check, null-check, apache-commons, java-apache-commons, java-null-체크, null-체크, 자바-널, 자바-널-체크, 자바-아파치]
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

```java
if (StringUtils.isNotEmpty(param)) {
}
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

```java
if (CollectionUtils.isEmpty(paramList)) {
}
```
