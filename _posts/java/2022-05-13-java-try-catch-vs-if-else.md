---
title: Java try/catch와 if/else 차이점
author: dejavuhyo
date: 2022-05-13 15:50:00 +0900
categories: [Language, Java]
tags: [java-try-catch-vs-if-else, try-catch-vs-if-else, java-try-catch, java-if-else, try-catch-if-else-차이점]
---

## 1. try/catch와 if/else 차이점
`try/catch`는 try 블록에서 예외가 발생하면 그 즉시 블록이 종료되고 catch 블록으로 제어가 이동된다.

`try/catch`는 `블럭`이고 `if/else`는 `구문` 이라는 점이 다르다.

## 2. 에러처리

### 1) try/catch
`try/catch` 문은 정상적인 프로그램 흐름이 아닌 제어할 수 없는 문제에 대한 것이다.

예를 들어 _'파일에 쓰려고 하는데 파일 시스템이 꽉 찼습니까?'_ 이러한 상황은 일반적으로 `try/catch`로 처리해야 한다.

`try/catch`문은 어떤 예외가 발생했을 경우 `try/catch` 안의 모든 객체는 스코프를 벗어나 참조할 수 없게 되지만 `if/else` 문은 스코프가 벗어나지 않게 되므로 `try/catch`보다 더 위험하다. 또한 활성 스택에서 생성되는 지역 객체들의 자동 소멸을 위해 그것의 정보들을 유지한다.

따라서 `try/catch` 문은 `if/else` 문의 예외 처리와는 달리, 지역 객체들의 소멸 자가 자동으로 호출되므로 메모리 등 리소스 누수의 문제를 조금이나마 해결할 수 있다.

### 2) if/else
`if/else` 문은 정상적인 흐름과 일반적인 오류 검사여야 한다.

예를 들어 _'사용자가 필수 입력 필드를 채우는 데 실패했습니까?'_ 이러한 상황은 `try/catch` 문이 아니라 `if/else` 문을 사용한다.

`if/else` 문으로 에러를 처리한다면 에러가 발생한 객체에 대해서 수명이 유지되기 때문에 에러를 처리하는 동안에도 에러가 발생한 객체를 참조하는 코드가 정상적으로 컴파일된다.

## 3. try/catch의 단점
`try/catch` 블록은 유지해야 할 정보도 많고, 예외가 발생했을 때도 해주어야 할 일이 많기 때문에 코드 크기나 예외 발생 시 처리 속도는 `if/else`의 전통적인 반환 값을 통한 오류처리보다 느리다.

`try/catch` 블록의 수를 최소한으로 유지하면 성능이 약간 향상되지만, 예외가 발생하여 건너뛸 수 있는 작업을 제외하고는 작업을 이동해도 실제로 차이가 발생하지 않는다.

## [출처 및 참고]
* <https://moltak.tistory.com/4>
* <https://stackoverflow.com/questions/8621762/java-if-vs-try-catch-overhead>
* <https://code-examples.net/ko/q/4151ff>
