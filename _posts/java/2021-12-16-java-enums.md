---
title: Java Enums
author: dejavuhyo
date: 2021-12-16 10:20:00 +0900
categories: [Language, Java]
tags: [java-enums, enums, enum, 자바-열거형, 열거형]
---

## 1. 열거형
Java 5는 처음으로 enum 키워드를 도입했다. 항상 java.lang.Enum 클래스를 확장하는 특수 유형의 클래스를 나타낸다.

이러한 방식으로 정의된 상수는 코드를 더 읽기 쉽게 만들고, 컴파일 시간 확인을 허용하고, 허용되는 값 목록을 미리 문서화하고, 잘못된 값이 전달되어 예기치 않은 동작을 방지한다.

다음은 피자 주문 상태를 정의하는 열거형의 빠르고 간단한 예이다. 주문 상태는 ORDERED, READY 또는 DELIVERED 일 수 있다.

```java
public enum PizzaStatus {
    ORDERED,
    READY,
    DELIVERED;
}
```

또한 열거형에는 기존의 공개 정적 최종 상수를 사용하는 경우 작성해야 하는 유용한 메서드가 많이 있다.

## 2. 사용자 지정 열거형 메서드
열거형에 대한 몇 가지 추가 API 메서드를 정의한다.

```java
public class Pizza {
    private PizzaStatus status;
    public enum PizzaStatus {
        ORDERED,
        READY,
        DELIVERED;
    }

    public boolean isDeliverable() {
        if (getStatus() == PizzaStatus.READY) {
            return true;
        }
        return false;
    }
    
    // Methods that set and get the status variable.
}
```

## 3. "==" 연산자를 사용한 열거형 비교
열거형 유형은 JVM에 하나의 상수 인스턴스만 존재하도록 보장하므로 위의 예에서와 같이 `==` 연산자를 사용하여 두 변수를 안전하게 비교할 수 있다. 또한 `==` 연산자는 컴파일 시간 및 런타임 안전성을 제공한다.

먼저 다음 조각에서 런타임 안전성을 살펴본다. 여기서 `==` 연산자를 사용하여 상태를 비교한다. 두 값 모두 null일 수 있으며 NullPointerException이 발생하지 않는다. 반대로 equals 메서드를 사용하면 NullPointerException이 발생한다.

```java
if(testPz.getStatus().equals(Pizza.PizzaStatus.DELIVERED)); 
if(testPz.getStatus() == Pizza.PizzaStatus.DELIVERED); 
```

컴파일 시간 안전성에 관해서는 equals 메소드를 사용하여 비교하여 다른 유형의 enum이 동일한지 판별하는 예제를 살펴본다. enum과 getStatus 메소드의 값이 우연히 동일하기 때문이다. 그러나 논리적으로 비교는 거짓이어야 한다. `==` 연산자를 사용하여 이 문제를 방지한다.

컴파일러는 비교를 비호환성 오류로 플래그 지정한다.

```java
if(testPz.getStatus().equals(TestColor.GREEN));
if(testPz.getStatus() == TestColor.GREEN);
```

## 4. Switch 문에서 열거형 사용
switch문 에서 열거형 유형을 사용할 수도 있다.

```java
public int getDeliveryTimeInDays() {
    switch (status) {
        case ORDERED: return 5;
        case READY: return 2;
        case DELIVERED: return 0;
    }
    return 0;
}
```

## 5. 열거형의 필드, 메서드 및 생성자
열거형 내부에 생성자, 메서드 및 필드를 정의할 수 있으므로 매우 강력하다.

다음으로 피자 주문의 한 단계에서 다른 단계로의 전환을 구현하여 위의 예를 확장한다. 이전에 사용된 if 및 switch 문을 제거하는 방법을 살펴본다.

