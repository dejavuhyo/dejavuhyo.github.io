---
title: Java 예약어
author: dejavuhyo
date: 2021-08-30 06:00:00 +0900
categories: [Language, Java]
tags: [java-reserved-word, reserved-word, java-예약어, 자바-예약어]
---

## 1. 예약어란
자바 예약어란 프로그래밍 언어인 자바(Java)에서 미리 정해둔 명령어를 말한다.

자바 예약어는 자바에 등록되어 있어 의미가 약속되어있는 명령어를 뜻한다. 프로그래밍에 있어 불필요한 시행착오를 줄이기 위해서 자바에서는 예약어를 사용하고 있다.

자바에 이미 예약어로 등록된 명령어는 속성의 변수명, 메소드명으로 사용할 수 없으며, 프로그래밍 과정에서 특정 의미가 있는 단어를 프로그래머가 식별자로 사용하게 되면 에러나 버그가 발생할 수 있다.

## 2. 종류
자바는 모든 표현식과 변수 등의 형식을 컴파일러에 알려야 하는 자료 형식에 엄격한 언어이다. 자료 형식을 엄격하게 함으로써 컴파일 시간에 자료형과 관련된 에러를 검출할 수 있어 실행 시간에 에러를 검출하는 시간을 줄일 수 있다.

자바의 자료 유형에는 Primitive types, User-defined typed, array가 있다. primitive types은 언어에 의해 정의되어 있고, 객체가 아닌 값을 가지는 자료형이다. 자바는 논리형, 문자, 바이트 정수, 단정수, 정수, 배정수, 부동 소수점 및 배정밀도 부동 소수점 등의 원시 자료형이 있다.

user-defined typers은 개발자가 클래스, 인터페이스, 열거형 및 어노테이션을 사용하여 작성한 후 생성하며, 값은 개체가 된다. 예를 들어, 자바의 String 클래스는 문자열을 표현하는 사용자 정의형으로, 문자열을 값으로 가지며 두 개의 문자열을 합치는 것과 같은 문자열에 관한 메소드들로 구성되어 있다. 사용자 정의형은 변수가 객체를 저장하는 메모리 영역에 대한 메모리의 주소 또는 다른 식별자의 값을 가지기 때문에 참조형이라고도 한다. 이와 반대로 원시 자료형의 변수들은 값을 직접 저장한다.

array는 참조된 엘리먼트를 메모리에 같은 크기로 연속적인 슬롯에 값을 저장하는 특별한 참조형이다. 이 자료형은 차원의 개수를 나타내는 하나 이상의 대괄호 쌍과 엘리먼트형으로 구성된다. 대괄호가 한 쌍이면 일차원, 두 쌍이면 이차원, 세 쌍 이상이면 이차원 배열의 일차원 배열이 된다.

| 타입 | 예약어 |
|:-----:|:-----:|
| 기본 데이터 타입 | boolean, byte, char, short, int, long, float, double |
| 접근 지정자 | private, protected, public |
| 클래스 관련 | class, abstract, interface, extends, implements, enum |
| 메서드 관련 | void, return |
| 제어문 관련 | if, else, swich, case, default, for, do, while, break, continue |
| 논리 리터널 | true, false |
| 예외 처리 관련 | try, catch, finally, throw, throws |
| 기타 | transient, volatile, package, import, synchronized, native, final, static, strictfp, assert |

## 3. 특징

| 자바 예약어 | 내용 |
|:-----:|:-----:|
| abstract | 추상클래스 또는 추상메소드에 사용함 |
| boolean | 논리형 참(true)와 거짓(false) 디폴트값은 false |
| break | 반복문 또는 Switch 문을 벗어나거나 멈출 때 사용 |
| byte | 데이터 정수형 타입 1byte=8bit 정수형:byte<short<int<long |
| case | switch 조건문에서 사용함. case 문에서는 리터럴과 상수만 허용 |
| catch | `try~catch~finally` 문에서 사용, 예외처리 할 때 사용 |
| char | 데이터 문자형 타입 2byte |
| class | 클래스 |
| continue | 반복문, 루프를 돌 때 현재 루프를 끝낸 뒤에 내용을 무시하고 다시 앞으로 돌아감. |
| default | 접근제어자 및 switch 문에서 쓰임. 접근제어자는 클래스, 메소드, 멤버변수 앞에 아무것도 선언을 안하면 default. switch 문에서 default는 case 문을 돌다가 해당하는 case가 없으면 default로 실행. |
| do | `do~while` 문에서 쓰임 |
| double | 데이터 실수형(변수) 타입 8byte |
| else | `if~else` 문에서 쓰임 |
| extends | 클래스 상속에 쓰임 |
| false | 거짓이라는 뜻으로 쓰이며 0과 같음 |
| finally | `try~catch~finally` 예외처리에 쓰임 |
| float | 데이터 실수형 타입 4byte |
| for | 반복문 중 하나로 초기값과 조건식 증가값이 들어감 |
| if | 일반적으로 사용되는 조건문 |
| implements | 인터페이스를 구현 할 때 사용 |
| import | 패키지 내의 클래스를 불러올 때 사용 |
| instanceof | 객체타입 검사시 사용된다. |
| int | 데이터 정수형 4byte |
| interface | 일종의 추상클래스로 상속을 목적으로 만들어짐 |
| long | 데이터 정수형 8byte |
| native | 자바 외 다른 프로그래밍 언어로 구현되었음을 알려주는 예약어 |
| new | 인스턴스, 객체를 만들 때 사용 |
| null | 메모리에 할당 되지 않은 상태 |
| package | 클래스를 모아둔 단위 |
| private | 접근제어자로 같은 클래스 내에서만 접근 가능 |
| protected | 접근제어자로 같은 패키지와 상속받은 클래스에서 접근 가능 |
| public | 접근제어자 중 가장 제한이 없는 것 |
| return | 메소드를 종료하거나 값을 반환 |
| short | 데이터 정수형 타입 2byte |
| static | 정적변수 혹은 클래스 변수 |
| super | 상위 클래스의 생성자를 호출 |
| switch | 조건문 중 하나 |
| synchronized | 쓰레드 동기화를 위해 사용 |
| this | 인스턴스가 본인을 가리킬 때 사용 |
| throw/throws | 예외처리에 사용 |
| true | 참이라는 뜻으로 1과 같다. |
| try | `try~catch~finally` 문에서 사용 |
| void | 리턴값이 없다. |
| while | 반복문 중 하나. |

## [출처 및 참고]
* <http://wiki.hash.kr/index.php/%EC%9E%90%EB%B0%94_%EC%98%88%EC%95%BD%EC%96%B4>
