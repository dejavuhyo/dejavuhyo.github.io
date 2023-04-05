---
title: Java Hashtable
author: dejavuhyo
date: 2023-04-06 08:40:00 +0900
categories: [Language, Java]
tags: [java-hashtable, hashtable, 자바-해시테이블, 해시테이블]
---

## 1. Hashtable을 사용하는 경우
**[Hashtable](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Hashtable.html)은  Java에서 가장 오래된 해시 테이블 데이터 구조 구현이다.** HashMap은 JDK1.2에서 도입된 두 번째 구현이다.

각 단어에 정의가 있는 사전이 있다고 가정한다. 또한 사전에서 단어를 빠르게 가져오고, 삽입하고, 제거해야 한다.

따라서 Hashtable (또는 HashMap)이 의미가 있다. 단어는 고유해야 하므로 Hashtable의 키가 된다. 반면에 정의는 값이 된다.

## 2. 사용 예
사전 예제를 계속 살펴본다. Word를 키로 모델링한다.

```java
public class Word {
    private String name;

    public Word(String name) {
        this.name = name;
    }
    
    // ...
}
```

값이 Strings 라고 가정한다. 이제 Hashtable을 만들 수 있다.

```java
Hashtable<Word, String> table = new Hashtable<>();
```

항목을 추가한다.

```java
Word word = new Word("cat");
table.put(word, "an animal");
```

항목을 얻으려면 다음을 수행한다.

```java
String definition = table.get(word);
```

항목을 제거한다.

```java
definition = table.remove(word);
```

## 3. hashCode()의 중요성
Hashtable에서 키로 사용하려면 개체가 `hashCode()` 계약을 위반하지 않아야 한다. 즉, 동일한 객체는 동일한 코드를 반환해야 한다.

Hashtable은 배열을 사용한다. 배열의 각 위치는 null 이거나 하나 이상의 key-value 쌍을 포함할 수 있는 "버킷"이다. 각 쌍의 인덱스가 계산된다.

그러나 배열 끝에 새 요소를 추가하여 요소를 순차적으로 저장하지 않는 이유는 인덱스로 요소를 찾는 것이 요소를 순차적으로 반복하는 것보다 훨씬 빠르다는 것이다. 따라서 키를 인덱스에 매핑하는 함수가 필요하다.

### 1) 직접 주소 테이블
이러한 매핑의 가장 간단한 예는 직접 주소 테이블이다. 여기서 키는 인덱스로 사용된다.

```java
index(k)=k,
where k is a key
```

키는 고유하다. 즉, 각 버킷에는 하나의 key-value 쌍이 포함된다. 이 기술은 가능한 범위가 상당히 작은 정수 키에 대해 잘 작동한다.

그러나 여기에는 두 가지 문제가 있다.

* 첫째, 키는 정수가 아니라 Word 개체 이다.

* 둘째, 그것들이 정수라면 아무도 그것들이 작다고 보장하지 않을 것이다. 키가 1, 2 및 1000000이라고 상상해본다. 크기가 1000000인 큰 배열에 세 개의 요소만 있고 나머지는 낭비되는 공간이 된다.

`hashCode()` 메서드는 첫 번째 문제를 해결한다.

Hashtable의 데이터 조작 논리는 두 번째 문제를 해결한다.

### 2) hashCode() 메서드
모든 Java 객체는 int 값을 반환하는 `hashCode()` 메서드를 상속한다. 이 값은 개체의 내부 메모리 주소에서 계산된다. 기본적으로 `hashCode()`는 개별 개체에 대해 개별 정수를 반환한다.

따라서 모든 키 개체는 `hashCode()`를 사용하여 정수로 변환할 수 있다. 그러나 이 정수는 클 수 있다.

### 3) 범위 줄이기
`get()`, `put()` 및 `remove()` 메서드에는 가능한 정수의 범위를 줄이는 두 번째 문제를 해결하는 코드가 포함되어 있다.

수식은 키에 대한 인덱스를 계산한다.

```java
int index = (hash & 0x7FFFFFFF) % tab.length;
```

