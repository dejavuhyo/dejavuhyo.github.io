---
title: Java Collection.stream().forEach()와 Collection.forEach()의 차이점
author: dejavuhyo
date: 2023-06-02 08:45:00 +0900
categories: [Language, Java]
tags: [java-collection-stream, collection-stream-foreach, collection-foreach, java-collection]
---

## 1. 간단한 목록
반복할 목록을 만든다.

```java
List<String> list = Arrays.asList("A", "B", "C", "D");
```

가장 간단한 방법은 향상된 `for-loop`를 사용하는 것이다.

```java
for(String s : list) {
    //do something with s
}
```

기능적 스타일의 Java를 사용하려면 `forEach()`를 사용할 수도 있다.

컬렉션에서 직접 수행할 수 있다.

```java
Consumer<String> consumer = s -> { System.out::println }; 
list.forEach(consumer);
```

또는 컬렉션의 스트림에서 `forEach()`를 호출할 수 있다.

```java
list.stream().forEach(consumer);
```

두 버전 모두 목록을 반복하고 모든 요소를 인쇄한다.

```text
ABCD ABCD
```

이 간단한 경우에는 `forEach()`에 차이가 없다.

## 2. Execution Order
`Collection.forEach()`는 컬렉션의 반복자(지정된 경우)를 사용하므로 항목의 처리 순서가 정의된다. 반대로 `Collection.stream().forEach()`의 처리 순서는 정의되지 않는다.

대부분의 경우 둘 중 어느 것을 선택하든 차이가 없다.

### 1) 병렬 스트림
병렬 스트림을 사용하면 여러 스레드에서 스트림을 실행할 수 있으며 이러한 상황에서는 실행 순서가 정의되지 않는다. Java는 `Collectors.toList()`와 같은 터미널 작업이 호출되기 전에 모든 스레드가 완료되기만 하면 된다.

먼저 컬렉션에서 직접 `forEach()`를 호출하고 두 번째로 병렬 스트림에서 호출하는 예이다.

```java
list.forEach(System.out::print);
System.out.print(" ");
list.parallelStream().forEach(System.out::print);
```

코드를 여러 번 실행하면 `list.forEach()`가 항목을 삽입 순서대로 처리하는 반면 `list.parallelStream().forEach()`는 실행할 때마다 다른 결과를 생성한다.

가능한 출력은 다음과 같다.

```text
ABCD CDBA
```

그리고 이것은 또 다른 것이다.

```text
ABCD DBCA
```

### 2) 사용자 정의 반복자
컬렉션을 역순으로 반복하기 위해 사용자 지정 반복자가 있는 목록을 정의한다.

```java
class ReverseList extends ArrayList<String> {

    @Override
    public Iterator<String> iterator() {

        int startIndex = this.size() - 1;
        List<String> list = this;

        Iterator<String> it = new Iterator<String>() {

            private int currentIndex = startIndex;

            @Override
            public boolean hasNext() {
                return currentIndex >= 0;
            }

            @Override
            public String next() {
                String next = list.get(currentIndex);
                currentIndex--;
                return next;
             }

             @Override
             public void remove() {
                 throw new UnsupportedOperationException();
             }
         };
         return it;
    }
}
```

그런 다음 컬렉션과 스트림에서 직접 `forEach()`를 사용하여 목록을 다시 반복한다.

```java
List<String> myList = new ReverseList();
myList.addAll(list);

myList.forEach(System.out::print);
System.out.print(" ");
myList.stream().forEach(System.out::print);
```

다른 결과를 얻는다.

```text
DCBA ABCD
```

결과가 다른 이유는 목록에서 직접 사용되는 `forEach()`가 사용자 정의 반복자를 사용하는 반면 **`stream().forEach()`는 단순히 반복자를 무시하고 목록에서 요소를 하나씩 가져오기 때문이다.**

## 3. 컬렉션 수정
많은 컬렉션(예: ArrayList 또는 HashSet)을 반복하는 동안 구조적으로 수정하면 안 된다. 반복 중에 요소가 제거되거나 추가되면 ConcurrentModification 예외가 발생한다.

또한 컬렉션은 빠르게 실패하도록 설계되었다. 즉, 수정이 있는 즉시 예외가 발생한다.

마찬가지로 스트림 파이프라인을 실행하는 동안 요소를 추가하거나 제거하면 ConcurrentModification 예외가 발생한다. 그러나 나중에 예외가 발생한다.

두 `forEach()` 메서드 간의 또 다른 미묘한 차이점은 Java가 반복자를 사용하여 요소를 수정하는 것을 명시적으로 허용한다는 것이다. 반대로 스트림은 비간섭이어야 한다.

### 1) 요소 제거
목록의 마지막 요소("D")를 제거하는 작업을 정의한다.

```java
Consumer<String> removeElement = s -> {
    System.out.println(s + " " + list.size());
    if (s != null && s.equals("A")) {
        list.remove("D");
    }
};
```

목록을 반복할 때 첫 번째 요소("A")가 인쇄된 후 마지막 요소가 제거된다.

```java
list.forEach(removeElement);
```

**`forEach()`는 `fail-fast` 이므로 반복을 중지하고 다음 요소가 처리되기 전에 예외를 확인한다.**

```text
A 4
Exception in thread "main" java.util.ConcurrentModificationException
	at java.util.ArrayList.forEach(ArrayList.java:1252)
	at ReverseList.main(ReverseList.java:1)
```

대신에 `stream().forEach()`를 사용한다.

```java
list.stream().forEach(removeElement);
```

여기에서 예외를 보기 전에 전체 목록을 계속 반복한다.

```text
A 4
B 3
C 3
null 3
Exception in thread "main" java.util.ConcurrentModificationException
	at java.util.ArrayList$ArrayListSpliterator.forEachRemaining(ArrayList.java:1380)
	at java.util.stream.ReferencePipeline$Head.forEach(ReferencePipeline.java:580)
	at ReverseList.main(ReverseList.java:1)
```

그러나 Java는 [ConcurrentModificationException](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/ConcurrentModificationException.html)이 전혀 발생하지 않는다고 보장하지 않는다. **즉, 이 예외에 의존하는 프로그램을 작성해서는 안 된다.**

### 2) 요소 변경
목록을 반복하면서 요소를 변경할 수 있다.

```java
list.forEach(e -> {
    list.set(3, "E");
});
```

그러나 `Collection.forEach()` 또는 `stream().forEach()`를 사용하여 이 작업을 수행하는 데 문제가 없지만 Java에서는 스트림에 대한 작업이 비간섭이어야 한다. 즉, 스트림 파이프라인을 실행하는 동안 요소를 수정하면 안된다.

그 이유는 스트림이 병렬 실행을 용이하게 해야 하기 때문이다. 여기서 스트림의 요소를 수정하면 예기치 않은 동작이 발생할 수 있다.

## [출처 및 참고]
* [https://www.baeldung.com/java-collection-stream-foreach](https://www.baeldung.com/java-collection-stream-foreach)
