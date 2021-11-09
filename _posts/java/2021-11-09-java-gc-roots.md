---
title: 가비지 컬렉터 루트
author: dejavuhyo
date: 2021-11-09 06:00:00 +0900
categories: [Language, Java]
tags: [java-gc-roots, garbage-collector-roots, garbage-collector, gc-roots, 가비지-컬렉터-루트, 가비지-컬렉터, 자바-가비지-컬렉터-루트]
---

## 1. GC 루트란
GC 루트는 Java의 가비지 수집 컨텍스트에서 사용되는 용어이다. 그것들은 가비지 수집기를 위한 특별한 개체이다.

이름에서 알 수 있듯이 GC 루트는 가비지 수집기 프로세스의 시작점이다. 일반적으로 GC 루트에서 직접 또는 간접적으로 참조되는 모든 개체는 가비지 수집되지 않는다.

## 2. GC 루트의 유형
GC 루트의 주요 유형이다.

* __Class:__ 시스템 클래스 로더에 의해 로드된 클래스. 정적 변수에 대한 참조도 포함한다.

* __Stack Local:__ 로컬 스택에 저장된 메소드에 대한 로컬 변수 및 매개변수

* __Active Java Threads:__ 모든 활성 Java 스레드

* __JNI References:__ JNI 호출을 위해 생성된 네이티브 코드 Java 객체. 로컬 변수, JNI 메소드에 대한 매개변수 및 전역 JNI 참조를 포함한다.

또한 몇 가지 가능한 유형의 GC 루트가 더 있다.

* 동기화를 위한 모니터로 사용되는 개체

* 목적을 위해 가비지 수집되지 않은 JVM 구현에 의해 정의된 특정 객체. 여기에는 중요한 예외 클래스, 시스템 클래스 로더 또는 사용자 정의 클래스 로더가 포함될 수 있다.

또한 특정 개체가 GC 루트인 JVM별 문서가 없다. 인기 있는 Java IDE 중 일부는 GC 루트 관점에서 메모리를 분석하는 기능을 제공한다. 이는 애플리케이션에서 메모리 누수를 분석할 때 유용하다.

## 3. GC Roots vs. Live Objects
응용 프로그램에서 적극적으로 사용하는 모든 개체는 GC용 라이브 개체이다. 또한 가비지 수집기는 라이브 개체를 삭제하지 않는다. GC 루트는 특수한 유형의 라이브 개체이다. 따라서, 모든 GC의 뿌리는 정의에 살아있는 개체이다.

## 4. GC 루트의 가비지 컬렉터 사용
사실 HotSpot JVM의 모든 GC 구현은 추적 수집기 이다. GC는 개체 그래프를 탐색하여 모든 라이브 개체를 식별한다. 또한 방문하고 활성 상태로 표시된 개체는 가비지 수집되지 않는다. 그래프를 순회하려면 시작점이 필요하다. 따라서 GC 루트는 수집기 추적의 시작점이다.

GC는 루트에서 그래프 탐색을 시작하고 그래프에서 방문한 모든 개체를 활성 상태로 표시한다. 프로세스는 애플리케이션에 정의된 모든 GC 루트에 대해 실행된다. 또한, 모든 GC 루트에서 시작하여 모든 그래프를 처리한다. 그런 다음 방문한 모든 개체를 활성 상태로 표시한다. 그 후 방문하지 않은 모든 개체는 가비지 수집된다.

## [출처 및 참고]
* <https://www.baeldung.com/java-gc-roots>