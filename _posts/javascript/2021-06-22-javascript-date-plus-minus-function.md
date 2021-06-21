---
title: JavaScript 날짜 더하기 빼기 함수
date: 2021-06-22 06:00:00 +0900
categories: [Language, JavaScript]
tags: [javascript-date-plus-minus-function, date-plus-minus-function, javascript-date-plus-minus, javascript-날짜-더하기-빼기-함수, javascript-날짜-더하기-빼기, 날짜-더하기-빼기-함수, 날짜-더하기-빼기]
---

## 1. 함수

```javascript
function datePlusMinus(sDate, nNum, type) {
    var yy = parseInt(sDate.substr(0, 4), 10);
    var mm = parseInt(sDate.substr(5, 2), 10);
    var dd = parseInt(sDate.substr(8), 10);
    
    var dt;
    if (type == "d") {
        dt = new Date(yy, mm - 1, dd + nNum);
    } else if (type == "m") {
        dt = new Date(yy, mm - 1, dd + (nNum * 31));
    } else if (type == "y") {
        dt = new Date(yy + nNum, mm - 1, dd);
    }
    
    yy = dt.getFullYear();
    mm = dt.getMonth() + 1;
    mm = (mm < 10) ? '0' + mm : mm;
    dd = dt.getDate();
    dd = (dd < 10) ? '0' + dd : dd;
    
    return '' + yy + '-' + mm + '-' + dd;
}
```

## 2. 사용

```javascript
// 일 빼기
datePlusMinus('2021-06-25', -7, 'd');
// 월 더하기
datePlusMinus('2021-06-25', +6, 'm');
// 년도 빼기
datePlusMinus('2021-06-25', -5, 'y');
```

## [출처 및 참고]
* <https://tonhnegod.tistory.com/10>
