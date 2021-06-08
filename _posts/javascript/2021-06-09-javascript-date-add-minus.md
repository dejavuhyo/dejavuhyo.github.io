---
title: JavaScript 날짜 더하기, 빼기
date: 2021-06-09 06:00:00 +0900
categories: [Language, JavaScript]
tags: [javascript-date-add-minus, javascript-date-add, javascript-date-minus, 자바스크립트-날짜-더하기-빼기, 자바스크립트-날짜-더하기, 자바스크립트-날짜-빼기]
---

## 1. 함수

```javascript
function dateAddMinus(sDate, nNum, type) {
    var yy = parseInt(sDate.substr(0, 4), 10);
    var mm = parseInt(sDate.substr(5, 2), 10);
    var dd = parseInt(sDate.substr(8), 10);

    if (type == "d") {
        d = new Date(yy, mm - 1, dd + nNum);
    } else if (type == "m") {
        d = new Date(yy, mm - 1, dd + (nNum * 31));
    } else if (type == "y") {
        d = new Date(yy + nNum, mm - 1, dd);
    }

    yy = d.getFullYear();
    mm = d.getMonth() + 1;
    mm = (mm < 10) ? '0' + mm : mm;
    dd = d.getDate();
    dd = (dd < 10) ? '0' + dd : dd;

    return '' + yy + '-' + mm + '-' + dd;
}
```

## 2. 활용

### 1) 연도

```javascript
dateAddMinus('2021-06-08', +5, 'd');
dateAddMinus('2021-06-08', -5, 'd');
```

### 2) 월

```javascript
dateAddMinus('2021-06-08', +5, 'm');
dateAddMinus('2021-06-08', -5, 'm');
```

### 3) 일

```javascript
dateAddMinus('2021-06-08', +5, 'm');
dateAddMinus('2021-06-08', -5, 'm');
```

## [출처 및 참고]
* <https://tonhnegod.tistory.com/10>
