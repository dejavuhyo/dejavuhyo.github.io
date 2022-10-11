---
title: JSON 문법
author: dejavuhyo
date: 2022-10-11 09:00:00 +0900
categories: [Language, JavaScript]
tags: [json-syntax, json, json-literal, json-object, json-주석, json-문법]
---

## 1. JSON 문법
JSON은 자바스크립트의 객체 표기법에서 리터럴(literal)과 프로퍼티(property)를 표현하는 방법만 가져와서 사용한다. 따라서 JSON 데이터는 모양과 규칙이 매우 단순하다.

그로 인해 브라우저 영역에서도 쉽고 빠르게 그 의미를 해석할 수 있으며, 다른 프로그래밍 언어에서도 구현하기 쉽다.

## 2. 리터럴(literal)
리터럴(literal)은 변수와 다르게 해석되는 값 그 자체를 의미한다.

다음 예제에서 등장하는 값은 모두 리터럴이다.

* 예제

```text
12     // 숫자 리터럴
"JSON" // 문자열 리터럴
true   // 불리언 리터럴
```

> 변수(variable)란 데이터(data)를 저장할 수 있는 메모리 공간을 의미하며, 그 값이 변경될 수 있다.

## 3. 객체(object)
객체(object)란 실생활에서 우리가 인식할 수 있는 사물로 이해할 수 있다. JSON에서 객체란 이름(name)과 값(value)으로 구성된 프로퍼티(property)의 정렬되지 않은 집합이다.

다음 예제는 이름과 값으로 이루어진 네 쌍의 프로퍼티를 가지는 "강아지" 객체를 나타내는 예제이다.

* 예제

```json
{
    "name": "식빵",
    "family": "웰시코기",
    "age": 1,
    "weight": 2.14
}
```

## 4. JSON 주석
JSON 표준의 창시자인 더글라스 크록포드는 JSON에는 주석이 들어가지 않는 것이 바르다고 규정하고 있다. 그것은 서로 다른 시스템 간의 연동과 호환성을 위한 조치였다.

반드시 주석을 사용해야 한다면, 주석이 포함된 JSON 데이터를 파싱하기 전에 주석만을 먼저 제거해야 한다. 하지만 되도록 JSON에는 주석을 사용하지 않는 것이 좋다.

## [출처 및 참고]
* <http://www.tcpschool.com/json/json_basic_syntax>