여기서 tab.length는 배열 크기이고 hash는 키의 `hashCode()` 메서드에서 반환된 숫자이다.

인덱스는 해시를 배열 크기로 나눈 것을 상기시킨다. 동일한 해시 코드는 동일한 인덱스를 생성한다.

### 4) 충돌
다른 해시 코드도 동일한 인덱스를 생성할 수 있다. 이것을 충돌이라고 부른다. 충돌을 해결하기 위해 Hashtable은 key-value 쌍의 LinkedList를 저장한다.

이러한 데이터 구조를 연결이 있는 해시 테이블이라고 한다.

### 5) 부하율
충돌로 인해 요소의 작업 속도가 느려진다고 추측하기 쉽다. 항목을 얻으려면 인덱스를 아는 것만으로는 충분하지 않지만 목록을 살펴보고 각 항목과 비교를 수행해야 한다.

따라서 충돌 횟수를 줄이는 것이 중요하다. 배열이 클수록 충돌 가능성이 작아진다. 부하율은 어레이 크기와 성능 사이의 균형을 결정한다. 기본적으로 0.75이다. 즉, 버킷의 75%가 비어 있지 않으면 어레이 크기가 두 배가 된다. 이 작업은 `rehash()` 메서드에 의해 실행된다.

### 6) equals() 및 hashCode() 재정의
항목을 Hashtable에 넣고 꺼낼 때 동일한 키 인스턴스뿐만 아니라 동일한 키로도 값을 얻을 수 있다.

```java
Word word = new Word("cat");
table.put(word, "an animal");
String extracted = table.get(new Word("cat"));
```

평등 규칙을 설정하기 위해 키의 `equals()` 메서드를 재정의한다.

```java
public boolean equals(Object o) {
    if (o == this)
        return true;
    if (!(o instanceof Word))
        return false;

    Word word = (Word) o;
    return word.getName().equals(this.name);
}
```

그러나 `equals()`를 재정의할 때 `hashCode()`를 재정의하지 않으면 Hashtable이 해시 코드를 사용하여 키의 인덱스를 계산하기 때문에 두 개의 동일한 키가 다른 버킷에 포함될 수 있다.

위의 예에서 `hashCode()`를 재정의하지 않으면

* 여기에는 Word의 두 가지 인스턴스가 관련되어 있다. 첫 번째는 항목을 넣기 위한 것이고 두 번째는 항목을 가져오기 위한 것이다. 이러한 인스턴스는 동일하지만 `hashCode()` 메서드는 다른 숫자를 반환한다.

* 각 키의 인덱스는 섹션 3-3의 공식으로 계산된다. 이 공식에 따르면 다른 해시 코드는 다른 인덱스를 생성할 수 있다.

* 즉, 항목을 한 버킷에 넣은 다음 다른 버킷에서 꺼내려고 한다. 이러한 논리는 Hashtable을 깨뜨린다.

동일한 키는 동일한 해시 코드를 반환해야 하므로 `hashCode()` 메서드를 재정의한다.

```java
public int hashCode() {
    return name.hashCode();
}
```

또한 동일하지 않은 키가 다른 해시 코드를 반환하도록 하는 것이 좋다. 그렇지 않으면 동일한 버킷에 있게 된다. 이것은 성능에 영향을 미치므로 Hashtable의 장점 중 일부를 잃게 된다.

또한 String, Integer, Long 또는 다른 래퍼 유형의 키는 신경쓰지 않는다. `equal()` 및 `hashCode()` 메서드는 래퍼 클래스에서 이미 재정의되었다.

## 4. Hashtables 반복

### 1) Fail Fast: 반복
Fail-fast 반복이란 Iterator가 생성된 후 Hashtable이 수정되면 ConcurrentModificationException이 발생함을 의미한다.

먼저 Hashtable을 만들고 여기에 항목을 추가한다.

```java
Hashtable<Word, String> table = new Hashtable<Word, String>();
table.put(new Word("cat"), "an animal");
table.put(new Word("dog"), "another animal");
```

