---
title: Java 메모리 누수
author: dejavuhyo
date: 2021-11-03 06:00:00 +0900
categories: [Language, Java]
tags: [java-memory-leaks, memory-leaks, java-메모리-누수, 메모리-누수]
---

## 1. 메모리 누수란
메모리 누수는 더는 사용되지 않는 개체가 힙에 있지만, 가비지 수집기가 메모리에서 개체를 제거할 수 없어 불필요하게 유지 관리되는 상황이다.

메모리 누수는 메모리 리소스를 차단하고 시간이 지남에 따라 시스템 성능을 저하하기 때문에 좋지 않다. 그리고 처리하지 않으면 애플리케이션은 결국 리소스를 소진하고 결국 치명적인 java.lang.OutOfMemoryError로 종료된다.

힙 메모리에 상주하는 두 가지 유형의 객체(참조된 객체와 참조되지 않은 객체)가 있다. 참조된 개체는 응용 프로그램 내에서 여전히 활성 참조가 있는 개체인 반면 참조되지 않은 개체에는 활성 참조가 없다.

가비지 수집기는 참조되지 않은 개체를 주기적으로 제거하지만 아직 참조 중인 개체는 수집하지 않는다. 여기에서 메모리 누수가 발생할 수 있다.

![memory-leak-in-java](/assets/img/2021-11-03-java-memory-leaks/memory-leak-in-java.png)

### 1) 메모리 누수의 증상

* 장기간 지속적으로 애플리케이션을 실행하면 심각한 성능 저하

* 애플리케이션의 OutOfMemoryError 힙 오류

* 자발적이고 이상한 응용 프로그램 충돌

* 응용 프로그램에 연결 개체가 부족

## 2. 자바의 메모리 누수 유형

### 1) 정적 필드를 통한 메모리 누수
잠재적인 메모리 누수를 일으킬 수 있는 첫 번째 시나리오는 정적 변수를 많이 사용하는 것이다.

Java에서 정적 필드는 일반적으로 실행 중인 애플리케이션의 전체 수명과 일치하는 수명을 갖는다(ClassLoader가 가비지 수집에 적합하지 않은 경우).

* 정적 목록을 채우는 간단한 Java 프로그램

```java
public class StaticTest {
    public static List<Double> list = new ArrayList<>();

    public void populateList() {
        for (int i = 0; i < 10000000; i++) {
            list.add(Math.random());
        }
        Log.info("Debug Point 2");
    }

    public static void main(String[] args) {
        Log.info("Debug Point 1");
        new StaticTest().populateList();
        Log.info("Debug Point 3");
    }
}
```

이 프로그램을 실행하는 동안 힙 메모리를 분석하면 디버그 지점 1과 2 사이에서 예상대로 힙 메모리가 증가했음을 알 수 있다.

그러나 populateList() 메서드를 디버그 지점 3에 두면 이 VisualVM 응답에서 볼 수 있듯이 힙 메모리가 아직 가비지 수집되지 않았다.

![memory-with-static](/assets/img/2021-11-03-java-memory-leaks/memory-with-static.png)

그러나 위 프로그램의 2번 줄에서 static 키워드를 삭제하면 메모리 사용량이 크게 변경된다. 이 VisualVM 응답은 아래와 같다.

![memory-without-static](/assets/img/2021-11-03-java-memory-leaks/memory-without-static.png)

디버그 지점까지의 첫 번째 부분은 정적의 경우 얻은 결과와 거의 동일 하다. 그러나 이번에는 populateList()  메서드를 떠난 후, 목록에 대한 참조가 없기 때문에 목록의 모든 메모리가 가비지 수집된다.

따라서 우리는 정적 변수 사용에 매우 세심한 주의를 기울여야 한다. 컬렉션이나 큰 개체가 static으로 선언되면 응용 프로그램의 수명 내내 메모리에 남아 있으므로 다른 곳에서 사용할 수 있는 중요한 메모리를 차단한다.

* 방지 방법
  - 정적 변수 사용 최소화
  - 싱글톤을 사용할 때 빠르게 로드하는 대신 개체를 느리게 로드하는 구현에 의존

### 2) 비공개 리소스를 통해
새로운 연결을 만들거나 스트림을 열 때마다 JVM은 이러한 리소스에 대한 메모리를 할당한다. 몇 가지 예에는 데이터베이스 연결, 입력 스트림 및 세션 개체가 포함된다.

이러한 리소스를 닫는 것을 잊어버리면 메모리가 차단되어 GC가 접근할 수 없게 된다. 이는 프로그램 실행이 이러한 리소스를 닫는 코드를 처리하는 문에 도달하지 못하게 하는 예외의 경우에도 발생할 수 있다.

