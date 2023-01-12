---
title: Java 메모리 구조
author: dejavuhyo
date: 2022-07-19 15:00:00 +0900
categories: [Language, Java]
tags: [java-static, java-stack, java-heap, java-garbage-collection, static, stack, heap, garbage-collection, 자바-static, 자바-stack, 자바-heap, 자바-garbage-collection]
---

## 1. Static
정적(static)은 고정된이란 의미가 있다. Static이라는 키워드를 사용하여 Static 변수와 Static 메서드를 만들 수 있는데 다른 말로 정적 필드와 정적 메소드라고도 하며 이 둘을 합쳐 정적 멤버라고 한다. (= 클래스 멤버)

정적 필드와 정적 메서드는 객체(인스턴스)에 소속된 멤버가 아니라 클래스에 고정된 멤버이다. 그렇기 때문에 클래스 로더가 클래스를 로딩해서 메서드 메모리 영역에 적재할 때 클래스별로 관리된다. 따라서 클래스의 로딩이 끝나는 즉시 바로 사용할 수 있다.

Static 키워드를 통해 생성된 정적 멤버들은 Heap 영역이 아닌 Static 영역에 할당된다.

Static 영역에 할당된 메모리는 모든 객체가 공유하여 하나의 멤버를 어디서든지 참조할 수 있는 장점을 가지지만 Garbage Collector의 관리 영역 밖에 존재한다.

따라서 Static 영역에 있는 멤버들은 프로그램의 종료 시까지 메모리가 할당된 채로 존재하게 됩니다. 그렇기 때문에 Static을 너무 많이 사용하게 되면 만들고자 하는 시스템 성능에 악영향을 줄 수 있다.

## 2. 정적(Static) 멤버 선언
필드나 메서드를 생성할 때 인스턴스로 생성할 것인지 정적으로 생성할 것인지에 대한 기준은 공용으로 사용하느냐 아니냐로 판단하면 된다.

그냥 생성하면 자동으로 인스턴스로 생성되며, 정적으로 생성하면 필드와 메소드 선언 시 static이라는 키워들를 추가로 붙이면 된다.

정적 메서드는 클래스가 메모리에 올라갈 때 정적 메서드가 자동으로 생성된다. 그렇기 때문에 정적 메서드는 인스턴스를 생성하지 않아도 호출을 할 수 있다.

## 3. 자바의 메모리 공간
자바의 메모리의 공간은 크게 Static(스태틱) 영역, Stack(스택) 영역, Heap(힙) 영역으로 구분되고, 데이터 타입(자료형)에 따라서 해당 공간에 할당된다.

### 1) Static area(스태틱 메모리 영역)
하나의 JAVA 파일은 크게 필드(field), 생성자(constructor), 메서드(method)로 구성된다.

그중 필드 부분에서 선언된 변수(전역 변수)와 정적 멤버 변수(static이 붙은 자료형) Static 영역에 데이터를 저장한다. Static 영역의 데이터는 프로그램의 시작부터 종료가 될 때까지 메모리에 남아있게 된다. 다르게 말하면 전역 변수가 프로그램이 종료될 때까지 어디서든 사용이 가능한 이유이기도 하다.

따라서 전역 변수를 무분별하게 많이 사용하다 보면 메모리가 부족하여질 우려가 있어 필요한 변수만 사용할 필요가 있다.

### 2) Stack area(스택 메모리 영역)
메서드(int, double, byte, long, boolean 등)에 해당하는 지역변수(매개 변수 및 블록 문 내 변수 포함)의 데이터의 값이 저장되는 공간은 Stack(스택) 영역이다.

해당 메서드가 호출될 때 메모리에 할당되고 종료되면 메모리가 해제된다.

```java
public class StackAreaEx {
    public static void main(String[] args) {
        int a = 5;	a = 4;	a = 3;	a = 2;
        System.out.println(a);
        for(int i=0; i<5; i++){
        }
        // System.out.println(i); 컴파일 에러
    }
}
```

위의 소스 코드에서 a라는 변수는 main 메소드가 호출될 때 Stack 영역에 할당되고 종료 시 해제된다. 또한 a라는 변수의 값이 5, 4, 3, 2 순으로 값을 할당하였고 출력되는 값은 2가 출력된다. 이전 데이터는 지워지는 것이고 2라는 값만 출력된다.

즉, Stack 영역은 LIFO(Last In First Out)의 구조를 갖고 변수에 새로운 데이터가 할당되면 이전 데이터는 지워진다는 것을 알 수 있다.

![stack-area](/assets/img/2022-07-19-java-memory-structure/stack-area.png)

또한 for문 내에 int i를 정의하였는데 for문이 종료된 다음 i를 출력하지 못하는 이유는 지역변수이므로 for문의 종료와 함께 Stack 영역에서 해제되었기 때문이다.

### 3) Heap area(힙 메모리 영역)
참조형(Reference Type)의 데이터 타입을 갖는 객체(인스턴스), 배열 등은 Heap 영역에 데이터가 저장된다. 이때 변수(객체, 객체 변수, 참조 변수)는 Stack 영역의 공간에서 실제 데이터가 저장된 Heap 영역의 참조 값(reference value, 해시 코드/메모리에 저장된 주소를 연결해주는 값)을 new 연산자를 통해 리턴 받는다.

죽, 실제 데이터를 가진 Heap 영역의 참조 값을 Stack 영역의 객체가 갖고 있다. 이렇게 리턴 받은 참조 값을 가진 객체를 통해서만 해당 인스턴스를 핸들 할 수 있다.

```java
public class HeapAreaEx01 {
    public static void main(String[] args) {
        int[] a = null; // int형 배열 선언 및 Stack 영역 공간 할당
        System.out.println(a); // 결과 : null
        a = new int[5]; // Heap 영역에 5개의 연속된 공간 할당 및 
        // 변수 a에 참조값 할당
        System.out.println(a); // 결과 : @15db9742 (참조값)
    }
}

public class HeapAreaEx02 {
    public static void main(String[] args) {
        String str1 = new String("joker");
        String str2 = new String("joker");
        if (str1 == str2) {
            System.out.println("같은 주소값 입니다.");
        } else {
            System.out.println("다른 주소값 입니다.");
        }
    }
}

class A {
}

public class HeapArea {
    public static void main(String[] args) {
        A a = null; // A타입의 a객체 선언 및 Stack 영역 공간 할당
        System.out.println(a); // 결과 : null
        a = new A(); // Heap 메모리에 공간 할당 및 객체(a)에 참조값 할당
        System.out.println(a); // 결과 : @15db9742
    }
}
```

문자열을 저장하는 String도 참조형이다. new 연산자를 이용해서 생성하면 데이터는 Heap 영역에 저장되고 str1과 str2는 참조 값을 리턴 받는다. 저장된 주소가 다르기 때문에 `==`으로 비교 시 '다른 주소 값 입니다.'가 출력되는 것이다.

![heap-area](/assets/img/2022-07-19-java-memory-structure/heap-area.png)

> Heap에 저장된 데이터가 더 이상 사용이 불필요하다면 메모리 관리를 위해 JVM(자바 가상 머신)에 의해 알아서 해제된다. 이러한 기능을 가비지컬렉션(GC, 쓰레기 수집)이라고 한다.

## [출처 및 참고]
* [https://minhamina.tistory.com/211](https://minhamina.tistory.com/211)
* [https://coding-factory.tistory.com/524](https://coding-factory.tistory.com/524)
