---
title: JavaScript URL Parameter 값 가져오기
author: dejavuhyo
date: 2022-11-15 22:30:00 +0900
categories: [Language, JavaScript]
tags: [javascript-url-value, url-parameter-value, parameter-value, javascript-parameter-value, location-href, location-search, 자바스크립트-파라미터, 자바스크립트-url-값, 파라미터-값]
---

## 1. 파라미터 값
URL에서 파라미터값만 JavaScript로 가져오는 경우, `location.href`를 이용하여 현재 페이지 전체 URL을 가져올 수 있다. 또한 `location.search`를 이용하여 현재 페이지 URL의 Parameter를 얻을 수 있다.

## 2. 모든 값 가져오기
new URLSearchParams 함수를 사용하여 `location.search`에 있는 key, value 형식의 파라미터를 가져올 수 있다.

```javascript
const searchParams = new URLSearchParams(location.search);
for (const param of searchParams) {
    console.log(param);
}
```

## 3. 특정 값만 가져오기
전체 URL은 `location.href`을 사용하여 구할 수 있다. 현재 주소를 URL 형식으로 바꿔주고 get 메소드를 사용하여 특정 파라미터값을 가져올 수 있다.

```javascript
const urlParams = new URL(location.href).searchParams;
const name = urlParams.get('name');
console.log(name)
```

## [출처 및 참고]
* [https://gurtn.tistory.com/126](https://gurtn.tistory.com/126)
