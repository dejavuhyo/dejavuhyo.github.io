---
title: Java의 기본 구문
author: dejavuhyo
date: 2021-11-11 06:00:00 +0900
categories: [Language, Java]
tags: [java-syntax, java-variables, java-arrays, java-keywords, java-operators, java-compiling-executing, 자바-구문, 자바-변수, 자바-배열, 자바-키워드, 자바-연산자, 자바-컴파일-실행]
---

## 1. Java란

![java-logo](/assets/img/2012-11-11-java-syntax/java-logo.png)

Java는 정적 형식의 객체 지향 프로그래밍 언어이다. 또한 플랫폼 독립적이다. Java 프로그램은 Windows 시스템과 같은 한 유형의 머신에서 작성 및 컴파일될 수 있으며, 소스 코드를 수정하지 않고도 MacOS와 같은 다른 머신에서 실행할 수 있다.

## 2. Data Types
Java에는 기본 유형과 객체/참조 유형의 두 가지 광범위한 데이터 유형 범주가 있다.

기본 유형은 단순 데이터를 저장하고 데이터 조작의 기초를 형성하는 기본 데이터 유형이다. 예를 들어, Java에는 정숫값(int, long,  byte, short), 부동 소수점 값(float 및 double), 문자 값(char) 및 논릿값(boolean)에 대한 기본 유형이 있다.

반면에 참조 유형은 값 and/or 기타 개체에 대한 참조를 포함하거나 값이 없음을 나타내는 특수 값 null에 대한 참조를 포함하는 개체이다 .

String 클래스는 참조 형식의 좋은 예이다. 객체라고 하는 클래스의 인스턴스는 "Hello World"와 같은 일련의 문자를 나타낸다.

## 3. 변수 선언
Java에서 변수를 선언하려면 이름(식별자라고도 함)과 유형을 지정해야 한다. 간단한 예를 살펴보겠다.

```java
int a;
int b;
double c;
```

위의 예에서 변수는 선언된 유형에 따라 기본 초깃값을 받는다. 변수를 int 및 double로 선언했기 때문에 기본값은 각각 0과 0.0이다.

또는 할당 연산자(=)를 사용하여 선언하는 동안 변수를 초기화할 수 있다.

```java
int a = 10;
```

위의 예에서는 식별자가 a인 변수를 int 유형으로 선언하고 할당 연산자(=)를 사용하여 10의 값을 할당하고 세미콜론(;)으로 문을 종료한다. 자바에서는 모든 문장이 세미콜론으로 끝나는 것이 필수적이다.

식별자는 다음 규칙을 준수하는 문자, 숫자, 밑줄 및 달러 기호로 구성된 모든 길이의 이름이다.

* 문자, 밑줄(_) 또는 달러 기호($)로 시작

* 예약된 키워드가 될 수 없음

* true, false 또는 null일 수 없음

위의 코드 조각을 확장하여 간단한 산술 연산을 포함하면 아래와 같다.

```java
int a = 10;
int b = 5;
double c = a + b;
System.out.println( a + " + " + b + " = " + c);
```

위의 코드 조각의 처음 세 줄을 "10의 값을 a에 할당하고, 5의 값을 b에 할당하고, a와 b의 값을 합하고, 결과를 c에 할당"으로 읽을 수 있다. 마지막 줄에서는 작업 결과를 콘솔에 출력한다.

## 4. 배열
배열은 특정 유형의 값 모음을 저장할 수 있는 참조 유형이다. Java에서 배열을 선언하는 일반적인 구문은 다음과 같다.

```java
type[] identifier = new type[length];
```

형식은 모든 원시 형식 또는 참조 형식을 사용할 수 있다.

예를 들어 최대 100개의 정수를 저장할 수 있는 배열을 선언하는 방법을 살펴보겠다.

```java
int[] numbers = new int[100];
```

배열의 특정 요소를 참조하거나 요소에 값을 할당하려면 변수 이름과 해당 인덱스를 사용한다.

```java
numbers[0] = 1;
numbers[1] = 2;
numbers[2] = 3;
int thirdElement = numbers[2];
```

Java에서 배열 인덱스는 0에서 시작한다. 배열의 첫 번째 요소는 색인 0에 있고 두 번째 요소는 색인 1에 있다.

또한 numbers.length를 호출하여 배열의 길이를 얻을 수 있다.

```java
int lengthOfNumbersArray = numbers.length;
```

