---
title: JavaScript 날짜, 시간, 요일 구하기
date: 2021-06-08 06:00:00 +0900
categories: [Language, JavaScript]
tags: [javascript-today, javascript-오늘, javascript-오늘-날짜]
---

## 1. 오늘 날짜

```javascript
let today = new Date();
```

## 2. 년도, 월, 일, 요일

```javascript
let year = today.getFullYear(); // 년도
let month = today.getMonth(); // 월
let date = today.getDate(); // 일
let day = today.getDay(); // 요일
```

## 3. 시간, 분, 초, 밀리초

```javascript
let today = new Date();

let hours = today.getHours(); // 시
let minutes = today.getMinutes(); // 분
let seconds = today.getSeconds(); // 초
let milliseconds = today.getMilliseconds(); // 밀리초
```

## 4. 요일

```javascript
var today = new Date();
var weekday = new Array(7);
weekday[0] = "일요일";
weekday[1] = "월요일";
weekday[2] = "화요일";
weekday[3] = "수요일";
weekday[4] = "목요일";
weekday[5] = "금요일";
weekday[6] = "토요일";

var day = weekday[today.getDay()];
```

## [출처 및 참고]
* <https://42kchoi.tistory.com/102>
