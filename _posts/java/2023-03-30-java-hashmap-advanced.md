---
title: Java HashMap 내부 동작
author: dejavuhyo
date: 2023-03-30 08:30:00 +0900
categories: [Language, Java]
tags: [java-hashmap, hashmap, hashmap-advanced, 자바-해시맵, 해시맵, 해시맵-내부동작]
---

## 1. put API
해시 맵에 값을 저장하기 위해 두 개의 매개변수를 사용하는 put API를 호출한다. key와 해당 value 이다.

```java
V put(K key, V value);
```

키 아래 맵에 값이 추가되면 키 객체의 `hashCode()` API가 호출되어 초기 해시 값으로 알려진 것을 검색한다.

이를 실제로 확인하기 위해 키 역할을 할 객체를 생성한다. 해싱의 첫 번째 단계를 시뮬레이션하기 위해 해시 코드로 사용할 단일 속성만 생성한다.

```java
public class MyKey {
    private int id;
   
    @Override
    public int hashCode() {
        System.out.println("Calling hashCode()");
        return id;
    }

    // constructor, setters and getters 
}
```

이 개체를 사용하여 해시 맵의 값을 매핑할 수 있다.

```java
@Test
public void whenHashCodeIsCalledOnPut_thenCorrect() {
    MyKey key = new MyKey(1);
    Map<MyKey, String> map = new HashMap<>();
    map.put(key, "val");
}
```

위의 코드에서는 아무 일도 일어나지 않지만 콘솔 출력에 주의한다. 실제로 hashCode 메서드가 호출된다.

```java
Calling hashCode()
```

다음으로 해시 맵의 `hash()` API를 내부적으로 호출하여 초기 해시 값을 사용하여 최종 해시 값을 계산한다.

최종 해시 값은 궁극적으로 내부 배열의 인덱스 또는 우리가 버킷 위치라고 부르는 것으로 귀결된다.

HashMap의 해시 함수는 다음과 같다.