```java
public class Pizza {

    private PizzaStatus status;
    public enum PizzaStatus {
        ORDERED (5){
            @Override
            public boolean isOrdered() {
                return true;
            }
        },
        READY (2){
            @Override
            public boolean isReady() {
                return true;
            }
        },
        DELIVERED (0){
            @Override
            public boolean isDelivered() {
                return true;
            }
        };

        private int timeToDelivery;

        public boolean isOrdered() {return false;}

        public boolean isReady() {return false;}

        public boolean isDelivered(){return false;}

        public int getTimeToDelivery() {
            return timeToDelivery;
        }

        PizzaStatus (int timeToDelivery) {
            this.timeToDelivery = timeToDelivery;
        }
    }

    public boolean isDeliverable() {
        return this.status.isReady();
    }

    public void printTimeToDeliver() {
        System.out.println("Time to delivery is " + 
          this.getStatus().getTimeToDelivery());
    }
    
    // Methods that set and get the status variable.
}
```

아래의 테스트 스니펫은 이것이 어떻게 작동하는지 보여준다.

```java
@Test
public void givenPizaOrder_whenReady_thenDeliverable() {
    Pizza testPz = new Pizza();
    testPz.setStatus(Pizza.PizzaStatus.READY);
    assertTrue(testPz.isDeliverable());
}
```

## 6. EnumSet 및 EnumMap

### 1) 열거형 집합
EnumSet은 Enum 형식과 함께 사용하도록 의도된 특수 집합 구현이다.

HashSet과 비교할 때 사용되는 내부 비트 벡터 표현으로 인해 특정 Enum 상수 집합을 매우 효율적이고 간결하게 표현 한다. 또한 기존의 int 기반 "비트 플래그"에 대한 유형 안전 대안을 제공 하므로 더 읽기 쉽고 유지 관리가 쉬운 간결한 코드를 작성할 수 있다.

EnumSet은 RegularEnumSet과 JumboEnumSet의 두 가지 구현체가 있는 추상 클래스이며, 인스턴스화 시 열거형의 상수 수에 따라 이 중 하나가 선택된다.

따라서 대부분의 시나리오에서 열거 상수 모음으로 작업하려면 항상 이 집합(부분 집합 지정, 추가, 제거 및 containsAll 및 removeAll와 같은 대량 작업)을 사용하고 가능한 모든 상수에 대해 반복하려면 Enum.value()를 사용하는 것이 좋다.

아래 코드 조각에서 EnumSet을 사용하여 상수의 하위 집합을 만드는 방법을 볼 수 있다.

```java
public class Pizza {

    private static EnumSet<PizzaStatus> undeliveredPizzaStatuses =
      EnumSet.of(PizzaStatus.ORDERED, PizzaStatus.READY);

    private PizzaStatus status;

    public enum PizzaStatus {
        ...
    }

    public boolean isDeliverable() {
        return this.status.isReady();
    }

    public void printTimeToDeliver() {
        System.out.println("Time to delivery is " + 
          this.getStatus().getTimeToDelivery() + " days");
    }

    public static List<Pizza> getAllUndeliveredPizzas(List<Pizza> input) {
        return input.stream().filter(
          (s) -> undeliveredPizzaStatuses.contains(s.getStatus()))
            .collect(Collectors.toList());
    }

    public void deliver() { 
        if (isDeliverable()) { 
            PizzaDeliverySystemConfiguration.getInstance().getDeliveryStrategy()
              .deliver(this); 
            this.setStatus(PizzaStatus.DELIVERED); 
        } 
    }
    
    // Methods that set and get the status variable.
}
```

다음 테스트를 실행하면 Set 인터페이스의 EnumSet 구현의 성능을 보여준다.

```java
@Test
public void givenPizaOrders_whenRetrievingUnDeliveredPzs_thenCorrectlyRetrieved() {
    List<Pizza> pzList = new ArrayList<>();
    Pizza pz1 = new Pizza();
    pz1.setStatus(Pizza.PizzaStatus.DELIVERED);

    Pizza pz2 = new Pizza();
    pz2.setStatus(Pizza.PizzaStatus.ORDERED);

    Pizza pz3 = new Pizza();
    pz3.setStatus(Pizza.PizzaStatus.ORDERED);

    Pizza pz4 = new Pizza();
    pz4.setStatus(Pizza.PizzaStatus.READY);

    pzList.add(pz1);
    pzList.add(pz2);
    pzList.add(pz3);
    pzList.add(pz4);

    List<Pizza> undeliveredPzs = Pizza.getAllUndeliveredPizzas(pzList); 
    assertTrue(undeliveredPzs.size() == 3); 
}
```

