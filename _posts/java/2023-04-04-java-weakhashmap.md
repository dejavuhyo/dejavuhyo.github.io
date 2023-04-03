---
title: Java WeakHashMap
author: dejavuhyo
date: 2023-04-04 08:45:00 +0900
categories: [Language, Java]
tags: [java-weakhashmap, weakhashmap, 자바-약한-참조-해시맵, 약한-참조-해시맵]
---

## 1.WeakHashMap
간단히 말해 WeakHashMap은 WeakReference 유형의 키를 사용하는 Map 인터페이스의 해시 테이블 기반 구현이다.

WeakHashMap의 항목은 키가 더 이상 일반적으로 사용되지 않을 때 자동으로 제거된다. 즉, 해당 키를 가리키는 단일 참조가 없다. 가비지 수집(GC) 프로세스가 키를 폐기하면 해당 항목이 Map에서 효과적으로 제거되므로 이 클래스는 다른 Map 구현과 다소 다르게 동작한다.

## 2. 강한 참조, 소프트 참조, 약한 참조
WeakHashMap의 작동 방식을 이해하려면 WeakHashMap 구현의 키에 대한 기본 구조인 WeakReference 클래스를 살펴봐야 한다. Java에는 세 가지 주요 유형의 참조가 있다.

### 1) 강한 참조
강한 참조는 일상적인 프로그래밍에서 사용하는 가장 일반적인 유형의 참조이다.

```java
Integer prime = 1;
```

변수 prime에는 값이 1인 Integer 개체에 대한 강력한 참조가 있다. 이를 가리키는 강력한 참조가 있는 개체는 GC에 적합하지 않다.

### 2) 소프트 참조
간단히 말해, SoftReference가 가리키는 객체는 JVM이 메모리를 절대적으로 필요로 할 때까지 가비지 수집되지 않는다.

```java
Integer prime = 1;  
SoftReference<Integer> soft = new SoftReference<Integer>(prime); 
prime = null;
```

기본 개체에는 이를 가리키는 강력한 참조가 있다.

다음으로 프라임 강한 참조를 소프트 참조로 래핑한다. 강력한 참조를 null로 만든 후 주요 개체는 GC에 적합하지만 JVM에 메모리가 절대적으로 필요한 경우에만 수집된다.

### 3) 약한 참조
약한 참조에 의해서만 참조되는 개체는 열심히 가비지 수집된다. 이 경우 GC는 메모리가 필요할 때까지 기다리지 않는다.

다음과 같은 방법으로 Java에서 WeakReference를 만들 수 있다.

```java
Integer prime = 1;  
WeakReference<Integer> soft = new WeakReference<Integer>(prime); 
prime = null;
```

기본 참조를 null로 만들면 기본 개체를 가리키는 다른 강력한 참조가 없으므로 다음 GC 주기에서 기본 개체가 가비지 수집된다.

WeakReference 유형의 참조는 WeakHashMap에서 키로 사용된다.

## 3. 효율적인 메모리 캐시로서의 WeakHashMap
큰 이미지 개체를 값으로 유지하고 이미지 이름을 키로 유지하는 캐시를 만들고 싶다고 가정한다. 문제를 해결하기 위해 적절한 Map 구현을 선택하고 싶다.

값 개체가 많은 메모리를 차지할 수 있으므로 간단한 HashMap을 사용하는 것은 좋은 선택이 아니다. 또한 애플리케이션에서 더 이상 사용하지 않는 경우에도 GC 프로세스에 의해 캐시에서 회수되지 않는다.

이상적으로는 GC가 사용하지 않는 개체를 자동으로 삭제할 수 있는 Map 구현이 필요하다. 큰 이미지 객체의 키가 애플리케이션에서 사용되지 않는 경우 해당 항목은 메모리에서 삭제된다.

다행스럽게도 WeakHashMap에는 정확히 이러한 특성이 있다. WeakHashMap을 테스트 하고 어떻게 작동하는지 확인해 본다.

```java
WeakHashMap<UniqueImageName, BigImage> map = new WeakHashMap<>();
BigImage bigImage = new BigImage("image_id");
UniqueImageName imageName = new UniqueImageName("name_of_big_image");

map.put(imageName, bigImage);
assertTrue(map.containsKey(imageName));

imageName = null;
System.gc();

await().atMost(10, TimeUnit.SECONDS).until(map::isEmpty);
```

BigImage 개체를 저장할 WeakHashMap 인스턴스를 만들고 있다. BigImage 객체를 값으로, imageName 객체 참조를 키로 넣는다. imageName은 지도에 WeakReference 유형으로 저장된다.

다음으로 imageName 참조를 null로 설정하므로 bigImage 개체를 가리키는 참조가 더 이상 없다. WeakHashMap의 기본 동작은 다음 GC에서 참조가 없는 항목을 회수하는 것이므로 이 항목은 다음 GC 프로세스에 의해 메모리에서 삭제된다.

JVM이 GC 프로세스를 트리거하도록 강제하기 위해 `System.gc()`를 호출하고 있다. GC 주기 후 WeakHashMap은 비어 있다.

```java
WeakHashMap<UniqueImageName, BigImage> map = new WeakHashMap<>();
BigImage bigImageFirst = new BigImage("foo");
UniqueImageName imageNameFirst = new UniqueImageName("name_of_big_image");

BigImage bigImageSecond = new BigImage("foo_2");
UniqueImageName imageNameSecond = new UniqueImageName("name_of_big_image_2");

map.put(imageNameFirst, bigImageFirst);
map.put(imageNameSecond, bigImageSecond);

assertTrue(map.containsKey(imageNameFirst));
assertTrue(map.containsKey(imageNameSecond));

imageNameFirst = null;
System.gc();

await().atMost(10, TimeUnit.SECONDS)
  .until(() -> map.size() == 1);
await().atMost(10, TimeUnit.SECONDS)
  .until(() -> map.containsKey(imageNameSecond));
```

imageNameFirst 참조만 null로 설정된다. imageNameSecond 참조는 변경되지 않은 상태로 유지된다. GC가 트리거된 후 Map에는 하나의 항목(imageNameSecond)만 포함된다.

## [출처 및 참고]
* [https://www.baeldung.com/java-weakhashmap](https://www.baeldung.com/java-weakhashmap)