두 경우 모두 리소스에서 남겨진 열린 연결은 메모리를 소모하며 처리하지 않으면 성능이 저하되고 OutOfMemoryError 가 발생할 수도 있다.

* 방지 방법
  - 항상 finally 블록을 사용하여 리소스를 닫는다.
  - 리소스를 닫는 코드(최종 블록에서도) 자체에는 예외가 없어야 한다.
  - Java 7 이상을 사용할 때 try-with-resources 블록을 사용할 수 있다.

### 3) 부적절한 equals() 및 hashCode() 구현
새 클래스를 정의할 때 매우 흔한 실수는 equals() 및 hashCode() 메서드에 대해 적절한 재정의 메서드를 작성하지 않는 것이다.

HashSet 및 HashMap은 많은 작업에서 이러한 메서드를 사용하며 올바르게 재정의되지 않으면 잠재적인 메모리 누수 문제의 원인이 될 수 있다.

간단한 Person 클래스의 예를 들어 HashMap에서 키로 사용하겠다.

```java
public class Person {
    public String name;
    
    public Person(String name) {
        this.name = name;
    }
}
```

이제 이 키를 사용하는 Map에 중복된 Person 객체를 삽입할 것이다.

맵에는 중복 키가 포함될 수 없다.

```java
@Test
public void givenMap_whenEqualsAndHashCodeNotOverridden_thenMemoryLeak() {
    Map<Person, Integer> map = new HashMap<>();
    for(int i=0; i<100; i++) {
        map.put(new Person("jon"), 1);
    }
    Assert.assertFalse(map.size() == 1);
}
```

여기서는 Person을 키로 사용하고 있다. Map은 중복 키를 허용하지 않기 때문에 키로 삽입한 수많은 중복 Person 객체가 메모리를 증가시키지 않아야 한다.

그러나 적절한 equals() 메서드를 정의하지 않았기 때문에 중복 개체가 쌓여 메모리가 증가하므로 메모리에 하나 이상의 개체가 표시된다. 이를 위한 VisualVM의 힙 메모리는 다음과 같다.

![before-implementing-equals-and-hashcode](/assets/img/2021-11-03-java-memory-leaks/before-implementing-equals-and-hashcode.png)

	그러나 equals() 및 hashCode() 메서드를 적절하게 재정의했다면 이 Map에는 하나의 Person 객체만 존재할 것이다.

	Person 클래스에 대한 equals() 및 hashCode()의 적절한 구현을 살펴보겠다.

```java
public class Person {
    public String name;
    
    public Person(String name) {
        this.name = name;
    }
    
    @Override
    public boolean equals(Object o) {
        if (o == this) return true;
        if (!(o instanceof Person)) {
            return false;
        }
        Person person = (Person) o;
        return person.name.equals(name);
    }
    
    @Override
    public int hashCode() {
        int result = 17;
        result = 31 * result + name.hashCode();
        return result;
    }
}
```

이 경우 다음과 같은 주장이 성립한다.

```java
@Test
public void givenMap_whenEqualsAndHashCodeNotOverridden_thenMemoryLeak() {
    Map<Person, Integer> map = new HashMap<>();
    for(int i=0; i<2; i++) {
        map.put(new Person("jon"), 1);
    }
    Assert.assertTrue(map.size() == 1);
}
```

equals() 및 hashCode()를 적절히 재정의한 후 동일한 프로그램의 힙 메모리는 다음과 같다.

![after-implementing-equals-and-hashcode](/assets/img/2021-11-03-java-memory-leaks/after-implementing-equals-and-hashcode.png)

또 다른 예는 equals() 및 hashCode() 메서드를 사용하여 객체를 분석하고 캐시에 저장하는 Hibernate와 같은 ORM 도구를 사용하는 것이다.

Hibernate가 객체를 비교할 수 없고 캐시를 중복 객체로 채울 것이기 때문에 이러한 메서드가 재정의되지 않으면 메모리 누수 가능성이 상당히 높다.

* 방지 방법
  - 경험적으로 새 엔터티를 정의할 때 항상 equals() 및 hashCode() 메서드를 재정의한다.
  - 재정의하는 것만으로는 충분하지 않지만, 이러한 메서드도 최적의 방식으로 재정의해야 한다.

### 4) 외부 클래스를 참조하는 내부 클래스
이것은 비정적 내부 클래스(익명 클래스)의 경우에 발생한다. 초기화를 위해 이러한 내부 클래스에는 항상 둘러싸는 클래스의 인스턴스가 필요하다.

모든 비정적 내부 클래스에는 기본적으로 포함하는 클래스에 대한 암시적 참조가 있다. 애플리케이션에서 이 내부 클래스의 객체를 사용하면 포함하는 클래스의 객체가 범위를 벗어난 후에도 가비지 수집되지 않는다 .

