---
title: Java 매핑 프레임워크 성능
author: dejavuhyo
date: 2025-05-22 11:20:00 +0900
categories: [Framework, Spring]
tags: [java-mapping, mapping-frameworks, 자바-매핑, 매핑-프레임워크]
---

## 1.매핑 프레임워크

### 1) Dozer
Dozer는 재귀를 사용하여 한 객체에서 다른 객체로 데이터를 복사하는 매핑 프레임워크이다. 이 프레임워크는 빈 간에 속성을 복사할 수 있을 뿐만 아니라, 서로 다른 유형 간에 자동으로 변환할 수도 있다.

Dozer 프레임워크를 사용하려면 프로젝트에 다음과 같은 종속성을 추가해야 한다.

```xml
<dependency>
    <groupId>com.github.dozermapper</groupId>
    <artifactId>dozer-core</artifactId>
    <version>6.5.2</version>
</dependency>
```

프레임워크에 대한 문서는 [여기](https://github.com/DozerMapper/dozer#what-is-dozer)에서 찾을 수 있으며, 최신 버전은 [여기](https://mvnrepository.com/search?q=com.github.dozermapper)에서 찾을 수 있다.

### 2) Orika
Orika는 한 객체에서 다른 객체로 데이터를 재귀적으로 복사하는 빈 대 빈 매핑 프레임워크이다.

Orika의 일반적인 작동 원리는 Dozer와 유사하다. 두 가지의 주요 차이점은 Orika가 바이트코드 생성을 사용한다는 것이다. 이를 통해 최소한의 오버헤드로 더 빠른 매퍼를 생성할 수 있다.

이를 사용하려면 프로젝트에 다음과 같은 종속성을 추가해야 한다.

```xml
<dependency>
    <groupId>ma.glasnost.orika</groupId>
    <artifactId>orika-core</artifactId>
    <version>1.5.4</version>
</dependency>
```

프레임워크의 실제 문서는 [여기](https://orika-mapper.github.io/orika-docs/)에서 찾을 수 있으며, 최신 버전은 [여기](https://mvnrepository.com/search?q=ma.glasnost.orika)에서 찾을 수 있다.

> 주의: Java 16 부터 불법적인 반사적 접근은 기본적으로 거부된다. Orika 1.5.4 버전은 이러한 반사적 접근을 사용하므로 Orika는 현재 Java 16과 함께 사용할 수 없다. 이 문제는 향후 1.6.0 버전이 출시되면 해결될 것으로 예상된다.

### 3) MapStruct
MapStruct는 빈 매퍼 클래스를 자동으로 생성하는 코드 생성기이다.

MapStruct는 다양한 데이터 유형 간의 변환 기능도 제공한다.

프로젝트에 MapStruct를 추가하려면  다음 종속성을 포함해야 합니다.

```xml
<dependency>
    <groupId>org.mapstruct</groupId>
    <artifactId>mapstruct</artifactId>
    <version>1.6.0.Beta1</version>
</dependency>
```

프레임워크에 대한 문서는 [여기](https://mapstruct.org/)에서 찾을 수 있으며, 최신 버전은 [여기](https://mvnrepository.com/search?q=org.mapstruct)에서 찾을 수 있다.

### 4) ModelMapper
ModelMapper는 객체 매핑을 단순화하는 프레임워크로, 규칙에 따라 객체 간 매핑 방식을 결정한다. 타입 안전 및 리팩토링 안전 API를 제공한다.

프레임워크에 대한 자세한 내용은 [설명서](https://modelmapper.org/)에서 확인할 수 있다.

프로젝트에 ModelMapper를 포함하려면 다음 종속성을 추가해야 합니다.

```xml
<dependency>
  <groupId>org.modelmapper</groupId>
  <artifactId>modelmapper</artifactId>
  <version>3.2.0</version>
</dependency>
```

프레임워크의 최신 버전은 [여기](https://mvnrepository.com/search?q=org.modelmapper)에서 찾을 수 있다.

### 5) JMapper
JMapper는 Java Beans 간의 사용하기 쉽고 고성능의 매핑을 제공하는 것을 목표로 하는 매핑 프레임워크이다.

이 프레임워크는 주석과 관계 매핑을 사용하여 DRY 원칙을 적용하는 것을 목표로 한다.

이 프레임워크는 주석 기반, XML 또는 API 기반 등 다양한 구성 방식을 허용한다.

프레임워크에 대한 자세한 내용은 [설명서](https://github.com/jmapper-framework/jmapper-core/wiki)에서 확인할 수 있다.

프로젝트에 JMapper를 포함하려면 종속성을 추가해야 한다.

```xml
<dependency>
    <groupId>com.googlecode.jmapper-framework</groupId>
    <artifactId>jmapper-core</artifactId>
    <version>1.6.1.CR2</version>
</dependency>
```

프레임워크의 최신 버전은 [여기](https://mvnrepository.com/search?q=com.googlecode.jmapper-framework)에서 찾을 수 있다.

## 2. 테스트 모델
매핑을 제대로 테스트하려면 소스 모델과 타겟 모델이 필요하다. 두 가지 테스트 모델을 만들었다.

첫 번째는 하나의 문자열 필드가 있는 간단한 POJO이다. 이를 통해 더 간단한 사례에서 프레임워크를 비교하고 더 복잡한 빈을 사용하면 변경 사항이 있는지 확인할 수 있다.

간단한 소스 모델은 아래와 같다.

```java
public class SourceCode {
    String code;
    // getter and setter
}
```

그리고 그 목적지는 매우 유사하다.

```java
public class DestinationCode {
    String code;
    // getter and setter
}
```

소스 빈의 실제 예는 다음과 같다.

```java
public class SourceOrder {
    private String orderFinishDate;
    private PaymentType paymentType;
    private Discount discount;
    private DeliveryData deliveryData;
    private User orderingUser;
    private List<Product> orderedProducts;
    private Shop offeringShop;
    private int orderId;
    private OrderStatus status;
    private LocalDate orderDate;
    // standard getters and setters
}
```

대상 클래스는 아래와 같다.

```java
public class Order {
    private User orderingUser;
    private List<Product> orderedProducts;
    private OrderStatus orderStatus;
    private LocalDate orderDate;
    private LocalDate orderFinishDate;
    private PaymentType paymentType;
    private Discount discount;
    private int shopId;
    private DeliveryData deliveryData;
    private Shop offeringShop;
    // standard getters and setters
}
```

전체 모델 구조는 [여기](https://github.com/eugenp/tutorials/tree/master/performance-tests/src/main/java/com/baeldung/performancetests/model/source)에서 확인할 수 있다.

## 3. 변환기
테스트 설정의 설계를 단순화하기 위해 Converter 인터페이스를 만들었다.

```java
public interface Converter {
    Order convert(SourceOrder sourceOrder);
    DestinationCode convert(SourceCode sourceCode);
}
```

그리고 모든 사용자 정의 매퍼는 이 인터페이스를 구현한다.

### 1) OrikaConverter
Orika는 전체 API 구현을 허용하므로 매퍼 생성이 크게 간소화된다.

```java
public class OrikaConverter implements Converter{
    private MapperFacade mapperFacade;

    public OrikaConverter() {
        MapperFactory mapperFactory = new DefaultMapperFactory
          .Builder().build();

        mapperFactory.classMap(Order.class, SourceOrder.class)
          .field("orderStatus", "status").byDefault().register();
        mapperFacade = mapperFactory.getMapperFacade();
    }

    @Override
    public Order convert(SourceOrder sourceOrder) {
        return mapperFacade.map(sourceOrder, Order.class);
    }

    @Override
    public DestinationCode convert(SourceCode sourceCode) {
        return mapperFacade.map(sourceCode, DestinationCode.class);
    }
}
```

### 2) DozerConverter
Dozer에는 다음 섹션이 포함된 XML 매핑 파일이 필요하다.

```xml
<mappings xmlns="http://dozermapper.github.io/schema/bean-mapping"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://dozermapper.github.io/schema/bean-mapping
  https://dozermapper.github.io/schema/bean-mapping.xsd">

    <mapping>
        <class-a>com.baeldung.performancetests.model.source.SourceOrder</class-a>
        <class-b>com.baeldung.performancetests.model.destination.Order</class-b>
        <field>
            <a>status</a>
            <b>orderStatus</b>
        </field>
    </mapping>
    <mapping>
        <class-a>com.baeldung.performancetests.model.source.SourceCode</class-a>
        <class-b>com.baeldung.performancetests.model.destination.DestinationCode</class-b>
    </mapping>
</mappings>
```

XML 매핑을 정의한 후 코드에서 사용할 수 있다.

```java
public class DozerConverter implements Converter {
    private final Mapper mapper;

    public DozerConverter() {
        this.mapper = DozerBeanMapperBuilder.create()
          .withMappingFiles("dozer-mapping.xml")
          .build();       
    }

    @Override
    public Order convert(SourceOrder sourceOrder) {
        return mapper.map(sourceOrder,Order.class);
    }

    @Override
    public DestinationCode convert(SourceCode sourceCode) {
        return mapper.map(sourceCode, DestinationCode.class);
    }
}
```

### 3) MapStructConverter
MapStruct 정의는 전적으로 코드 생성에 기반하기 때문에 매우 간단하다.

```java
@Mapper
public interface MapStructConverter extends Converter {
    MapStructConverter MAPPER = Mappers.getMapper(MapStructConverter.class);

    @Mapping(source = "status", target = "orderStatus")
    @Override
    Order convert(SourceOrder sourceOrder);

    @Override
    DestinationCode convert(SourceCode sourceCode);
}
```

### 4) JMapperConverter
JMapperConverter에는 더 많은 작업이 필요하다. 인터페이스를 구현한다.

```java
public class JMapperConverter implements Converter {
    JMapper realLifeMapper;
    JMapper simpleMapper;
 
    public JMapperConverter() {
        JMapperAPI api = new JMapperAPI()
          .add(JMapperAPI.mappedClass(Order.class));
        realLifeMapper = new JMapper(Order.class, SourceOrder.class, api);
        JMapperAPI simpleApi = new JMapperAPI()
          .add(JMapperAPI.mappedClass(DestinationCode.class));
        simpleMapper = new JMapper(
          DestinationCode.class, SourceCode.class, simpleApi);
    }

    @Override
    public Order convert(SourceOrder sourceOrder) {
        return (Order) realLifeMapper.getDestination(sourceOrder);
    }

    @Override
    public DestinationCode convert(SourceCode sourceCode) {
        return (DestinationCode) simpleMapper.getDestination(sourceCode);
    }
}
```

대상 클래스의 각 필드에 `@JMap` 애노테이션을 추가해야 한다. 또한, JMapper는 열거형 타입을 자체적으로 변환할 수 없으므로 사용자 지정 매핑 함수를 만들어야 한다.

```java
@JMapConversion(from = "paymentType", to = "paymentType")
public PaymentType conversion(com.baeldung.performancetests.model.source.PaymentType type) {
    PaymentType paymentType = null;
    switch(type) {
        case CARD:
            paymentType = PaymentType.CARD;
            break;

        case CASH:
            paymentType = PaymentType.CASH;
            break;

        case TRANSFER:
            paymentType = PaymentType.TRANSFER;
            break;
    }
    return paymentType;
}
```

### 5) ModelMapperConverter
ModelMapperConverter를 사용하려면 매핑하려는 클래스만 제공하면 된다.

```java
public class ModelMapperConverter implements Converter {
    private ModelMapper modelMapper;

    public ModelMapperConverter() {
        modelMapper = new ModelMapper();
    }

    @Override
    public Order convert(SourceOrder sourceOrder) {
       return modelMapper.map(sourceOrder, Order.class);
    }

    @Override
    public DestinationCode convert(SourceCode sourceCode) {
        return modelMapper.map(sourceCode, DestinationCode.class);
    }
}
```

## 4. 간단한 모델 테스트
성능 테스트를 위해 Java Microbenchmark Harness를 사용할 수 있다.

각 변환기 에 대해 BenchmarkMode를 `Mode.All`로 지정하여 별도의 벤치마크를 만들었다.

### 1) 평균 시간
JMH는 평균 실행 시간에 대해 다음과 같은 결과를 반환했다(짧을수록 좋음).

| Framework Name | Average running time (in ms per operation) |
|:-----:|:-----:|
| MapStruct | $$10^-5 |
| JMapper | $$10^-5 |
| Orika | 0.001 |
| ModelMapper | 0.002 |
| Dozer | 0.004 |

이 벤치마크는 MapStruct와 JMapper가 모두 가장 좋은 평균 작업 시간을 가지고 있음을 분명히 보여준다.

### 2) 처리량
이 모드에서 벤치마크는 초당 작업 수를 반환한다. 다음과 같은 결과를 얻었다(결과가 많을수록 좋다).

| Framework Name | Throughput (in operations per ms) |
|:-----:|:-----:|
| MapStruct | 58101 |
| JMapper | 53667 |
| Orika | 1195 |
| ModelMapper | 379 |
| Dozer | 230 |

처리량 모드에서는 테스트된 프레임워크 중 MapStruct가 가장 빨랐고, JMapper가 그 뒤를 이었다.

### 3) SingleShotTime
이 모드에서는 단일 작업의 시작부터 끝까지 소요되는 시간을 측정할 수 있다. 벤치마크 결과는 다음과 같다(작을수록 좋다).

| Framework Name | Single Shot Time (in ms per operation) |
|:-----:|:-----:|
| JMapper | 0.016 |
| MapStruct | 1.904 |
| Dozer | 3.864 |
| Orika | 6.593 |
| ModelMapper | 8.788 |

여기서는 JMapper가 MapStruct보다 더 나은 결과를 반환하는 것을 볼 수 있다.

### 4) SampleTime
이 모드에서는 각 작업의 시간을 샘플링할 수 있다. 세 가지 백분위수에 대한 결과는 다음과 같다.

* Sample Time (in milliseconds per operation) | 

| Framework Name | p0.90 | p0.999 | p1.0 |
|:-----:|:-----:|:-----:|:-----:|
| JMapper | $$10^-4 | 0.001 | 1.526 |
| MapStruct | $$10^-5 | $$10^-4 | 1.948 |
| Orika | 0.001 | 0.018 | 2.327 |
| ModelMapper | 0.002 | 0.044 | 3.604 |
| Dozer | 0.003 | 0.088 | 5.382 |

모든 벤치마크는 시나리오에 따라 MapStruct와 JMapper가 모두 좋은 선택임을 보여주었다.

## 5. 실제 모델 테스트
성능 테스트를 위해 Java Microbenchmark Harness를 사용할 수 있다.

각 변환기 에 대해 BenchmarkMode를 `Mode.All`로 지정하여 별도의 벤치마크를 생성했다.

### 1) 평균 시간
JMH는 평균 실행 시간에 대해 다음과 같은 결과를 반환했습니다(짧을수록 좋음).

| Framework Name | Average running time (in ms per operation) |
|:-----:|:-----:|
| Framework Name | Average running time (in ms per operation) |
| MapStruct | $$10^-4 |
| JMapper | $$10^-4 |
| Orika | 0.007 |
| ModelMapper | 0.137 |
| Dozer | 0.145 |

### 2) 처리량
이 모드에서 벤치마크는 초당 작업 수를 반환한다. 각 매퍼에 대해 다음과 같은 결과를 얻었다(결과가 많을수록 좋다).

| Framework Name | Throughput (in operations per ms) |
|:-----:|:-----:|
| JMapper | 3205 |
| MapStruct | 3467 |
| Orika | 121 |
| ModelMapper | 7 |
| Dozer | 6.342 |

### 3) SingleShotTime
이 모드에서는 단일 작업의 시작부터 끝까지 소요되는 시간을 측정할 수 있습니다. 벤치마크 결과는 다음과 같습니다(짧을수록 좋습니다).

| Framework Name | Single Shot Time (in ms per operation) |
|:-----:|:-----:|
| JMapper | 0.722 |
| MapStruct | 2.111 |
| Dozer | 16.311 |
| ModelMapper | 22.342 |
| Orika | 32.473 |

### 4) SampleTime
이 모드에서는 각 작업의 시간을 샘플링할 수 있다. 샘플링 결과는 백분위수로 나뉘며, 세 가지 백분위수(p0.90, p0.999 , p1.00)에 대한 결과를 제시한다.

* Sample Time (in milliseconds per operation)

| Framework Name | p0.90 | p0.999 | p1.0 |
|:-----:|:-----:|:-----:|:-----:|
| JMapper | $$10^-4 | 0.001 | 1.526 |
| MapStruct | $$10^-4 | $$10^-4 | 1.948 |
| Orika | 0.001 | 0.018 | 2.327 |
| ModelMapper | 0.002 | 0.044 | 3.604 |
| Dozer | 0.003 | 0.088 | 5.382 |

간단한 예제와 실제 예제의 정확한 결과는 분명히 달랐지만, 거의 비슷한 경향을 보인다. 두 예제 모두에서 JMapper와 MapStruct가 1위 자리를 두고 치열한 경쟁을 벌였다.

### 5) 결론
이 섹션에서 수행한 실제 모델 테스트를 바탕으로, JMapper가 가장 우수한 성능을 보였으며, MapStruct가 그 뒤를 바짝 쫓았다. 같은 테스트에서 Dozer는 SingleShotTime을 제외하고 결과 테이블 최하위에 꾸준히 위치하는 것을 확인할 수 있다.

## [출처 및 참고]
* [https://www.baeldung.com/java-performance-mapping-frameworks](https://www.baeldung.com/java-performance-mapping-frameworks)
