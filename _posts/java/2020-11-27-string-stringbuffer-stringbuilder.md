---
title: String, StringBuffer, StringBuilder 차이 및 장단점
author: dejavuhyo
date: 2020-11-27 11:05:00 +0900
categories: [Language, Java]
tags: [string, stringbuffer, stringbuilder]
---

## 1. 개념
Java에서 문자열을 다루는 대표적인 클래스로 String, StringBuffer, StringBuilder가 있다.

연산이 많이 않을때는 특별한 이슈가 발생할 가능성이 거의 없지만, 연산횟수가 많아지거나 멀티스레드, 경쟁 상태(Race Condition) 등의 상황이 자주 발생하면 각 클래스의 특징을 이해하고 상황에 맞는 적절한 클래스를 사용해야 한다.

String과 StringBuffer와 StringBuilder의 기본적인 차이는 String은 불변(Immutable), StringBuffer와 StringBuilder는 변함(Mutable) 이다.

## 2. String
String 객체는 한번 생성되면 할당된 메모리 공간이 변하지 않는다.

concat 메서드 또는 + 연산자를 통해 기존에 생성된 String 클래스 객체 문자열에 다른 문자열을 붙여도 기존 문자열에 새로운 문자열을 붙이는 것이 아니라, 새로운 String 객체를 만든 후 새 String 객체에 연결된 문자열을 저장하고 그 객체를 참조하도록 한다.

즉, String 클래스 객체는 Heap 메모리 영역(가비지 컬렉션이 동작하는 영역)에 생성하고 한번 생성된 객체의 내부 내용을 변화시킬 수 없다. (기존 객체가 제거되면 Java의 가비지 컬렉션이 회수한다.)

String 객체는 이러한 이유로 문자열 연산이 많은 경우 성능이 좋지 않다.

![img001](/assets/img/2020-11-27-string-stringbuffer-stringbuilder/img001.png)

## 3. StringBuffer와 StringBuilder
StringBuffer와 StringBuilder는 String과 다르게 동작한다. 문자열 연산 등으로 기존 객체의 공간이 부족하게 되는 경우 기존의 버퍼 크기를 늘리며 유연하게 동작한다. StringBuffer와 StringBuilder 클래스가 제공하는 메서드는 서로 동일하다.

StringBuffer와 StringBuilder의 차이점은 동기화 여부이다.

StringBuffer는 각 메서드별로 Synchronized Keyword가 존재하여, 멀티스레드 환경에서도 동기화를 지원한다. 그러나 StringBuilder는 동기화를 보장하지 않는다.

멀티스레드 환경이라면 값의 동기화 보장을 위해 StringBuffer를 사용하고, 단일스레드 환경이라면 StringBuilder를 사용하는 것이 좋다. 단일 스레드환경에서 StringBuffer를 사용한다고 문제가 되는 것은 아니지만, 동기화 관련 처리로 인해 StringBuilder에 비해 성능이 좋지 않다.

![img002](/assets/img/2020-11-27-string-stringbuffer-stringbuilder/img002.png)

## 4. 정리

* String
  - 짧은 문자열을 더할 경우
  - 문자열 연산이 적고 멀티스레드 환경일 경우

* StringBuffer
  - 스레드에 안전한 프로그램이 필요할 때나, 개발 중인 시스템의 부분이 스레드에 안전한지 모를 경우
  - 문자열 연산이 많고 멀티스레드 환경일 경우

* StringBuilder
  - 스레드에 안전한지 여부가 전혀 관계 없는 프로그램을 개발할 경우
  - 문자열 연산이 많고 단일스레드이거나 동기화를 고려하지 않아도 되는 경우

## [출처 및 참고]
* [https://12bme.tistory.com/42](https://12bme.tistory.com/42)
* [https://ifuwanna.tistory.com/221](https://ifuwanna.tistory.com/221)