둘째, Iterator를 생성한다.

```java
Iterator<Word> it = table.keySet().iterator();
```

셋째, 테이블을 수정한다.

```java
table.remove(new Word("dog"));
```

이제 테이블을 반복하려고 하면 ConcurrentModificationException이 발생한다.

```java
while (it.hasNext()) {
    Word key = it.next();
}
```

```text
java.util.ConcurrentModificationException at java.util.Hashtable$Enumerator.next(Hashtable.java:1378)
```

ConcurrentModificationException은 예를 들어 한 스레드가 테이블을 반복하고 다른 스레드가 동시에 테이블을 수정하려고 할 때 버그를 찾아 예측할 수 없는 동작을 방지하는 데 도움이 된다.

### 2) Not Fail Fast: 열거형
Hashtable의 열거는 fail-fast가 아니다.

먼저 Hashtable을 만들고 여기에 항목을 추가한다.

```java
Hashtable<Word, String> table = new Hashtable<Word, String>();
table.put(new Word("1"), "one");
table.put(new Word("2"), "two");
```

둘째, Enumeration을 생성한다.

```java
Enumeration<Word> enumKey = table.keys();
```

셋째, 테이블을 수정한다.

```java
table.remove(new Word("1"));
```

이제 테이블을 반복하면 예외가 발생하지 않는다.

```java
while (enumKey.hasMoreElements()) {
    Word key = enumKey.nextElement();
}
```

### 3) 예측할 수 없는 반복 순서
Hashtable의 반복 순서는 예측할 수 없으며 항목이 추가된 순서와 일치하지 않는다.

이는 키의 해시 코드를 사용하여 각 인덱스를 계산하므로 이해할 수 있다. 또한 때때로 재해싱이 발생하여 데이터 구조의 순서를 재정렬한다.

```java
Hashtable<Word, String> table = new Hashtable<Word, String>();
    table.put(new Word("1"), "one");
    table.put(new Word("2"), "two");
    // ...
    table.put(new Word("8"), "eight");

    Iterator<Map.Entry<Word, String>> it = table.entrySet().iterator();
    while (it.hasNext()) {
        Map.Entry<Word, String> entry = it.next();
        // ...
    }
}
```

```text
five
four
three
two
one
eight
seven
```

## 5. Hashtable vs HashMap
Hashtable과 HashMap은 매우 유사한 기능을 제공한다.

둘 다 다음을 제공한다.

* 페일 패스트 반복

* 예측할 수 없는 반복 순서

몇 가지 차이점도 있다.

* HashMap은 Enumeration을 제공하지 않지만 Hashtable은 Fail-Fast Enumeration을 제공하지 않는다.

* Hashtable은 null 키와 null 값을 허용하지 않는 반면 HashMap은 하나의 null 키와 여러 개의 null 값을 허용한다.

* Hashtable의 메소드는 동기화되지만 HashMaps의 메소드는 동기화되지 않는다.

## 6. Java 8의 해시 테이블 API
Java 8은 코드를 더 깔끔하게 만드는 데 도움이 되는 새로운 메서드를 도입했다. 특히 일부 if 블록을 제거할 수 있다.

### 1) getOrDefault()
"dog"라는 단어의 정의를 가져와 테이블에 있는 경우 변수에 할당해야 한다고 가정한다. 그렇지 않으면 변수에 "찾을 수 없음"을 할당한다.

자바 8 이전:

```java
Word key = new Word("dog");
String definition;

if (table.containsKey(key)) {
     definition = table.get(key);
} else {
     definition = "not found";
}
```

자바 8 이후:

```java
definition = table.getOrDefault(key, "not found");
```

### 2) putIfAbsent()
아직 사전에 없는 경우에만 "cat"라는 단어를 입력해야 한다고 가정한다.

자바 8 이전:

```java
if (!table.containsKey(new Word("cat"))) {
    table.put(new Word("cat"), definition);
}
```

자바 8 이후:

```java
table.putIfAbsent(new Word("cat"), definition);
```