### 2) 열거맵
EnumMap은 enum 상수를 키로 사용하기 위한 특수한 Map 구현이다. 대응하는 HashMap과 비교하여 내부적으로 배열로 표현되는 효율적이고 간결한 구현이다.

```java
EnumMap<Pizza.PizzaStatus, Pizza> map;
```

실제로 사용하는 방법의 예이다.

```java
public static EnumMap<PizzaStatus, List<Pizza>> 
  groupPizzaByStatus(List<Pizza> pizzaList) {
    EnumMap<PizzaStatus, List<Pizza>> pzByStatus = 
      new EnumMap<PizzaStatus, List<Pizza>>(PizzaStatus.class);
    
    for (Pizza pz : pizzaList) {
        PizzaStatus status = pz.getStatus();
        if (pzByStatus.containsKey(status)) {
            pzByStatus.get(status).add(pz);
        } else {
            List<Pizza> newPzList = new ArrayList<Pizza>();
            newPzList.add(pz);
            pzByStatus.put(status, newPzList);
        }
    }
    return pzByStatus;
}
```

다음 테스트를 실행하면 Map 인터페이스의 EnumMap 구현의 성능을 보여준다.

```java
@Test
public void givenPizaOrders_whenGroupByStatusCalled_thenCorrectlyGrouped() {
    List<Pizza> pzList = new ArrayList<>();
    Pizza pz1 = new Pizza();
    pz1.setStatus(Pizza.PizzaStatus.DELIVERED);

    Pizza pz2 = new Pizza();
    pz2.setStatus(Pizza.PizzaStatus.ORDERED);

    Pizza pz3 = new Pizza();
    pz3.setStatus(Pizza.PizzaStatus.ORDERED);

    Pizza pz4 = new Pizza();
    pz4.setStatus(Pizza.PizzaStatus.READY);

    pzList.add(pz1);
    pzList.add(pz2);
    pzList.add(pz3);
    pzList.add(pz4);

    EnumMap<Pizza.PizzaStatus,List<Pizza>> map = Pizza.groupPizzaByStatus(pzList);
    assertTrue(map.get(Pizza.PizzaStatus.DELIVERED).size() == 1);
    assertTrue(map.get(Pizza.PizzaStatus.ORDERED).size() == 2);
    assertTrue(map.get(Pizza.PizzaStatus.READY).size() == 1);
}
```

## 7. 열거형을 사용하여 디자인 패턴 구현

### 1) 싱글톤 패턴
일반적으로 Singleton 패턴을 사용하여 클래스를 구현하는 것은 매우 간단하다. 열거형은 싱글톤을 구현하는 빠르고 쉬운 방법을 제공한다.

또한 enum 클래스는 내부적으로 Serializable 인터페이스를 구현 하므로 JVM에서 클래스가 싱글톤임을 보장한다. 이는 역직렬화 중에 새 인스턴스가 생성되지 않도록 해야 하는 기존 구현과 다르다.

아래 코드 스니펫에서 싱글톤 패턴을 구현하는 방법을 볼 수 있다.

```java
public enum PizzaDeliverySystemConfiguration {
    INSTANCE;
    PizzaDeliverySystemConfiguration() {
        // Initialization configuration which involves
        // overriding defaults like delivery strategy
    }

    private PizzaDeliveryStrategy deliveryStrategy = PizzaDeliveryStrategy.NORMAL;

    public static PizzaDeliverySystemConfiguration getInstance() {
        return INSTANCE;
    }

    public PizzaDeliveryStrategy getDeliveryStrategy() {
        return deliveryStrategy;
    }
}
```

### 2) 전략 패턴
일반적으로 전략 패턴은 서로 다른 클래스에서 구현되는 인터페이스를 사용하여 작성된다.