많은 부피가 큰 객체에 대한 참조를 보유하고 정적이 아닌 내부 클래스를 갖는 클래스를 고려한다. 이제 내부 클래스의 개체를 만들 때 메모리 모델은 다음과 같다.

![inner-classes-that-reference-outer-classes](/assets/img/2021-11-03-java-memory-leaks/inner-classes-that-reference-outer-classes.png)

그러나 내부 클래스를 static으로 선언하면 동일한 메모리 모델이 다음과 같이 보인다.

![static-classes-that-reference-outer-classes](/assets/img/2021-11-03-java-memory-leaks/static-classes-that-reference-outer-classes.png)

이것은 내부 클래스 객체가 외부 클래스 객체에 대한 참조를 암시적으로 보유하여 가비지 수집에 대한 유효하지 않은 후보가 되기 때문에 발생한다. 익명 클래스의 경우에도 마찬가지이다.

* 방지 방법
  - 내부 클래스가 포함하는 클래스 멤버에 대한 액세스가 필요하지 않은 경우 정적 클래스로 전환하는 것을 고려한다.

### 5) finalize() 메서드를 통해
finalizers의 사용은 잠재적인 메모리 누수 문제의 또 다른 원인이다. 클래스의 finalize() 메서드가 재정의될 때마다 해당 클래스의 개체가 즉시 가비지 수집되지 않는다. 대신, GC는 최종화를 위해 대기열에 넣는다. 이 작업은 나중에 발생한다.

또한 finalize() 메서드로 작성된 코드가 최적이 아니고 종료자 대기열이 Java 가비지 수집기를 따라갈 수 없는 경우 조만간 애플리케이션이 OutOfMemoryError를 만날 운명이다.

이를 증명하기 위해 finalize() 메서드를 재정의한 클래스가 있고 메서드를 실행하는 데 약간의 시간이 걸린다고 가정해 보겠다. 이 클래스의 많은 개체가 가비지 수집되면 VisualVM에서 다음과 같다.

![finalize-method-overridden](/assets/img/2021-11-03-java-memory-leaks/finalize-method-overridden.png)

그러나 재정의된 finalize() 메서드를 제거 하면 동일한 프로그램에서 다음과 같은 응답을 제공한다.

![finalize-method-not-overridden](/assets/img/2021-11-03-java-memory-leaks/finalize-method-not-overridden.png)

* 방지 방법
  - 우리는 항상 finalizers를 피해야 한다.

### 6) Interned Strings
Java String 풀은 PermGen에서 HeapSpace로 전송될 때 Java 7에서 주요 변경을 거쳤다. 그러나 버전 6 이하에서 작동하는 애플리케이션의 경우 큰 Strings로 작업할 때 더 주의해야 한다.

거대한 String 객체를 읽고 해당 객체에 대해 intern()을 호출하면 PermGen(영구 메모리)에 있는 문자열 풀로 이동하고 애플리케이션이 실행되는 동안 그대로 유지된다. 이것은 메모리를 차단하고 애플리케이션에서 주요 메모리 누수를 생성한다.

JVM 1.6의 이 경우에 대한 PermGen은 VisualVM에서 다음과 같다.

![interned-strings](/assets/img/2021-11-03-java-memory-leaks/interned-strings.png)

이와 대조적으로 메소드에서 파일에서 문자열을 읽고 인턴을 하지 않으면 PermGen은 다음과 같다.

![normal-strings](/assets/img/2021-11-03-java-memory-leaks/normal-strings.png)

* 방지 방법
  - 이 문제를 해결하는 가장 간단한 방법은 String 풀이 Java 버전 7에서 HeapSpace로 이동하므로 최신 Java 버전으로 업그레이드하는 것이다.
  - 큰 문자열에서 작업하는 경우 잠재적인 OutOfMemoryError를 방지하기 위해 PermGen 공간의 크기를 늘린다.

> -XX:MaxPermSize=512m

### 7) ThreadLocals 사용
ThreadLocal은 상태를 특정 스레드로 격리할 수 있는 기능을 제공하여 스레드 안전을 달성할 수 있도록 하는 구성이다.

이 구성을 사용할 때 각 스레드는 ThreadLocal 변수 복사본에 대한 암시적 참조를 보유하고 스레드가 살아 있는 한 여러 스레드에서 리소스를 공유하는 대신 자체 복사본을 유지한다.

장점에도 불구하고 ThreadLocal 변수의 사용은 논란의 여지가 있다. 제대로 사용하지 않으면 메모리 누수가 발생하기로 악명이 높기 때문이다. Joshua Bloch는 스레드 로컬 사용에 대해 다음과 같이 언급했다.

