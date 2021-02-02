---
title: List를 Json으로 변환
author: dejavuhyo
date: 2020-02-19 17:00:00 +0900
categories: [Language, Java]
tags: [list-to-json, list-to-json-convert, 리스트-제이슨-변환]
---

## 1. Gson Download
Gson is a Java library that can be used to convert Java Objects into their JSON representation. It can also be used to convert a JSON string to an equivalent Java object. Gson can work with arbitrary Java objects including pre-existing objects that you do not have source-code of.

* Gradle

```gradle
dependencies {
    implementation 'com.google.code.gson:gson:2.8.5'
}
```

* Maven

```xml
<dependency>
    <groupId>com.google.code.gson</groupId>
    <artifactId>gson</artifactId>
    <version>2.8.5</version>
</dependency>
```

## 2. Sample Code

```java
public static void main(String[] args) {

    List<String> foo = new ArrayList<String>();
    foo.add("A");
    foo.add("B");
    foo.add("C");

    String json = new Gson().toJson(foo);
    System.out.println(json);
}
```

## 출처 및 참고
* <https://github.com/google/gson>
