---
title: JavaScript ==와 ===의 차이
author: dejavuhyo
date: 2025-05-08 09:30:00 +0900
categories: [Language, JavaScript]
tags: [javascript-operator, equal-operator, strict-equal-operator, 자바스크립트-연산자, 동등-연산자, 일치-연산자]
---

## 1. 동등 연산자 (==)
`==` 연산자는 동등 비교 연산자로, 비교하려는 두 값이 서로 동등한지를 확인한다. 이 연산자는 값을 비교할 때 데이터 형식이 다른 경우에도 암묵적 형변환을 수행하여 값이 동일한지를 확인한다.

또한 타입이 다른 두 값의 비교를 할 때 타입 변환이 일어난다. 타입 변환 규칙에 따라 값이 같은지 비교한다.

```text
100 == 100        // true
100 == '100'      // true
1 == true         // true
true == 'true'    // true
null == undefined // true
```

## 2. 일치 연산자 (===)

`===` 연산자는 일치 비교 연산자로, 비교하려는 두 값이 정확하게 동일한지를 확인한다. 이 연산자는 값의 동등성 뿐만 아니라 데이터 형식까지 비교한다. 따라서, 비교하려는 값의 데이터 형식이 다른 경우에는 일치하지 않다고 판단한다.

피연산자 타입이 서로 다르면 자동으로 타입을 반환해 주는 `==` 연산자와 달리 `===` 연산자는 두 피연산자 값의 타입이 서로 다르더라도 변환하지 않고 있는 그대로의 값을 비교하는 방식으로 엄격하게 값을 비교한다.

또한 `NaN` 값은 자기 자신을 포함하여 어떠한 값과도 일치하지 않는다. 즉, `NaN` 값과 비교하게 되면 항상 false 값이 반환된다.

```text
100 === 100        // true
100 === '100'      // false
1 === true         // false
true === 'true'    // false
null === undefined // false
NaN === NaN        // false
```

## [출처 및 참고]
* [https://tmdrnr96.tistory.com/27](https://tmdrnr96.tistory.com/27)
* [https://velog.io/@dev_seongjoo/@dev_seongjoo/와-의-차이](https://velog.io/@dev_seongjoo/@dev_seongjoo/와-의-차이)
