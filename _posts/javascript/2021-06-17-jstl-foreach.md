---
title: JSTL 반복문 사용법
date: 2021-06-17 06:00:00 +0900
categories: [Language, JavaScript]
tags: [jstl-foreach, foreach, jstl-반복문, 반복문]
---

## 1. 선언
Maven dependency 추가 및 JSP 페이지 상단에 JSTL core 선언

* Maven

```xml
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>jstl</artifactId>
    <version>1.2</version>
</dependency>
```

* JSP

```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
```

## 2. forEach

### 1) 속성

* __var:__ 사용할 변수명

* __items:__ Collection 객체 (List, Map)

* __begin:__ 반복 시작 index, 정의되지 않으면 0

* __end:__ 반복 종료

* __step:__ 반복할 때마다 건너뛸 index 개수

* __varStatus:__ 반복 상태를 알 수 있는 변수

### 2) varStatus 활용

```jsp
<c:forEach var="item" items="${resultList}" varStatus="status">
    ${status.current}
    ${status.index}
    ${status.count}
    ${status.first}
    ${status.last}
    ${status.begin}
    ${status.end}
    ${status.step}
</c:forEach>
```

## [출처 및 참고]
* <https://thefif19wlsvy.tistory.com/61>
