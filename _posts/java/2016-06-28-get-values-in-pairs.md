---
title: 값을 쌍으로 받기
author: dejavuhyo
date: 2016-06-28 15:03:00 +0900
categories: [Language, Java]
tags: [get-values-pairs, values-pairs, get-parameter-values, 값을-쌍으로-받기]
---

## 1. value를 넘길때
`<c:foreach>`로 뿌려진 값을 id, value를 넘길때 타입 name 설정한다.

* a.jsp

```jsp
<form name="form" id="form" method="post">
<c:forEach var="result" items="${resultList}" varStatus="status">
    <input type="text" name="id" value="${result.id}" />
    <input type="text" name="value" value="${result.value}" />
</c:forEach>
</form>
```

## 2. Controller에서 받을때
배열로 받기, for문에서 증가 연산자 같이 사용한다.

* AController.java

```java
String[] id = request.getParameterValues("id");
String[] value = request.getParameterValues("value");

for (int i = 0; i < id.length(); i++) {
    System.out.println("id = " + id[i] + ", " + "value = " + value[i]);
}
```

> request.getParameterValues방법과 vo를 배열로 선언하여 get하는 방법이 있다.
