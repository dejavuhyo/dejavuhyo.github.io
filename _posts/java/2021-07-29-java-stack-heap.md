---
title: Java Stack Memory와 Heap Space
date: 2021-07-29 06:00:00 +0900
categories: [Language, Java]
tags: [java-stack-memory-heap-space, stack-memory-heap-space, stack-heap, stack, heap]
---

## 1. Stack Memory와 Heap Space 개념
JVM은 애플리케이션을 최적의 방식으로 실행하기 위해 메모리를 스택과 힙 메모리로 나뉜다. 새로운 변수와 객체를 선언하거나, 새로운 메소드를 호출하거나, String을 선언하거나, 유사한 작업을 수행할 때마다 JVM은 스택 메모리 또는 힙 공간에서 이러한 작업에 대한 메모리를 지정한다.

## 2. Stack Memory
Java의 스택 메모리는 정적 메모리 할당 및 스레드 실행에 사용된다. 여기에는 메소드에 고유한 기본값과 메소드에서 참조되는 힙에 있는 개체에 대한 참조가 포함된다.

이 메모리에 대한 액세스는 LIFO(후입선출) 순서이다. 새 메소드가 호출될 때마다 기본 변수 및 개체 참조와 같은 해당 메소드에 특정한 값을 포함하는 스택 맨 위에 새 블록이 생성된다.

메소드가 실행을 마치면 해당 스택 프레임이 플러시 되고 흐름이 호출 메소드로 돌아가고 다음 메소드를 위한 공간이 확보된다.

### 1) Stack Memory 주요기능

* 새로운 메소드가 각각 호출되고 반환될 때마다 커지고 축소된다.

* 스택 내부의 변수는 변수를 생성한 메소드가 실행되는 동안에만 존재한다.

* 메소드 실행이 완료되면 자동으로 할당 및 할당 해제된다.

* 이 메모리가 가득 차면 Java는 java.lang.StackOverFlowError를 발생시킨다.

* 이 메모리에 대한 액세스는 힙 메모리에 비해 빠르다.

* 이 메모리는 각 스레드가 자체 스택에서 작동하므로 스레드로부터 안전하다.

## 3. Heap Space
Java의 힙 공간은 런타임 시 Java 객체 및 JRE 클래스에 대한 동적 메모리 할당에 사용된다. 새로운 객체는 항상 힙 공간에 생성되고 이 객체에 대한 참조는 스택 메모리에 저장된다.

이러한 개체는 전역 액세스 권한을 가지며 응용 프로그램의 모든 위치에서 액세스할 수 있다.

이 메모리 모델은 세대라고 하는 더 작은 부분으로 더 세분화 된다.

* __Young Generation:__ 모든 새로운 객체가 할당되고 에이징되는 곳이다. 이것이 가득 차면 사소한 가비지 수집이 발생한다.

* __Old or Tenured Generation:__ 오래 살아남은 개체가 저장되는 곳이다. 객체가 Young Generation에 저장되면 객체의 연령에 대한 임계 값이 설정되고 해당 임계 값에 도달하면 객체가 이전 세대로 이동한다.

* __Permanent Generation:__ 런타임 클래스 및 애플리케이션 메소드에 대한 JVM 메타데이터로 구성된다.

### 1) Heap Memory 주요 기능
 
* Young Generation, Old or Tenured Generation, Permanent Generation을 포함하는 복잡한 메모리 관리 기술을 통해 액세스 된다.

* 힙 공간이 가득 차면 Java는  java.lang.OutOfMemoryError를 발생시킨다.

* 이 메모리에 대한 액세스는 스택 메모리보다 상대적으로 느리다.

* 스택과 달리 이 메모리는 자동으로 할당 해제되지 않는다. 메모리 사용의 효율성을 유지하기 위해 사용하지 않는 개체를 해제하려면 Garbage Collector가 필요하다.

* 스택과 달리 힙은 스레드로부터 안전하지 않으며 코드를 적절하게 동기화하여 보호해야 한다.

## 4. Example

```java
class Person {
    int id;
    String name;

    public Person(int id, String name) {
        this.id = id;
        this.name = name;
    }
}

public class PersonBuilder {
    private static Person buildPerson(int id, String name) {
        return new Person(id, name);
    }

    public static void main(String[] args) {
        int id = 23;
        String name = "John";
        Person person = null;
        person = buildPerson(id, name);
    }
}
```

① main() 메소드를 입력하면 스택 메모리에 이 메소드의 원시 및 참조를 저장하기 위한 공간이 생성된다.

* 정수 id의 기본값은 스택 메모리에 직접 저장된다.

* Person 유형의 참조 변수 person은 힙의 실제 객체를 가리키는 스택 메모리에도 생성된다.

② main()에서 매개 변수화된 생성자 Person(int, String)에 대한 호출은 이전 스택 위에 추가 메모리를 할당한다. 이것은 다음 항목이 저장된다.

* 스택 메모리에 있는 호출 객체의 this 객체 참조

* 스택 메모리의 기본값 id

* 힙 메모리의 문자열 풀에서 실제 문자열을 가리킬 문자열 인수 이름의 참조 변수

③ 기본 메소드는 buildPerson() 정적 메소드를 추가로 호출하는 것으로, 이전 메소드 위에 있는 스택 메모리에서 추가 할당이 수행된다. 이렇게 하면 위에서 설명한 방식으로 변수가 다시 저장된다.

④ 그러나 Person 유형의 새로 생성된 개체 person의 경우 모든 인스턴스 변수가 힙 메모리에 저장된다.

![heap-stack-diagram](/assets/img/2021-07-29-java-stack-heap/heap-stack-diagram.png)

## 5. 요약

| Parameter | Stack Memory | Heap Space |
|:---:|:---:|:---:|
| Application | 스택은 스레드 실행 중에 한 번에 하나씩 부분적으로 사용된다. | 전체 애플리케이션은 런타임 동안 힙 공간을 사용한다. |
| Size | 스택은 OS에 따라 크기 제한이 있으며 일반적으로 힙보다 작다. | 힙에는 크기 제한이 없다. |
| Storage | 힙 공간에서 생성된 객체에 대한 기본 변수 및 참조만 저장한다. | 새로 생성된 모든 객체가 여기에 저장된다. |
| Order | LIFO(Last-in First-out) 메모리 할당 시스템을 사용하여 액세스 된다. | 이 메모리는 Young Generation, Old 또는 Tenured Generation, Permanent Generation을 포함하는 복잡한 메모리 관리 기술을 통해 액세스 된다. |
| Life | 스택 메모리는 현재 메소드가 실행되는 동안에만 존재한다. | 애플리케이션이 실행되는 동안 힙 공간이 존재한다. |
| Efficiency | 힙에 비해 할당 속도가 훨씬 빠르다. | 스택에 비해 할당 속도가 느리다. |
| Allocation/Deallocation | 이 메모리는 각각 메소드가 호출되고 반환될 때 자동으로 할당 및 할당 해제된다. | 힙 공간은 Gargabe Collector에 의해 더 이상 참조되지 않을 때 새 개체가 생성되고 할당 해제될 때 할당된다. |

## [출처 및 참고]
* <https://www.baeldung.com/java-stack-heap>