> "쓰레드 풀을 부적절하게 사용하고 쓰레드 로컬을 부적절하게 사용하면 많은 곳에서 언급한 바와 같이 의도하지 않은 객체 유지가 발생할 수 있습니다. 그러나 스레드 로컬에게 책임을 돌리는 것은 부당합니다."

ThreadLocals의 메모리 누수

ThreadLocals는 보유 스레드가 더 이상 활성 상태가 아닌 경우 가비지 수집되어야 한다. 그러나 ThreadLocals가 최신 애플리케이션 서버와 함께 사용될 때 문제가 발생한다.

현대의 애플리케이션 서버는 새로운 요청을 생성하는 대신 스레드 풀을 사용한다(예를 들어 Apache Tomcat의 경우 실행자). 또한 별도의 클래스 로더도 사용한다.

애플리케이션 서버의 스레드 풀은 스레드 재사용의 개념에 따라 작동하므로 절대 가비지 수집되지 않고 대신 다른 요청을 처리하는 데 재사용된다.

이제 클래스가 ThreadLocal 변수를 생성했지만 명시적으로 제거하지 않으면 웹 응용 프로그램이 중지된 후에도 해당 개체의 복사본이 작업자 스레드에 남아 있으므로 개체가 가비지 수집되는 것을 방지한다.

* 방지 방법
  - ThreadLocals가 더 이상 사용되지 않을 때 정리하는 것이 좋다. ThreadLocals는 이 변수에 대한 현재 스레드 값을 제거하는 remove() 메서드를 제공한다.
  - ThreadLocal.set(null)을 사용하여 값을 지우지 않는다. 실제로 값을 지우지는 않지만 대신 현재 스레드와 연결된 Map을 조회하고 키-값 쌍을 각각 현재 스레드 및 null로 설정한다.
  - 예외의 경우에도 항상 닫혀 있는지 확인하기 위해 finally 블록에서 닫아야 하는 리소스로 ThreadLocal을 고려하는 것이 훨씬 좋다.

```java
try {
    threadLocal.set(System.nanoTime());
    //... further processing
}
finally {
    threadLocal.remove();
}
```

## 3. 메모리 누수를 처리하기 위한 기타 전략

### 1) 프로파일링 활성화
Java 프로파일러는 애플리케이션을 통해 메모리 누수를 모니터링하고 진단하는 도구이다. 예를 들어 메모리 할당 방법과 같이 애플리케이션 내부에서 진행 중인 작업을 분석한다.

프로파일러를 사용하여 다양한 접근 방식을 비교하고 리소스를 최적으로 사용할 수 있는 영역을 찾을 수 있다.

### 2) 자세한 가비지 컬렉션
자세한 가비지 수집을 활성화하여 GC의 자세한 추적을 추적하고 있다. 이를 활성화하려면 JVM 구성에 다음을 추가해야 한다.

> -verbose:gc

이 매개변수를 추가하면 GC 내부에서 일어나는 일에 대한 세부 정보를 볼 수 있다.

![verbose-garbage-collection](/assets/img/2021-11-03-java-memory-leaks/verbose-garbage-collection.png)

### 3) 참조 객체를 사용하여 메모리 누수 방지
메모리 누수를 처리하기 위해 java.lang.ref 패키지와 함께 내장된 Java의 참조 객체에 의존할 수도 있다. java.lang.ref 패키지를 사용하면 객체를 직접 참조하는 대신 가비지를 쉽게 수집할 수 있는 객체에 대한 특별한 참조를 사용한다.

### 4) Eclipse 메모리 누수 경고
JDK 1.5 이상의 프로젝트에서 Eclipse는 명백한 메모리 누수 사례가 발생할 때마다 경고 및 오류를 표시한다. 따라서 Eclipse에서 개발할 때 "Problems" 탭을 정기적으로 방문하여 메모리 누수 경고(있는 경우)에 대해 더 주의를 기울일 수 있다.

![eclipse-memory-leak-warnings](/assets/img/2021-11-03-java-memory-leaks/eclipse-memory-leak-warnings.png)

### 5) 벤치마킹
벤치마크를 실행하여 Java 코드의 성능을 측정하고 분석할 수 있다. 이런 식으로 우리는 동일한 작업을 수행하기 위한 대체 접근 방식의 성능을 비교할 수 있다. 이것은 우리가 더 나은 접근 방식을 선택하는 데 도움이 될 수 있고 기억을 보존하는 데 도움이 될 수 있다.

### 6) 코드 리뷰
마지막으로, 우리는 항상 간단한 코드 연습을 수행하는 고전적인 구식 방법을 사용한다.

어떤 경우에는 이 사소해 보이는 방법도 몇 가지 일반적인 메모리 누수 문제를 제거하는 데 도움이 될 수 있다.

## [출처 및 참고]
* <https://www.baeldung.com/java-memory-leaks>
