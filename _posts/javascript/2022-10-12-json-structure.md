---
title: JSON 구조
author: dejavuhyo
date: 2022-10-12 07:15:00 +0900
categories: [Language, JavaScript]
tags: [json-structure, json, json-object, json-array, json-data, json-구조, json-객체, json-배열, json-데이터]
---

## 1. JSON 구조
JSON은 자바스크립트의 객체 표기법으로부터 파생된 부분 집합이다. 따라서 JSON 데이터는 다음과 같은 자바스크립트 객체 표기법에 따른 구조로 구성된다.

* JSON 데이터는 이름과 값의 쌍으로 이루어진다.

* JSON 데이터는 쉼표(`,`)로 나열된다.

* 객체(object)는 중괄호(`{}`)로 둘러쌓아 표현한다.

* 배열(array)은 대괄호(`[]`)로 둘러쌓아 표현한다.

## 2. JSON 데이터
JSON 데이터는 이름과 값의 쌍으로 구성된다. 이러한 JSON 데이터는 데이터 이름, 콜론(`:`), 값의 순서로 구성된다.

* 문법

```text
"데이터이름": "값"
```

다음 예제는 데이터의 이름이 "name"이고, 값은 "식빵"이라는 문자열을 갖는 JSON 데이터의 예제이다.

```json
{
    "name": "식빵"
}
```

데이터의 이름도 문자열이므로, 항상 큰따옴표("")와 함께 입력해야 한다. 데이터의 값으로는 다음과 같은 타입이 올 수 있다.

* 숫자(number)

* 문자열(string)

* 불리언(boolean)

* 객체(object)

* 배열(array)

* NULL

## 3. JSON 객체
JSON 객체는 중괄호(`{}`)로 둘러쌓아 표현한다. 또한, JSON 객체는 쉼표(`,`)를 사용하여 여러 프로퍼티를 포함할 수 있다.

* 예제

```json
{
    "name": "식빵",
    "family": "웰시코기",
    "age": 1,
    "weight": 2.14
}
```

JSON 객체를 그림으로 나타내면 다음과 같다.

![json-object](/assets/img/2022-10-12-json-structure/json-object.png)

## 4. JSON 배열
JSON 배열은 대괄호(`[]`)로 둘러쌓아 표현한다. 또한, JSON 배열은 쉼표(`,`)를 사용하여 여러 JSON 데이터를 포함할 수 있다.

다음 예제는 배열의 이름이 "dog"이고, 3개의 JSON 객체를 요소로 가지는 JSON 배열의 예제이다.

```json
{
    "dog": [{
            "name": "식빵",
            "family": "웰시코기",
            "age": 1,
            "weight": 2.14
        },
        {
            "name": "콩콩",
            "family": "포메라니안",
            "age": 3,
            "weight": 2.5
        },
        {
            "name": "젤리",
            "family": "푸들",
            "age": 7,
            "weight": 3.1
        }
    ]
}
```

JSON 배열을 그림으로 나타내면 다음과 같다.

![json-array](/assets/img/2022-10-12-json-structure/json-array.png)

## [출처 및 참고]
* [https://www.tcpschool.com/json/json_basic_structure](https://www.tcpschool.com/json/json_basic_structure)
