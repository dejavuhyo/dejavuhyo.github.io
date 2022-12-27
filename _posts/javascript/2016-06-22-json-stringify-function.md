---
title: JSON.stringify 함수
author: dejavuhyo
date: 2016-06-22 17:55:00 +0900
categories: [Language, JavaScript]
tags: [json-stringify-function, json-stringify, stringify-function]
---

## 1. JSON.stringify 함수
JavaScript 값을 JSON(JavaScript Object Notation) 문자열로 변환한다.

## 2. 구문
JSON.stringify(value [, replacer] [, space])

## 3. 예제 1
이 예제에서는 JSON.stringify를 사용하여 contact 개체를 JSON 텍스트로 변환한다. memberfilter 배열이 정의되어 surname 및 phone 멤버만 변환된다. firstname 멤버는 생략된다.

```javascript
var contact = new Object();
contact.firstname = "Jesper";
contact.surname = "Aaberg";
contact.phone = ["555-0100", "555-0120"];

var memberfilter = new Array();
memberfilter[0] = "surname";
memberfilter[1] = "phone";
var jsonText = JSON.stringify(contact, memberfilter, "\t");
document.write(jsonText);

// Output: 
// { "surname": "Aaberg", "phone": [ "555-0100", "555-0120" ] }
```

## 4. 예제 2
이 예제에서는 배열이 있는 JSON.stringify를 사용한다. replaceToUpper 함수는 배열의 모든 문자열을 대문자로 변환한다.

```javascript
var continents = new Array();
continents[0] = "Europe";
continents[1] = "Asia";
continents[2] = "Australia";
continents[3] = "Antarctica";
continents[4] = "North America";
continents[5] = "South America";
continents[6] = "Africa";

var jsonText = JSON.stringify(continents, replaceToUpper);

function replaceToUpper(key, value) {
    return value.toString().toUpperCase();
}

//Output:
// "EUROPE,ASIA,AUSTRALIA,ANTARCTICA,NORTH AMERICA,SOUTH AMERICA,AFRICA"
```

## 5. 예제 3
이 예제에서는 toJSON 메서드를 사용하여 문자열 값을 대문자로 변환한다.

```javascript
var contact = new Object(); 
contact.firstname = "Jesper";
contact.surname = "Aaberg";
contact.phone = ["555-0100", "555-0120"];

contact.toJSON = function(key) {
    var replacement = new Object();
    for (var val in this) {
        if (typeof (this[val]) === 'string')
            replacement[val] = this[val].toUpperCase();
        else
            replacement[val] = this[val]
    }
    return replacement;
};

var jsonText = JSON.stringify(contact);
document.write(jsonText);

// Output:
/*
{"firstname":"JESPER","surname":"AABERG","phone":["555-0100","555-0120"]}
'{"firstname":"JESPER","surname":"AABERG","phone":["555-0100","555-0120"]}'
*/
```

## [출처 및 참고]
* <https://msdn.microsoft.com/ko-kr/library/cc836459(v=vs.94).aspx>