```java
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

중요한 것은 키 개체의 해시 코드를 사용하여 최종 해시 값을 계산한다는 것이다.

put 함수 내부에서 최종 해시 값은 다음과 같이 사용된다.

```java
public V put(K key, V value) {
    return putVal(hash(key), key, value, false, true);
}
```

내부 putVal 함수가 호출되고 최종 해시 값이 첫 번째 매개변수로 제공된다.

해시 값을 계산하는데 키를 이미 사용했기 때문에 이 함수 내에서 키가 다시 사용되는 이유가 궁금할 수 있다.

그 이유는 **해시 맵이 `Map.Entry` 객체로 버킷 위치에 key와 value를 모두 저장하기 때문이다.**

모든 Java 컬렉션 프레임워크 인터페이스는 Collection 인터페이스를 확장하지만 Map은 확장하지 않는다.

```java
public interface Set<E> extends Collection<E>
```

그 이유는 맵이 다른 컬렉션처럼 정확히 단일 요소를 저장하는 것이 아니라 key-value 쌍의 컬렉션을 저장하기 때문이다.

따라서 add, toArray와 같은 Collection 인터페이스의 일반 메서드는 Map에서 의미가 없다.

해시 맵의 특별한 속성 중 하나는 null value와 null key를 허용한다는 것이다.

```java
@Test
public void givenNullKeyAndVal_whenAccepts_thenCorrect(){
    Map<String, String> map = new HashMap<>();
    map.put(null, null);
}
```

넣기 작업 중에 null key가 발견되면 자동으로 최종 해시 값 0이 할당된다. 즉, 기본 배열의 첫 번째 요소가 된다.

이는 키가 null일 때 해싱 작업이 없으므로 키의 hashCode API가 호출되지 않음을 의미하므로 궁극적으로 null 포인터 예외를 방지한다.

넣기 작업 중에 이전에 값을 저장하는데 이미 사용된 키를 사용하면 키와 연결된 이전 값이 반환된다.

```java
@Test
public void givenExistingKey_whenPutReturnsPrevValue_thenCorrect() {
    Map<String, String> map = new HashMap<>();
    map.put("key1", "val1");

    String rtnVal = map.put("key1", "val2");

    assertEquals("val1", rtnVal);
}
```

그렇지 않으면 null을 반환한다.

```java
@Test
public void givenNewKey_whenPutReturnsNull_thenCorrect() {
    Map<String, String> map = new HashMap<>();

    String rtnVal = map.put("key1", "val1");

    assertNull(rtnVal);
}
```

put이 null을 반환 하면 키와 연결된 이전 값이 null임을 의미할 수도 있으며 반드시 새로운 key-value 매핑일 필요는 없다.

```java
@Test
public void givenNullVal_whenPutReturnsNull_thenCorrect() {
    Map<String, String> map = new HashMap<>();

    String rtnVal = map.put("key1", null);

    assertNull(rtnVal);
}
```

## 2. get API
해시 맵에 이미 저장된 개체를 검색하려면 해당 개체가 저장된 키를 알아야 한다. get API를 호출 하고 여기에 키 개체를 전달한다.

```java
@Test
public void whenGetWorks_thenCorrect() {
    Map<String, String> map = new HashMap<>();
    map.put("key", "val");

    String val = map.get("key");

    assertEquals("val", val);
}
```

내부적으로 동일한 해싱 원칙이 사용된다. 초기 해시 값을 얻기 위해 키 객체의 `hashCode()` API가 호출된다.

```java
@Test
public void whenHashCodeIsCalledOnGet_thenCorrect() {
    MyKey key = new MyKey(1);
    Map<MyKey, String> map = new HashMap<>();
    map.put(key, "val");
    map.get(key);
}
```

이번에는 MyKey의 hashCode API가 두 번 호출된다. 한 번은 넣기, 한 번은 가져오기 이다.

```java
Calling hashCode()
Calling hashCode()
```

이 값은 최종 해시 값을 얻기 위해 내부 `hash()` API를 호출하여 다시 해시된다.

이전 섹션에서 본 것처럼 이 최종 해시 값은 궁극적으로 버킷 위치 또는 내부 배열의 인덱스로 귀결된다.

그런 다음 해당 위치에 저장된 값 객체가 검색되어 호출 함수로 반환된다.

반환된 값이 null이면 키 개체가 해시 맵의 값과 연결되지 않았음을 의미할 수 있다.

```java
@Test
public void givenUnmappedKey_whenGetReturnsNull_thenCorrect() {
    Map<String, String> map = new HashMap<>();

    String rtnVal = map.get("key1");

    assertNull(rtnVal);
}
```

또는 단순히 키가 null 인스턴스에 명시적으로 매핑되었음을 의미할 수 있다.

```java
@Test
public void givenNullVal_whenRetrieves_thenCorrect() {
    Map<String, String> map = new HashMap<>();
    map.put("key", null);
        
    String val=map.get("key");
        
    assertNull(val);
}
```

두 시나리오를 구별하기 위해 키를 전달하는 containsKey API를 사용할 수 있으며 해시 맵에서 지정된 키에 대한 매핑이 생성된 경우에만 true를 반환한다.

```java
@Test
public void whenContainsDistinguishesNullValues_thenCorrect() {
    Map<String, String> map = new HashMap<>();

    String val1 = map.get("key");
    boolean valPresent = map.containsKey("key");

    assertNull(val1);
    assertFalse(valPresent);

    map.put("key", null);
    String val = map.get("key");
    valPresent = map.containsKey("key");

    assertNull(val);
    assertTrue(valPresent);
}
```

위 테스트의 두 경우 모두 get API 호출의 반환 값은 null이지만 어느 것이 어떤 것인지 구별할 수 있다.

## 3. HashMap의 컬렉션 뷰
HashMap은 키와 값을 다른 컬렉션으로 취급할 수 있는 세 가지 뷰를 제공한다. 맵의 모든 키 세트를 얻을 수 있다.

```java
@Test
public void givenHashMap_whenRetrievesKeyset_thenCorrect() {
    Map<String, String> map = new HashMap<>();
    map.put("name", "baeldung");
    map.put("type", "blog");

    Set<String> keys = map.keySet();

    assertEquals(2, keys.size());
    assertTrue(keys.contains("name"));
    assertTrue(keys.contains("type"));
}
```

세트는 맵 자체에서 지원한다. 따라서 세트에 대한 모든 변경 사항은 맵에 반영된다.

```java
@Test
public void givenKeySet_whenChangeReflectsInMap_thenCorrect() {
    Map<String, String> map = new HashMap<>();
    map.put("name", "baeldung");
    map.put("type", "blog");

    assertEquals(2, map.size());

    Set<String> keys = map.keySet();
    keys.remove("name");

    assertEquals(1, map.size());
}
```

값의 컬렉션 보기를 얻을 수도 있다.

```java
@Test
public void givenHashMap_whenRetrievesValues_thenCorrect() {
    Map<String, String> map = new HashMap<>();
    map.put("name", "baeldung");
    map.put("type", "blog");

    Collection<String> values = map.values();

    assertEquals(2, values.size());
    assertTrue(values.contains("baeldung"));
    assertTrue(values.contains("blog"));
}
```

키 세트와 마찬가지로 이 컬렉션의 모든 변경 사항은 기본 맵에 반영된다.

마지막으로 맵의 모든 항목에 대한 집합 보기를 얻을 수 있다.

```java
@Test
public void givenHashMap_whenRetrievesEntries_thenCorrect() {
    Map<String, String> map = new HashMap<>();
    map.put("name", "baeldung");
    map.put("type", "blog");

    Set<Entry<String, String>> entries = map.entrySet();

    assertEquals(2, entries.size());
    for (Entry<String, String> e : entries) {
        String key = e.getKey();
        String val = e.getValue();
        assertTrue(key.equals("name") || key.equals("type"));
        assertTrue(val.equals("baeldung") || val.equals("blog"));
    }
}
```

해시 맵에는 특히 순서가 지정되지 않은 요소가 포함되어 있으므로 for each 루프에서 항목의 키와 값을 테스트할 때 모든 순서를 가정한다.

여러 번, 마지막 예제에서와 같이 루프에서 컬렉션 뷰를 사용하고 특히 반복자를 사용한다.

위의 모든 보기에 대한 반복자는 fail-fast 이다.

맵에서 구조적 수정이 이루어진 경우 반복자가 생성된 후 동시 수정 예외가 발생한다.

```java
@Test(expected = ConcurrentModificationException.class)
public void givenIterator_whenFailsFastOnModification_thenCorrect() {
    Map<String, String> map = new HashMap<>();
    map.put("name", "baeldung");
    map.put("type", "blog");

    Set<String> keys = map.keySet();
    Iterator<String> it = keys.iterator();
    map.remove("type");
    while (it.hasNext()) {
        String key = it.next();
    }
}
```

유일하게 허용되는 구조 수정은 반복자 자체를 통해 수행되는 제거 작업이다.

```java
public void givenIterator_whenRemoveWorks_thenCorrect() {
    Map<String, String> map = new HashMap<>();
    map.put("name", "baeldung");
    map.put("type", "blog");

    Set<String> keys = map.keySet();
    Iterator<String> it = keys.iterator();

    while (it.hasNext()) {
        it.next();
        it.remove();
    }

    assertEquals(0, map.size());
}
```

이러한 컬렉션 뷰에 대해 중요한 사항은 반복 성능이다. 이것은 해시 맵이 연결된 해시 맵 및 트리 맵과 비교하여 성능이 매우 떨어진다.

해시 맵에 대한 반복은 최악의 경우 `O(n)`에서 발생한다. 여기서 n은 용량과 항목 수의 합이다.

## 4. 해시맵 성능
해시 맵의 성능은 초기 용량 및 로드 비율의 두 가지 매개변수에 의해 영향을 받는다. 용량은 버킷 수 또는 기본 어레이 길이이며 초기 용량은 단순히 생성중 용량이다.

간단히 말해서 로드 팩터 또는 LF는 크기를 조정하기 전에 일부 값을 추가한 후 해시 맵이 얼마나 채워져야 하는지를 측정한 것이다.

기본 초기 용량은 16이고 기본 부하 계수는 0.75이다. 초기 용량 및 LF에 대한 사용자 지정 값을 사용하여 해시 맵을 만들 수 있다.

```java
Map<String,String> hashMapWithCapacity=new HashMap<>(32);
Map<String,String> hashMapWithCapacityAndLF=new HashMap<>(32, 0.5f);
```

Java 팀에서 설정한 기본값은 대부분의 경우에 잘 최적화되어 있다. 그러나 자체 값을 사용해야 하는 경우(매우 괜찮음) 수행 중인 작업을 알 수 있도록 성능 영향을 이해해야 한다.

해시 맵 항목의 수가 LF와 용량의 곱을 초과하면 재해싱이 발생한다. 즉, **초기 항목의 두 배 크기로 다른 내부 어레이가 생성되고 모든 항목이 새 어레이의 새 버킷 위치로 이동된다.**

**초기 용량이 낮으면 공간 비용이 줄어들지만 재해싱 빈도가 높아진다.** 재해싱은 분명히 매우 비용이 많이 드는 프로세스이다. 따라서 원칙적으로 많은 항목이 예상되는 경우 초기 용량을 상당히 높게 설정해야 한다.

반대로 초기 용량을 너무 높게 설정하면 반복 시간에 비용을 지불하게 된다.

따라서 **높은 초기 용량은 반복이 거의 또는 전혀 없는 많은 항목에 적합하다.**

**낮은 초기 용량은 반복이 많은 소수의 항목에 적합하다.**

## 5. HashMap의 충돌
충돌 또는 더 구체적으로 HashMap의 해시 코드 충돌은 둘 이상의 키 개체가 동일한 최종 해시 값을 생성하여 동일한 버킷 위치 또는 배열 인덱스를 가리키는 상황이다.

이 시나리오는 equals 및 hashCode 계약에 따라 Java에서 동일하지 않은 두 개체가 동일한 해시 코드를 가질 수 있기 때문에 발생할 수 있다.

기본 배열의 유한한 크기 때문에, 즉 크기를 조정하기 전에 발생할 수도 있다. 이 배열이 작을수록 충돌 가능성이 높아진다.

즉, 자바가 해시 코드 충돌 해결 기술을 구현한다는 것을 알 수 있다.

객체가 저장될 버킷을 결정하는 것은 키의 해시 값이다. 따라서 두 키의 해시 코드가 충돌하더라도 해당 항목은 여전히 동일한 버킷에 저장된다.

그리고 기본적으로 구현은 연결된 목록을 버킷 구현으로 사용한다.

초기 상수 시간 `O(1)` 입력 및 가져오기 작업은 충돌의 경우 선형 시간 `O(n)`에서 발생한다. 최종 해시 값으로 버킷 위치를 찾은 후 equals API를 사용하여 이 위치의 각 키를 제공된 키 객체와 비교하기 때문이다.

이 충돌 해결 기술을 시뮬레이트하기 위해 이전 키 개체를 약간 수정해본다.

```java
public class MyKey {
    private String name;
    private int id;