### 3) boolean remove()
"cat"라는 단어를 제거해야 하지만 그 정의가 "an animal"인 경우에만 제거해야 한다고 가정한다.

자바 8 이전:

```java
if (table.get(new Word("cat")).equals("an animal")) {
    table.remove(new Word("cat"));
}
```

자바 8 이후:

```java
boolean result = table.remove(new Word("cat"), "an animal");
```

마지막으로 이전 `remove()` 메서드는 값을 반환하지만 새 메서드는 boolean을 반환한다.

### 4) replace()
"cat"의 정의를 대체해야 하지만 이전 정의가 "a small domesticated carnivorous mammal"인 경우에만 해당한다고 가정한다.

자바 8 이전:

```java
if (table.containsKey(new Word("cat")) 
    && table.get(new Word("cat")).equals("a small domesticated carnivorous mammal")) {
    table.put(new Word("cat"), definition);
}
```

자바 8 이후:

```java
table.replace(new Word("cat"), "a small domesticated carnivorous mammal", definition);
```

### 5) computeIfAbsent()
이 메서드는 `putIfabsent()`와 유사하다. 그러나 `putIfabsent()`는 값을 직접 가져오고 `computeIfAbsent()`는 매핑 함수를 가져온다. 키를 확인한 후에만 값을 계산하며, 특히 값을 얻기 어려운 경우에는 이 방법이 더 효율적이다.

```java
table.computeIfAbsent(new Word("cat"), key -> "an animal");
```

따라서 위의 줄은 다음과 동일하다.

```java
if (!table.containsKey(cat)) {
    String definition = "an animal"; // note that calculations take place inside if block
    table.put(new Word("cat"), definition);
}
```

### 6) computeIfPresent()
이 메서드는 `replace()` 메서드와 유사하다. 그러나 다시 `replace()`는 값을 직접 가져오고 `computeIfPresent()`는 매핑 함수를 가져온다. if 블록 내부의 값을 계산하므로 더 효율적이다.

정의를 변경해야 한다고 가정한다.

```java
table.computeIfPresent(cat, (key, value) -> key.getName() + " - " + value);
```

따라서 위의 줄은 다음과 동일하다.

```java
if (table.containsKey(cat)) {
    String concatination=cat.getName() + " - " + table.get(cat);
    table.put(cat, concatination);
}
```

### 7) compute()
요소가 고유하지 않은 String 배열이 있다고 가정한다. 또한 배열에서 얻을 수 있는 문자열의 수를 계산한다. 배열은 다음과 같다.

```java
String[] animals = { "cat", "dog", "dog", "cat", "bird", "mouse", "mouse" };
```

또한 동물을 키로 포함하고 동물의 발생 횟수를 값으로 포함하는 Hashtable을 생성한다.

해결책은 다음과 같다.

```java
Hashtable<String, Integer> table = new Hashtable<String, Integer>();

for (String animal : animals) {
    table.compute(animal, 
        (key, value) -> (value == null ? 1 : value + 1));
}
```

마지막으로 테이블에 two cats, two dogs, one bird 그리고 two mouses가 있는지 확인한다.

```java
assertThat(table.values(), hasItems(2, 2, 2, 1));
```

### 8) merge()
위의 작업을 해결하는 또 다른 방법이 있다.

```java
for (String animal : animals) {
    table.merge(animal, 1, (oldValue, value) -> (oldValue + value));
}
```

두 번째 인수 1은 키가 아직 테이블에 없는 경우 키에 매핑되는 값이다. 키가 이미 테이블에 있으면 `oldValue+1`로 계산한다.

### 9) foreach()
이것은 항목을 반복하는 새로운 방법이다. 모든 항목을 인쇄한다.

```java
table.forEach((k, v) -> System.out.println(k.getName() + " - " + v)
```

### 10) replaceAll()
또한 반복 없이 모든 값을 바꿀 수 있다.

```java
table.replaceAll((k, v) -> k.getName() + " - " + v);
```

## [출처 및 참고]
* [https://www.baeldung.com/java-hash-table](https://www.baeldung.com/java-hash-table)
