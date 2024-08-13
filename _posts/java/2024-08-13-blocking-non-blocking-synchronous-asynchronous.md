---
title: Blocking, Non-Blocking, Synchronous, Asynchronous 차이점
author: dejavuhyo
date: 2024-08-13 08:55:00 +0900
categories: [Language, Java]
tags: [blocking, non-blocking, synchronous, asynchronous, 블로킹, 논블로킹, 동기, 비동기]
---

## 1. Blocking과 Non-Blocking
블로킹(Blocking)과 논블로킹(Non-Blocking)은 다른 주체가 작업할 때 자신의 제어권이 있는지 없는지로 구분한다.

### 1) Blocking
Blocking은 호출된 함수가 자신이 할 일을 모두 마칠 때까지 제어권을 계속 가지고서 호출한 함수에게 바로 돌려주지 않는 상황을 의미한다.

* 호출된 함수가 자신의 작업을 모두 마칠 때까지 호출한 함수에게 제어권을 반환하지 않는다.

* 다른 작업을 수행할 수 없게 만든다.

* CPU 자원을 효율적으로 사용하지 못한다.

* 예시: 동기적인 파일 I/O 작업이 블로킹 방식이다. 파일을 읽거나 쓸 때, 해당 작업이 완료될 때까지 다른 코드 실행이 중단된다.

### 2) Non-Blocking
Non-Blocking은 호출된 함수가 자신이 할 일을 모두 마치지 않았더라도 바로 제어권을 건네주어 호출한 함수가 다른 일을 진행할 수 있도록 해주는 상황을 의미한다.

* 호출된 함수가 바로 제어권을 호출한 함수에게 반환한다.

* 작업의 완료 여부와 상관없이 호출한 함수는 다른 작업을 계속할 수 있다.

* 예시: 비동기적인 파일 I/O 작업이 논블로킹 방식이다. 파일을 읽거나 쓸 때, 다른 코드 실행을 중단하지 않고 작업을 수행한다.

## 2. Synchronous와 Asynchronous
동기(Synchronous)와 비동기(Asynchronous)는 결과를 돌려주었을 때 순서와 결과에 관심이 있는지 없는지로 구분한다.

### 1) Synchronous
Synchronous는 호출된 함수의 수행 결과 및 종료를 호출한 함수와 함께 신경 쓰는 경우를 의미한다.

* 결과를 돌려줄 때 결과와 순서에 관심이 있는 방식이다.

* 호출된 함수의 수행 결과 및 종료를 호출한 함수와 함께 신경 쓴다.

* 예시: 함수 호출 후 결과를 기다리는 동안 다른 작업을 수행하지 않는 것이 동기 방식이다.

### 2) Asynchronous
Asynchronous는 호출된 함수의 수행 결과 및 종료를 호출된 함수 혼자 직접 쓰고 처리하는 경우를 의미한다.

* 결과를 돌려줄 때 결과와 순서에 관심이 없는 방식이다.

* 시작과 종료가 일치하지 않고 끝나는 동시에 시작하지 않는다.

* 예시: 비동기적인 네트워크 요청이 비동기 방식이다. 요청을 보낸 후 결과를 기다리지 않고 다른 작업을 수행할 수 있다.

## 3. 정리
블로킹과 동기 방식은 작업의 순서와 완료를 엄격하게 관리한다. 반면, 논블로킹과 비동기 방식은 작업의 효율성과 자원 사용의 최적화에 초점을 맞춘다.

애플리케이션의 성능과 사용자 경험에 큰 영향을 미치기 때문에 적절한 상황에서 각각의 방식을 선택하여 사용하는 것이 중요하다.

![comparison](/assets/img/2024-08-13-blocking-non-blocking-synchronous-asynchronous/comparison.png)

## [출처 및 참고]
* [https://f-lab.kr/insight/blocking-nonblocking-synchronous-asynchronous-differences](https://f-lab.kr/insight/blocking-nonblocking-synchronous-asynchronous-differences)
* [https://velog.io/@maketheworldwise/SyncAsync-BlockingNon-Blocking-%EB%AC%B4%EC%8A%A8-%EC%B0%A8%EC%9D%B4%EC%9D%BC%EA%B9%8C](https://velog.io/@maketheworldwise/SyncAsync-BlockingNon-Blocking-%EB%AC%B4%EC%8A%A8-%EC%B0%A8%EC%9D%B4%EC%9D%BC%EA%B9%8C)
* [https://developer.ibm.com/articles/l-async/](https://developer.ibm.com/articles/l-async/)
* [https://musma.github.io/2019/04/17/blocking-and-synchronous.html](https://musma.github.io/2019/04/17/blocking-and-synchronous.html)
* [https://homoefficio.github.io/2017/02/19/Blocking-NonBlocking-Synchronous-Asynchronous/](https://homoefficio.github.io/2017/02/19/Blocking-NonBlocking-Synchronous-Asynchronous/)