    public MyKey(int id, String name) {
        this.id = id;
        this.name = name;
    }
    
    // standard getters and setters
 
    @Override
    public int hashCode() {
        System.out.println("Calling hashCode()");
        return id;
    } 
 
    // toString override for pretty logging

    @Override
    public boolean equals(Object obj) {
        System.out.println("Calling equals() for key: " + obj);
        // generated implementation
    }

}
```

단순히 id 속성을 해시 코드로 반환하여 충돌이 발생하도록 하는 방법이다.

또한 equals 및 hashCode 구현에 로그 문을 추가하여 논리가 호출되는 시점을 정확히 알 수 있다.

어떤 지점에서 충돌하는 일부 객체를 저장하고 검색해본다.

```java
@Test
public void whenCallsEqualsOnCollision_thenCorrect() {
    HashMap<MyKey, String> map = new HashMap<>();
    MyKey k1 = new MyKey(1, "firstKey");
    MyKey k2 = new MyKey(2, "secondKey");
    MyKey k3 = new MyKey(2, "thirdKey");

    System.out.println("storing value for k1");
    map.put(k1, "firstValue");
    System.out.println("storing value for k2");
    map.put(k2, "secondValue");
    System.out.println("storing value for k3");
    map.put(k3, "thirdValue");

    System.out.println("retrieving value for k1");
    String v1 = map.get(k1);
    System.out.println("retrieving value for k2");
    String v2 = map.get(k2);
    System.out.println("retrieving value for k3");
    String v3 = map.get(k3);

    assertEquals("firstValue", v1);
    assertEquals("secondValue", v2);
    assertEquals("thirdValue", v3);
}
```

위의 테스트에서 세 가지 다른 키를 생성한다. 하나는 고유한 ID를 갖고 다른 두 개는 동일한 ID를 갖는다. id를 초기 해시 값으로 사용하기 때문에 이러한 키로 데이터를 저장하고 검색하는 동안 분명히 충돌이 있을 것이다.

그 외에도 앞서 본 충돌 해결 기술 덕분에 저장된 각 값이 올바르게 검색될 것으로 예상하므로 마지막 세 줄의 어설션이 있다.

테스트를 실행하면 통과해야 충돌이 해결되었음을 나타내며 생성된 로깅을 사용하여 충돌이 실제로 발생했는지 확인한다.

```text
storing value for k1
Calling hashCode()
storing value for k2
Calling hashCode()
storing value for k3
Calling hashCode()
Calling equals() for key: MyKey [name=secondKey, id=2]
retrieving value for k1
Calling hashCode()
retrieving value for k2
Calling hashCode()
retrieving value for k3
Calling hashCode()
Calling equals() for key: MyKey [name=secondKey, id=2]
```

저장 작업 중에 k1 및 k2는 해시 코드만 사용하여 해당 값에 성공적으로 매핑되었다.

그러나 k3의 저장은 그렇게 간단하지 않다. 시스템은 버킷 위치에 이미 k2에 대한 매핑이 포함되어 있음을 감지했다. 따라서 같음 비교를 사용하여 구분하고 두 매핑을 모두 포함하도록 연결 목록을 만들었다.

키 해시가 동일한 버킷 위치에 있는 다른 모든 후속 매핑은 동일한 경로를 따라 연결 목록의 노드 중 하나를 대체하거나 같음 비교가 모든 기존 노드에 대해 false를 반환하는 경우 목록의 헤드에 추가된다.

마찬가지로, 검색 중에 k3 및 k2는 값을 검색해야 하는 올바른 키를 식별하기 위해 비교 되었다.

마지막으로 Java 8부터 연결된 목록은 주어진 버킷 위치의 충돌 수가 특정 임계값을 초과한 후 충돌 해결에서 균형 잡힌 이진 검색 트리로 동적으로 대체된다.

이 변경은 충돌의 경우 저장 및 검색이 `O(log n)`에서 발생하기 때문에 성능 향상을 제공한다.

## [출처 및 참고]
* [https://www.baeldung.com/java-hashmap-advanced](https://www.baeldung.com/java-hashmap-advanced)
