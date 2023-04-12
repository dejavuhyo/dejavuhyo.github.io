---
title: Java ConcurrentSkipListMap
author: dejavuhyo
date: 2023-04-13 08:20:00 +0900
categories: [Language, Java]
tags: [java-concurrentskiplistmap, concurrentskiplistmap, 자바-컨커런트스킵리스트맵, 컨커런트스킵리스트맵]
---

## 1. 스트림 정렬 로직
여러 스레드에서 지속적으로 발생하는 이벤트 스트림이 있다고 가정한다. 마지막 60초의 이벤트와 60초보다 오래된 이벤트를 가져올 수 있어야 한다.

먼저 이벤트 데이터의 구조를 정의한다.

```java
public class Event {
    private ZonedDateTime eventTime;
    private String content;

    // standard constructors/getters
}
```

eventTime 필드를 사용하여 이벤트를 정렬된 상태로 유지하려고 한다. ConcurrentSkipListMap을 사용하여 이를 달성하려면 인스턴스를 생성하는 동안 Comparator를 생성자에 전달해야 한다.

```java
ConcurrentSkipListMap<ZonedDateTime, String> events = new ConcurrentSkipListMap<>(
    Comparator.comparingLong(v -> v.toInstant().toEpochMilli()));
```

타임스탬프를 사용하여 도착한 모든 이벤트를 비교한다. `compareLong()` 메서드를 사용하고 있으며 ZonedDateTime에서 긴 타임스탬프를 취할 수 있는 추출 함수를 전달하고 있다.

이벤트가 도착하면 `put()` 메서드를 사용하여 맵에 이벤트를 추가하기만 하면 된다. 이 방법에는 명시적인 동기화가 필요하지 않다.

```java
public void acceptEvent(Event event) {
    events.put(event.getEventTime(), event.getContent());
}
```

ConcurrentSkipListMap은 생성자에서 전달된 Comparator를 사용하여 아래에 있는 해당 이벤트의 정렬을 처리한다.

ConcurrentSkipListMap의 가장 눈에 띄는 장점은 잠금 없는 방식으로 데이터의 변경 불가능한 스냅샷을 만들 수 있는 방법이다. 지난 1분 이내에 도착한 모든 이벤트를 가져오려면 `tailMap()` 메서드를 사용하고 요소를 가져오려는 시간을 전달할 수 있다.

```java
public ConcurrentNavigableMap<ZonedDateTime, String> getEventsFromLastMinute() {
    return events.tailMap(ZonedDateTime.now().minusMinutes(1));
}
```

지난 1분 동안의 모든 이벤트를 반환한다. 그것은 변경 불가능한 스냅샷이 될 것이며 가장 중요한 것은 다른 쓰기 스레드가 명시적 잠금을 수행할 필요 없이 ConcurrentSkipListMap에 새 이벤트를 추가할 수 있다는 것이다.

이제 `headMap()` 메서드를 사용하여 지금부터 1분 후에 도착한 모든 이벤트를 가져올 수 있다.

```java
public ConcurrentNavigableMap<ZonedDateTime, String> getEventsOlderThatOneMinute() {
    return events.headMap(ZonedDateTime.now().minusMinutes(1));
}
```

그러면 1분보다 오래된 모든 이벤트의 변경 불가능한 스냅샷이 반환된다.

## 2. 정렬 스트림 로직 테스트
ConcurrentSkipListMap을 사용하여 정렬 논리를 구현한 후에는 각각 100개의 이벤트를 보내는 두 개의 작성기 스레드를 만들어 테스트 할 수 있다.

```java
ExecutorService executorService = Executors.newFixedThreadPool(3);
EventWindowSort eventWindowSort = new EventWindowSort();
int numberOfThreads = 2;

Runnable producer = () -> IntStream
  .rangeClosed(0, 100)
  .forEach(index -> eventWindowSort.acceptEvent(
      new Event(ZonedDateTime.now().minusSeconds(index), UUID.randomUUID().toString()))
  );

for (int i = 0; i < numberOfThreads; i++) {
    executorService.execute(producer);
}
```

각 스레드는 `acceptEvent()` 메서드를 호출하여 지금부터 "현재 마이너스 100초"까지 eventTime이 있는 이벤트를 전송한다.

그동안 1분 기간 내에 있는 이벤트의 스냅샷을 반환하는 `getEventsFromLastMinute()` 메서드를 호출할 수 있다.

```java
ConcurrentNavigableMap<ZonedDateTime, String> eventsFromLastMinute 
  = eventWindowSort.getEventsFromLastMinute();
```

eventsFromLastMinute의 이벤트 수는 생산자 스레드가 이벤트를 EventWindowSort로 보내는 속도에 따라 각 테스트 실행에서 달라진다. 반환된 스냅샷에 1분보다 오래된 단일 이벤트가 없다고 주장할 수 있다.

```java
long eventsOlderThanOneMinute = eventsFromLastMinute
  .entrySet()
  .stream()
  .filter(e -> e.getKey().isBefore(ZonedDateTime.now().minusMinutes(1)))
  .count();
 
assertEquals(eventsOlderThanOneMinute, 0);
```

그리고 스냅샷에는 1분 기간 내에 있는 이벤트가 0개 이상 있다.

```java
long eventYoungerThanOneMinute = eventsFromLastMinute
  .entrySet()
  .stream()
  .filter(e -> e.getKey().isAfter(ZonedDateTime.now().minusMinutes(1)))
  .count();
 
assertTrue(eventYoungerThanOneMinute > 0);
```

`getEventsFromLastMinute()`는 아래의 `tailMap()`을 사용한다.

ConcurrentSkipListMap에서 `headMap()` 메서드를 사용하는 `getEventsOlderThatOneMinute()`를 테스트 한다.

```java
ConcurrentNavigableMap<ZonedDateTime, String> eventsFromLastMinute 
  = eventWindowSort.getEventsOlderThatOneMinute();
```

이번에는 1분보다 오래된 이벤트의 스냅샷을 얻는다. 이러한 이벤트가 0개 이상 있다고 주장할 수 있다.

```java
long eventsOlderThanOneMinute = eventsFromLastMinute
  .entrySet()
  .stream()
  .filter(e -> e.getKey().isBefore(ZonedDateTime.now().minusMinutes(1)))
  .count();
 
assertTrue(eventsOlderThanOneMinute > 0);
```

다음으로 마지막 순간에 발생하는 단일 이벤트가 없다는 것이다.

```java
long eventYoungerThanOneMinute = eventsFromLastMinute
  .entrySet()
  .stream()
  .filter(e -> e.getKey().isAfter(ZonedDateTime.now().minusMinutes(1)))
  .count();
 
assertEquals(eventYoungerThanOneMinute, 0);
```

가장 중요한 점은 다른 스레드가 여전히 ConcurrentSkipListMap에 새 값을 추가하는 동안 데이터의 스냅샷을 찍을 수 있다는 것이다.

## [출처 및 참고]
* [https://www.baeldung.com/java-concurrent-skip-list-map](https://www.baeldung.com/java-concurrent-skip-list-map)
