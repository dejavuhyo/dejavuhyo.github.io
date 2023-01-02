---
title: jQuery 요소 탐색
author: dejavuhyo
date: 2021-06-16 06:00:00 +0900
categories: [Language, JavaScript]
tags: [jquery-explore-element, explore-element, jquery-요소-탐색, jquery-탐색, jquery-노드-탐색]
---

## 1. 셀렉터

```javascript
$("*") // 모든 엘리먼트 선택
$("div") // Tag 이름으로 노드 찾기
$("#idName") // id 이름으로 노드 찾기
$(".className") // class 이름으로 노드 찾기
$("ul.className") // ul 엘리먼트 중 class 이름이 className 인 엘리먼트 선택
$("ul, div") // ul 및 div 태그 선택
$("div img") // div 의 자손노드 중 Tag이름이 img인 모든 엘리먼트 선택
$("ul .className") // ul의 자손노드 중 class 이름이 className 인 엘리먼트 선택
$("ul>li") // ul Tag 의 바로 아래 자식노드 중 li Tag 인 엘리먼트 선택
$("prev+next")
$("prev~siblings")
```

## 2. 필터

```javascript
:not(selector) // 셀렉터와 일치하지 않는 요소들 리턴
:first // 선택된 요소 중 첫 번째 요소
:last // 선택된 요소 중 마지막 요소
:even // 선택된 요소 중 짝수 인덱스 요소들
:odd // 선택된 요소 중 홀수 인덱스 요소들
:eq(index) // 선택된 요소 중 지정된 index 번호를 가진 요소
:gt(index) // 선택된 요소 중 지정된 index 번호보다 큰 인덱스 요소들
:lt(index) // 선택된 요소 중 지정된 index 번호보다 작은 인덱스 요소들
:header // <h1> ~ <h6> 요소들
:animated // 애니메이션이 적용된 모든 요소들
:focus // 현재 포커스를 가지고 있는 요소
:contains("text") // "text"를 가지고 있는 요소들
:empty // 자식 요소가 없는 모든 요소들
:parent // 자식 요소를 가지고 있는 모든 요소들
:has(selector) // selector에 부합하는 요소를 하나 이상 가지고 있는 요소들
:hidden // 화면에서 숨겨진 모든 요소들
:visible // 공간을 차지하고 있는 모든 요소들
:nth-child(expr) // expr 순번의 자식 요소
:first-child // 첫번째 자식 요소
:last-child // 마지막 자식 요소
:only-child // 요소의 자식 요소가 하나뿐인 요소

// 예시
$('div:not("#summary")') // #summary를 제외한 나머지 div 태그들을 선택
$("li").eq(3) // 찾은 노드 중 4 번째 노드에 접근하기
$("div:has(p)") // <p> 요소를 가진 모든 <div> 요소들
$("ul li:nth-child(2)") // 두 번째 <li> 요소
```

## 3. 속성옵션 필터

```javascript
$("[attr]") // attr 속성을 가진 모든 요소들
$("el[attr]") // 속성 attr 를 포함한 모든 el 노드 찾기
$("el[attr=val]") // 속성 attr 의 값이 val 인 모든 el 노드 찾기
$("el[attr!=val]") // 속성 attr 의 값이 val이 아닌 모든 el 노드 찾기
$("el[attr^=val]") // 속성 attr 의 값이 val 로 시작하는 모든 el 노드 찾기
$("el[attr$=val]") // 속성 attr 의 값이 val 로 끝나는 모든 el 노드 찾기
$("el[attr*=val]") // 속성 attr 의 값이 val 을 포함하고 있는 모든 el 노드 찾기
$("[attribute|='value']") // 속성값이 value값과 일치하거나 value- 으로 시작하는 요소들
$("[attr1][attr2]") // 지정된 속성중 하나를 가진 모든 요소들

// 예시
$("div[class*=test]") // class의 값이 test를 포함하고 있는 모든 div 노드
```

## 4. 노드 탐색

```javascript
$("ul").children() // 모든 자식 노드 찾기
$("ul").children(".select") // 특정 자식 노드 찾기

$("ul").children().first() // 첫번째 자식노드 찾기
$("ul").children(":first") // 첫번째 자식노드 찾기
$("ul").children().eq(0) // 첫번째 자식노드 찾기

$("ul").children().last() // 마지막 자식노드 찾기
$("ul").children(":last") // 마지막 자식노드 찾기
$("ul").children().eq(-1) // 마지막 자식노드 찾기

$("ul").children().eq(index) // n번째 자식노드 찾기

// 부모노드 탐색
$("ul.menu").parent() // 부모노드 찾기
$("ul.menu").parents() // 조상노드 찾기
$("ul.menu").parents(".header") // 특정 조상노드 찾기

// 형제노드 탐색
$("li.select").prev() // 특정 노드의 이전 형제노드 찾기
$("li.select").prevAll() // 특정 노드의 모든 이전 형제노드 찾기
$("li.select").prevAll("#info") // 이전 형제노드 중 #info를 가진 노드 찾기
$("li.select").next() // 특정 노드의 다음 형제노드 찾기
$("li.select").nextAll() // 특정 노드의 모든 다음 형제노드 찾기
$("li.select").nextAll("#info") // 다음 형제노드 중 #info를 가진 노드 찾기
$("li.select").siblings('.txt') // 형제 노드를 선택
```

## [출처 및 참고]
* [https://tutorialpost.apptilus.com/code/posts/jquery/jq-node-traverse/](https://tutorialpost.apptilus.com/code/posts/jquery/jq-node-traverse/)