## 5. Java 키워드
키워드는 Java에서 특별한 의미가 있는 예약어이다.

예를 들어 public, static, class, main, new, instanceof는 Java의 키워드이므로 식별자(변수 이름)로 사용할 수 없다.

## 6. 연산자

### 1) 산술 연산자
Java는 수학적 계산 논리를 작성하는데 사용할 수 있는 다음과 같은 산술 연산자를 지원한다.

* ```+``` (plus 또는 addition 문자열 연결에도 사용됨)

* ```–``` (minus 또는 subtraction)

* ```*``` (multiplication)

* ```/``` (division)

* ```%``` (modulus 또는 remainder)

이전 코드 예제에서 더하기(+) 연산자를 사용하여 두 변수를 더했다. 다른 산술 연산자도 비슷하게 사용된다.

더하기(+)의 또 다른 용도는 문자열을 연결(결합)하여 완전히 새로운 문자열을 형성하는 것이다.

```java
String output =  a + " + " + b + " = " + c;
```

### 2) 논리 연산자
산술 연산자 외에도 Java는 부울 표현식을 평가하기 위해 다음과 같은 논리 연산자를 지원한다.

* ```&&``` (AND)

* ```||``` (OR)

* ```!``` (NOT)

논리적 AND 및 OR 연산자를 보여주는 다음 코드 조각에 대해 살펴본다. 첫 번째 예제에서는 숫자 변수가 2와 3으로 둘 다 나눌 수 있을 때 실행되는 print문을 보여 준다.

```java
int number = 6;
        
if (number % 2 == 0 && number % 3 == 0) {
    System.out.println(number + " is divisible by 2 AND 3");
}
````

실행되는 동안 숫자 2 또는 5로 나눌 수 있을 때이다.

```java
if (number % 2 == 0 || number % 5 == 0) {
    System.out.println(number + " is divisible by 2 OR 5");
}
```

### 3) 비교 연산자
한 변수의 값을 다른 변수의 값과 비교할 필요가 있을 때 Java의 비교 연산자를 사용할 수 있다.

* ```<``` (less than)
* ```<=``` (less than or equal to)
* ```>``` (greater than)
* ```>=``` (greater than or equal to)
* ```==``` (equal to)
* ```!=``` (NOT equal to)

예를 들어 비교 연산자를 사용하여 유권자의 자격을 결정할 수 있다.

```java
public boolean canVote(int age) {
    if(age < 18) {
        return false;
    }
    return true;
}
```

## 7. 프로그램 구조
Java 프로그램의 기본 단위는 Class이다. 클래스에는 하나 이상의 필드(속성이라고도 함), 메서드 및 내부 클래스라고 하는 다른 클래스 멤버도 있을 수 있다.

클래스를 실행하려면 기본 메서드가 있어야 한다. 주 메서드는 프로그램의 진입점을 나타낸다.

앞에서 검토했던 코드 조각 중 하나를 실행하기 위해 간단한 실행 가능한 클래스이다.

```java
public class SimpleAddition {

    public static void main(String[] args) {
        int a = 10;
        int b = 5;
        double c = a + b;
        System.out.println( a + " + " + b + " = " + c);
    }
}
```

클래스의 이름은 SimpleAddition이며, 클래스 안에는 우리의 논리를 수용하는 주요 방법이 있다. 중괄호 열기 및 닫기 사이의 코드 세그먼트를 코드 블록이라고 한다.

Java 프로그램의 소스 코드는 확장자가 ```.java```인 파일에 저장된다.

## 8. 프로그램 컴파일 및 실행
소스 코드를 실행하려면 먼저 컴파일해야 한다. 이 프로세스는 ```.class``` 파일 확장자를 가진 이진 파일을 생성한다. Java Runtime Environment(JRE)가 설치된 모든 컴퓨터에서 이진 파일을 실행할 수 있다.

위의 예에서 소스 코드를 SimpleAddition.java라는 파일로 저장하고 파일을 저장한 디렉토리에서 이 명령을 실행한다.

```shell
$ javac SimpleAddition.java
```

프로그램을 실행하기 위해 다음을 실행한다.

```shell
$ java SimpleAddition
```

위에 표시된 것과 동일한 출력이 콘솔에 생성된다.

```shell
10 + 5 = 15.0
```

## [출처 및 참고]
* <https://www.baeldung.com/java-syntax>
