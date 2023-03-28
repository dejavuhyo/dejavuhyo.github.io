---
title: StringUtils.isNotEmpty와 StringUtils.isNotBlank 차이
author: dejavuhyo
date: 2022-12-01 21:15:00 +0900
categories: [Language, Java]
tags: [stringutils-isnotempty, stringutils-isnotblank, stringutils, isnotempty, isnotblank, apache-commons, string-null, null, whitespace-string, apache-commons-lang, commons-lang3]
---

## 1. StringUtils.isNotEmpty()
`StringUtils.isNotEmpty()`는 String이 비어 있지 않은지 혹은 String이 길이가 0이고 null이 아닌지 찾는데 사용된다.

* Example

```java
StringUtils.isNotEmpty(null)      = false
StringUtils.isNotEmpty("")        = false
StringUtils.isNotEmpty(" ")       = true
StringUtils.isNotEmpty("  str  ") = true
StringUtils.isNotEmpty("str")     = true
```

## 2. StringUtils.isNotEmpty()
`StringUtils.isNotBlank()`는 한 단계 더 나아간다. 문자열이 비어 있지 않고 null이 아닌지 확인할 뿐만 아니라, 공백 문자열이 아닌지도 확인한다.

* Example

```java
StringUtils.isNotBlank(null)      = false
StringUtils.isNotBlank("")        = false
StringUtils.isNotBlank(" ")       = false
StringUtils.isNotBlank("str")     = true
StringUtils.isNotBlank("  str  ") = true
```

## [출처 및 참고]
* [https://javakeypoint.wordpress.com/2018/01/18/stringutils-isnotempty-vs-stringutils-isnotblank/](https://javakeypoint.wordpress.com/2018/01/18/stringutils-isnotempty-vs-stringutils-isnotblank/)
* [https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/String.html#isBlank()](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/String.html#isBlank())
* [https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/String.html#isEmpty()](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/String.html#isEmpty())