새 전략을 추가한다는 것은 새 구현 클래스를 추가하는 것을 의미한다. 열거형을 사용하면 더 적은 노력으로 이를 달성할 수 있으며 새 구현을 추가한다는 것은 단순히 일부 구현으로 다른 인스턴스를 정의하는 것을 의미한다.

아래 코드 스니펫은 전략 패턴을 구현하는 방법을 보여준다.

```java
public enum PizzaDeliveryStrategy {
    EXPRESS {
        @Override
        public void deliver(Pizza pz) {
            System.out.println("Pizza will be delivered in express mode");
        }
    },
    NORMAL {
        @Override
        public void deliver(Pizza pz) {
            System.out.println("Pizza will be delivered in normal mode");
        }
    };

    public abstract void deliver(Pizza pz);
}
```

그런 다음 Pizza 클래스에 다음 메서드를 추가한다.

```java
public void deliver() {
    if (isDeliverable()) {
        PizzaDeliverySystemConfiguration.getInstance().getDeliveryStrategy()
          .deliver(this);
        this.setStatus(PizzaStatus.DELIVERED);
    }
}
```

```java
@Test
public void givenPizaOrder_whenDelivered_thenPizzaGetsDeliveredAndStatusChanges() {
    Pizza pz = new Pizza();
    pz.setStatus(Pizza.PizzaStatus.READY);
    pz.deliver();
    assertTrue(pz.getStatus() == Pizza.PizzaStatus.DELIVERED);
}
```

## 8. 자바 8과 열거형
Java 8에서 Pizza 클래스를 다시 작성할 수 있으며 람다 및 Stream API를 사용하여 getAllUndeliveredPizzas() 및 groupPizzaByStatus() 메서드가 얼마나 간결해 졌는지 확인할 수 있다.

```java
public static List<Pizza> getAllUndeliveredPizzas(List<Pizza> input) {
    return input.stream().filter(
      (s) -> !deliveredPizzaStatuses.contains(s.getStatus()))
        .collect(Collectors.toList());
}
```

```java
public static EnumMap<PizzaStatus, List<Pizza>> 
  groupPizzaByStatus(List<Pizza> pzList) {
    EnumMap<PizzaStatus, List<Pizza>> map = pzList.stream().collect(
      Collectors.groupingBy(Pizza::getStatus,
      () -> new EnumMap<>(PizzaStatus.class), Collectors.toList()));
    return map;
}
```


## 9. Enum의 JSON 표현
Jackson 라이브러리를 사용하면 열거형 유형이 POJO인 것처럼 JSON 표현을 가질 수 있다. 아래 코드 스니펫에서 동일한 항목에 대해 Jackson 주석을 사용하는 방법을 확인할 수 있다.

```java
@JsonFormat(shape = JsonFormat.Shape.OBJECT)
public enum PizzaStatus {
    ORDERED (5){
        @Override
        public boolean isOrdered() {
            return true;
        }
    },
    READY (2){
        @Override
        public boolean isReady() {
            return true;
        }
    },
    DELIVERED (0){
        @Override
        public boolean isDelivered() {
            return true;
        }
    };

    private int timeToDelivery;

    public boolean isOrdered() {return false;}

    public boolean isReady() {return false;}

    public boolean isDelivered(){return false;}

    @JsonProperty("timeToDelivery")
    public int getTimeToDelivery() {
        return timeToDelivery;
    }

    private PizzaStatus (int timeToDelivery) {
        this.timeToDelivery = timeToDelivery;
    }
}
```

다음과 같이 Pizza 및 PizzaStatus 를 사용할 수 있다.

```java
Pizza pz = new Pizza();
pz.setStatus(Pizza.PizzaStatus.READY);
System.out.println(Pizza.getJsonString(pz));
```

그러면 Pizza 상태에 대한 다음 JSON 표현이 생성된다.

```json
{
  "status" : {
    "timeToDelivery" : 2,
    "ready" : true,
    "ordered" : false,
    "delivered" : false
  },
  "deliverable" : true
}
```

## [출처 및 참고]
* [https://www.baeldung.com/a-guide-to-java-enums](https://www.baeldung.com/a-guide-to-java-enums)
