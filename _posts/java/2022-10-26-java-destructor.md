---
title: Java 소멸자
author: dejavuhyo
date: 2022-10-26 14:00:00 +0900
categories: [Language, Java]
tags: [java-destructor, destructor, garbage-collection, java-finalizer, java-finalize, java-close, 자바-소멸자, 소멸자, 가비지-컬렉션, 자바-종료자]
---

## 1. 자바 Destructor
객체를 생성할 때마다 Java는 자동으로 힙에 메모리를 할당한다. 마찬가지로 객체가 더 이상 필요하지 않을 때마다 메모리가 자동으로 할당 해제된다.

C와 같은 언어에서는 메모리에서 객체 사용을 마치면 수동으로 할당을 해제해야 한다. 불행히도 Java는 수동 메모리 할당 해제를 지원하지 않는다. 또한 Java 프로그래밍 언어의 기능 중 하나는 [가비지 수집](https://www.baeldung.com/jvm-garbage-collectors)이라는 기술을 사용하여 자체적으로 개체 소멸을 처리한다는 것이다.

## 2. Garbage Collection
가비지 컬렉션은 힙의 메모리에서 사용하지 않는 개체를 제거한다. 메모리 누수를 방지하는 데 도움이 된다. 간단히 말해서 특정 개체에 대한 참조가 더 이상 없고 개체에 더 이상 액세스할 수 없으면 가비지 수집기가 이 개체를 연결할 수 없는 것으로 표시하고 해당 공간을 회수한다.

가비지 수집을 제대로 처리하지 못하면 성능 문제가 발생할 수 있으며 결국 응용 프로그램의 메모리가 부족해진다.

프로그램에서 더 이상 액세스할 수 없는 상태에 도달하면 개체가 가비지 수집될 수 있다. 다음 두 상황 중 하나가 발생하면 개체에 더 이상 연결할 수 없다.

* 개체에 가리키는 참조가 없다.

* 개체에 대한 모든 참조가 범위를 벗어났다.

Java에는 가비지 수집을 지원하는 `System.gc()` 메서드가 포함되어 있다. 이 메서드를 호출하여 JVM에 가비지 수집기를 실행하도록 제안할 수 있다. 그러나 JVM이 실제로 호출할 것이라고 보장할 수는 없다. JVM은 요청을 무시할 수 있다.

## 3. Finalizer
[Object](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Object.html) 클래스는 `finalize()` 메서드를 제공한다. 가비지 수집기가 메모리에서 개체를 제거하기 전에 `finalize()` 메서드를 호출한다. 이 메서드는 0번 또는 1번 실행할 수 있다. 그러나 동일한 개체에 대해 두 번 실행할 수 없다.

Object 클래스 내부에 정의된 `finalize()` 메서드는 특별한 작업을 수행하지 않는다.

Finalizer의 주요 목표는 메모리에서 제거하기 전에 개체에서 사용하는 리소스를 해제하는 것이다. 예를 들어 데이터베이스 연결이나 다른 리소스를 닫는 메서드를 재정의할 수 있다.

BufferedReader 인스턴스 변수를 포함하는 클래스를 생성한다.

```java
class Resource {

    final BufferedReader reader;

    public Resource(String filename) throws FileNotFoundException {
        reader = new BufferedReader(new FileReader(filename));
    }

    public long getLineNumber() {
        return reader.lines().count();
    }
}
```

이 예에서는 리소스를 닫지 않았다. `finalize()` 메서드 내에서 닫을 수 있다.

```java
@Override
protected void finalize() {
    try {
        reader.close();
    } catch (IOException e) {
        // ...
    }
}
```

JVM이 `finalize()` 메서드를 호출 하면 BufferedReader 리소스가 해제된다. `finalize()` 메서드에서 throw된 예외는 개체 종료를 중지한다.

그러나 Java 9부터 `finalize()` 메서드는 더 이상 사용되지 않는다. `finalize()` 메서드를 사용 하면 혼란스럽고 제대로 사용하기 어려울 수 있다.

객체가 보유한 리소스를 해제하려면 대신 AutoCloseable 인터페이스 구현을 고려해야 한다. Cleaner 및 PhantomReference와 같은 클래스는 개체에 연결할 수 없게 되면 리소스를 관리하는 보다 유연한 방법을 제공한다.

### 1) AutoCloseable 구현
AutoCloseable 인터페이스는 `try-with-resources` 블록을 종료할 때 자동으로 실행되는 `close()` 메서드를 제한니다. 이 메서드 내에서 개체가 사용하는 리소스를 닫을 수 있다.

AutoCloseable 인터페이스를 구현하도록 예제 클래스를 수정한다.

```java
class Resource implements AutoCloseable {

    final BufferedReader reader;

    public Resource(String filename) throws FileNotFoundException {
        reader = new BufferedReader(new FileReader(filename));
    }

    public long getLineNumber() {
        return reader.lines().count();
    }

    @Override
    public void close() throws Exception {
        reader.close();
    }
}
```

`finalize()` 메서드를 사용하는 대신 `close()` 메서드를 사용하여 리소스를 닫을 수 있다.

### 2) Cleaner 클래스
개체가 팬텀에 도달할 수 있을 때 특정 작업을 수행하려는 경우 Cleaner 클래스를 사용할 수 있다. 즉, 개체가 완료되고 해당 메모리가 할당 해제될 준비가 되었을 때이다.

Cleaner를 정의한다.

```java
Cleaner cleaner = Cleaner.create();
```

다음으로 더 깨끗한 참조가 포함된 클래스를 만든다.

```java
class Order implements AutoCloseable {

    private final Cleaner cleaner;

    public Order(Cleaner cleaner) {
        this.cleaner = cleaner;
    }
}
```

Order 클래스 내부에 Runnable을 구현하는 정적 내부 클래스를 정의한다.

```java
static class CleaningAction implements Runnable {

    private final int id;

    public CleaningAction(int id) {
        this.id = id;
    }

    @Override
    public void run() {
        System.out.printf("Object with id %s is garbage collected. %n", id);
    }
}
```

내부 클래스의 인스턴스는 정리 작업을 나타낸다. 개체가 팬텀에 도달할 수 있게 된 후 실행되도록 각 정리 작업을 등록해야 한다.

청소 작업에 람다를 사용하지 않는 것을 고려해야 한다. 람다를 사용하면 객체 참조를 쉽게 캡처하여 객체가 팬텀에 도달할 수 없게 되는 것을 방지할 수 있다. 위와 같이 정적 중첩 클래스를 사용하면 개체 참조가 유지되지 않는다.

Order 클래스 내부에 Cleanable 인스턴스 변수를 추가한다.

```java
private Cleaner.Cleanable cleanable;
```

Cleanable 인스턴스는 청소 작업이 포함된 청소 개체를 나타낸다.

다음으로 청소 작업을 등록할 메서드를 만든다.

```java
public void register(Product product, int id) {
    this.cleanable = cleaner.register(product, new CleaningAction(id));
}
```

마지막으로 `close()` 메서드를 구현한다.

```java
public void close() {
    cleanable.clean();
}
```

`clean()` 메서드는 청소 가능 항목을 등록 취소하고 등록된 청소 작업을 호출한다. 이 메서드는 clean 호출 횟수와 관계없이 최대 한 번만 호출된다.

`try-with-resources` 블록 내에서 CleaningExample 인스턴스를 사용할 때 `close()` 메서드는 청소 작업을 호출한다.

```java
final Cleaner cleaner = Cleaner.create();
try (Order order = new Order(cleaner)) {
    for (int i = 0; i < 10; i++) {
        order.register(new Product(i), i);
    }
} catch (Exception e) {
    System.err.println("Error: " + e);
}
```

다른 경우에는 인스턴스가 팬텀에 연결할 수 있게 되면 클리너가 `clean()` 메서드를 호출 한다.

또한 `System.exit()` 동안 클리너의 동작은 구현에 따라 다르다. Java는 정리 작업이 호출되는지 여부를 보장하지 않는다.

## [출처 및 참고]
* <https://www.baeldung.com/java-destructor>
