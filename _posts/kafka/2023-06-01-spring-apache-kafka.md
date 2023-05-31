---
title: Spring Apache Kafka
author: dejavuhyo
date: 2023-06-01 08:50:00 +0900
categories: [Framework, Spring]
tags: [spring-apache-kafka, apache-kafka, spring-kafka, kafka, 스프링-아파치-카프카, 스프링-카프카, 아파치-카프카, 카프카]
---

## 1. 설치 및 설정
Kafka를 다운로드하고 설치하려면 [공식 가이드](https://kafka.apache.org/quickstart)를 참조한다.

`pom.xml`에 spring-kafka 종속성을 추가한다.

```xml
<dependency>
    <groupId>org.springframework.kafka</groupId>
    <artifactId>spring-kafka</artifactId>
    <version>3.0.0</version>
</dependency>
```

`spring-boot-maven-plugin`을 구성한다.

```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <configuration>
        <mainClass>com.baeldung.spring.kafka.KafkaApplication</mainClass>
    </configuration>
</plugin>
```

## 2. Topics 구성
이전에는 명령줄 도구를 실행하여 Kafka에서 토픽을 생성했다.

```shell
$ bin/kafka-topics.sh --create \
  --zookeeper localhost:2181 \
  --replication-factor 1 --partitions 1 \
  --topic mytopic
```

그러나 Kafka에 AdminClient가 도입되면서 이제 프로그래밍 방식으로 토픽을 생성할 수 있다.

NewTopic 유형의 모든 빈에 대한 주제를 자동으로 추가하는 KafkaAdmin Spring 빈을 추가해야 한다.

```java
@Configuration
public class KafkaTopicConfig {
    
    @Value(value = "${spring.kafka.bootstrap-servers}")
    private String bootstrapAddress;

    @Bean
    public KafkaAdmin kafkaAdmin() {
        Map<String, Object> configs = new HashMap<>();
        configs.put(AdminClientConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrapAddress);
        return new KafkaAdmin(configs);
    }
    
    @Bean
    public NewTopic topic1() {
         return new NewTopic("baeldung", 1, (short) 1);
    }
}
```

## 3. 메시지 생성
메시지를 생성하려면 먼저 ProducerFactory를 구성해야 한다. 이는 Kafka Producer 인스턴스를 만들기 위한 전략을 설정한다.

그런 다음 Producer 인스턴스를 래핑 하고 Kafka 주제에 메시지를 보내기 위한 편리한 메서드를 제공하는 KafkaTemplate이 필요하다.

생산자 인스턴스는 스레드로부터 안전하다. 따라서 애플리케이션 컨텍스트 전체에서 단일 인스턴스를 사용하면 더 높은 성능을 얻을 수 있다. 따라서 KakfaTemplate 인스턴스도 스레드로부터 안전하며 하나의 인스턴스를 사용하는 것이 좋다.

### 1) 생산자 구성

```java
@Configuration
public class KafkaProducerConfig {

    @Bean
    public ProducerFactory<String, String> producerFactory() {
        Map<String, Object> configProps = new HashMap<>();
        configProps.put(
          ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, 
          bootstrapAddress);
        configProps.put(
          ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, 
          StringSerializer.class);
        configProps.put(
          ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, 
          StringSerializer.class);
        return new DefaultKafkaProducerFactory<>(configProps);
    }

    @Bean
    public KafkaTemplate<String, String> kafkaTemplate() {
        return new KafkaTemplate<>(producerFactory());
    }
}
```

### 2) 메시지 게시
KafkaTemplate 클래스를 사용하여 메시지를 보낼 수 있다.

```java
@Autowired
private KafkaTemplate<String, String> kafkaTemplate;

public void sendMessage(String msg) {
    kafkaTemplate.send(topicName, msg);
}
```

송신 API는 CompletableFuture 개체를 반환한다. 보내는 스레드를 차단하고 보낸 메시지에 대한 결과를 얻으려면 CompletableFuture 개체의 get API를 호출할 수 있다. 스레드는 결과를 기다리지만 생산자의 속도가 느려진다.

Kafka는 빠른 스트림 처리 플랫폼이다. 따라서 후속 메시지가 이전 메시지의 결과를 기다리지 않도록 결과를 비동기적으로 처리하는 것이 좋다.

콜백을 통해 이를 수행할 수 있다.

```java
public void sendMessage(String message) {
     CompletableFuture<SendResult<String, String>> future = kafkaTemplate.send(topicName, message);
     future.whenComplete((result, ex) -> {
         if (ex == null) {
             System.out.println("Sent message=[" + message + 
                 "] with offset=[" + result.getRecordMetadata().offset() + "]");
         } else {
             System.out.println("Unable to send message=[" + 
                 message + "] due to : " + ex.getMessage());
         }
     });
}
```

## 4. 메시지 소비

### 1) 소비자 구성
메시지 소비를 위해 ConsumerFactory 및 KafkaListenerContainerFactory를 구성해야 한다. Spring 빈 팩토리에서 이러한 빈을 사용할 수 있게 되면 `@KafkaListener` 주석을 사용하여 POJO 기반 소비자를 구성할 수 있다.

스프링 관리 Bean에서 `@KafkaListener` 주석을 감지하려면 구성 클래스에 `@EnableKafka` 주석이 필요하다.

```java
@EnableKafka
@Configuration
public class KafkaConsumerConfig {

    @Bean
    public ConsumerFactory<String, String> consumerFactory() {
        Map<String, Object> props = new HashMap<>();
        props.put(
          ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, 
          bootstrapAddress);
        props.put(
          ConsumerConfig.GROUP_ID_CONFIG, 
          groupId);
        props.put(
          ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, 
          StringDeserializer.class);
        props.put(
          ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, 
          StringDeserializer.class);
        return new DefaultKafkaConsumerFactory<>(props);
    }

    @Bean
    public ConcurrentKafkaListenerContainerFactory<String, String> 
      kafkaListenerContainerFactory() {
   
        ConcurrentKafkaListenerContainerFactory<String, String> factory =
          new ConcurrentKafkaListenerContainerFactory<>();
        factory.setConsumerFactory(consumerFactory());
        return factory;
    }
}
```

### 2) 메시지 소비

```java
@KafkaListener(topics = "topicName", groupId = "foo")
public void listenGroupFoo(String message) {
    System.out.println("Received Message in group foo: " + message);
}
```

각각 다른 그룹 ID를 가진 주제에 대해 여러 수신기를 구현할 수 있다. 또한 한 소비자는 다양한 주제의 메시지를 들을 수 있다.

```java
@KafkaListener(topics = "topic1, topic2", groupId = "foo")
```

Spring은 또한 리스너에서 `@Header` 주석을 사용하여 하나 이상의 메시지 헤더 검색을 지원한다.

```java
@KafkaListener(topics = "topicName")
public void listenWithHeaders(
  @Payload String message, 
  @Header(KafkaHeaders.RECEIVED_PARTITION_ID) int partition) {
      System.out.println(
        "Received Message: " + message"
        + "from partition: " + partition);
}
```

### 3) 특정 파티션에서 메시지 사용
단 하나의 파티션으로 주제 baeldung을 생성했다.

그러나 여러 파티션이 있는 주제의 경우 `@KafkaListener`는 초기 오프셋을 사용하여 주제의 특정 파티션을 명시적으로 구독할 수 있다.

```java
@KafkaListener(
  topicPartitions = @TopicPartition(topic = "topicName",
  partitionOffsets = {
    @PartitionOffset(partition = "0", initialOffset = "0"), 
    @PartitionOffset(partition = "3", initialOffset = "0")}),
  containerFactory = "partitionsKafkaListenerContainerFactory")
public void listenToPartition(
  @Payload String message, 
  @Header(KafkaHeaders.RECEIVED_PARTITION_ID) int partition) {
      System.out.println(
        "Received Message: " + message"
        + "from partition: " + partition);
}
```

이 리스너에서 `initialOffset`이 0으로 설정되었으므로 파티션 0과 3에서 이전에 소비된 모든 메시지는 이 리스너가 초기화될 때마다 다시 소비된다.

오프셋을 설정할 필요가 없으면 `@TopicPartition` 주석의 partitions 속성을 사용하여 오프셋 없이 파티션만 설정할 수 있다.

```java
@KafkaListener(topicPartitions 
  = @TopicPartition(topic = "topicName", partitions = { "0", "1" }))
```

### 4) 리스너에 대한 메시지 필터 추가
사용자 지정 필터를 추가하여 특정 메시지 콘텐츠를 사용하도록 수신기를 구성할 수 있다. 이는 RecordFilterStrategy를 KafkaListenerContainerFactory로 설정하여 수행할 수 있다 

```java
@Bean
public ConcurrentKafkaListenerContainerFactory<String, String>
  filterKafkaListenerContainerFactory() {

    ConcurrentKafkaListenerContainerFactory<String, String> factory =
      new ConcurrentKafkaListenerContainerFactory<>();
    factory.setConsumerFactory(consumerFactory());
    factory.setRecordFilterStrategy(
      record -> record.value().contains("World"));
    return factory;
}
```

그런 다음 이 컨테이너 팩토리를 사용하도록 리스너를 구성할 수 있다.

```java
@KafkaListener(
  topics = "topicName", 
  containerFactory = "filterKafkaListenerContainerFactory")
public void listenWithFilter(String message) {
    System.out.println("Received Message in filtered listener: " + message);
}
```

이 리스너에서는 필터와 일치하는 모든 메시지가 삭제된다.

## 5. 사용자 지정 메시지 변환기
사용자 지정 Java 개체를 보내고 받을 수도 있다. 이렇게 하려면 ProducerFactory에 적절한 직렬 변환기를 구성 하고 ConsumerFactory에 역직렬 변환기를 구성해야 한다.

메시지로 보낼 간단한 빈 클래스 이다.

```java
public class Greeting {

    private String msg;
    private String name;

    // standard getters, setters and constructor
}
```

### 1) 맞춤 메시지 생성
예제에서는 JsonSerializer를 사용한다.

ProducerFactory 및 KafkaTemplate에 대한 코드이다.

```java
@Bean
public ProducerFactory<String, Greeting> greetingProducerFactory() {
    // ...
    configProps.put(
      ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, 
      JsonSerializer.class);
    return new DefaultKafkaProducerFactory<>(configProps);
}

@Bean
public KafkaTemplate<String, Greeting> greetingKafkaTemplate() {
    return new KafkaTemplate<>(greetingProducerFactory());
}
```

새로운 KafkaTemplate을 사용하여 인사말 메시지를 보낼 수 있다.

```java
kafkaTemplate.send(topicName, new Greeting("Hello", "World"));
```

### 2) 사용자 지정 메시지 사용
ConsumerFactory 및 KafkaListenerContainerFactory를 수정하여 Greeting 메시지를 올바르게 역직렬화한다.

```java
@Bean
public ConsumerFactory<String, Greeting> greetingConsumerFactory() {
    // ...
    return new DefaultKafkaConsumerFactory<>(
      props,
      new StringDeserializer(), 
      new JsonDeserializer<>(Greeting.class));
}

@Bean
public ConcurrentKafkaListenerContainerFactory<String, Greeting> 
  greetingKafkaListenerContainerFactory() {

    ConcurrentKafkaListenerContainerFactory<String, Greeting> factory =
      new ConcurrentKafkaListenerContainerFactory<>();
    factory.setConsumerFactory(greetingConsumerFactory());
    return factory;
}
```

spring-kafka JSON 직렬 변환기 및 역직렬 변환기는 Spring-kafka 프로젝트의 선택적 Maven 종속성이기도 한 Jackson 라이브러리를 사용한다.

`pom.xml`에 추가한다.

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.9.7</version>
</dependency>
```

최신 버전의 Jackson을 사용하는 대신 spring-kafka의 `pom.xml`에 추가된 버전을 사용하는 것이 좋다.

마지막으로 인사말 메시지를 사용할 리스너를 작성한다.

```java
@KafkaListener(
  topics = "topicName", 
  containerFactory = "greetingKafkaListenerContainerFactory")
public void greetingListener(Greeting greeting) {
    // process greeting message
}
```

## 6. 다중 메서드 리스너
이제 다양한 종류의 개체를 동일한 주제로 보낸 다음 소비하도록 응용 프로그램을 구성하는 방법이다.

먼저 새 클래스인 Farewell을 추가한다.

```java
public class Farewell {

    private String message;
    private Integer remainingMinutes;

    // standard getters, setters and constructor
}
```

reeting 및 Farewell 개체를 동일한 주제로 보낼 수 있으려면 몇 가지 추가 구성이 필요하다.

### 1) 생산자에서 매핑 유형 설정
생산자에서 JSON 유형 매핑을 구성해야 합니다.

```java
configProps.put(JsonSerializer.TYPE_MAPPINGS, "greeting:com.baeldung.spring.kafka.Greeting, farewell:com.baeldung.spring.kafka.Farewell");
```

이런 식으로 라이브러리는 해당 클래스 이름으로 유형 헤더를 채운다.

결과적으로 ProducerFactory 및 KafkaTemplate은 다음과 같다.

```java
@Bean
public ProducerFactory<String, Object> multiTypeProducerFactory() {
    Map<String, Object> configProps = new HashMap<>();
    configProps.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrapAddress);
    configProps.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
    configProps.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, JsonSerializer.class);
    configProps.put(JsonSerializer.TYPE_MAPPINGS, 
      "greeting:com.baeldung.spring.kafka.Greeting, farewell:com.baeldung.spring.kafka.Farewell");
    return new DefaultKafkaProducerFactory<>(configProps);
}

@Bean
public KafkaTemplate<String, Object> multiTypeKafkaTemplate() {
    return new KafkaTemplate<>(multiTypeProducerFactory());
}
```

이 KafkaTemplate을 사용하여 Greeting, Farewell 또는 모든 객체를 주제로 보낼 수 있다.

```java
multiTypeKafkaTemplate.send(multiTypeTopicName, new Greeting("Greetings", "World!"));
multiTypeKafkaTemplate.send(multiTypeTopicName, new Farewell("Farewell", 25));
multiTypeKafkaTemplate.send(multiTypeTopicName, "Simple string message");
```

### 2) 소비자에서 사용자 정의 MessageConverter 사용
들어오는 메시지를 역직렬화할 수 있으려면 소비자에게 사용자 정의 MessageConverter를 제공해야 한다.

뒤에서 MessageConverter는 Jackson2JavaTypeMapper에 의존한다. 기본적으로 매퍼는 수신된 객체의 유형을 유추한다. 역직렬화 대상 클래스를 결정하기 위해 유형 헤더를 사용하도록 명시적으로 알려야 한다.

```java
typeMapper.setTypePrecedence(Jackson2JavaTypeMapper.TypePrecedence.TYPE_ID);
```

역방향 매핑 정보도 제공해야 한다. 유형 헤더에서 "greeting"을 찾으면 Greeting 개체를 식별하는 반면 "farewell"은 Farewell 개체에 해당한다.

```java
Map<String, Class<?>> mappings = new HashMap<>(); 
mappings.put("greeting", Greeting.class);
mappings.put("farewell", Farewell.class);
typeMapper.setIdClassMapping(mappings);
```

마지막으로 매퍼가 신뢰하는 패키지를 구성해야 한다. 대상 클래스의 위치가 포함되어 있는지 확인해야 한다.

```java
typeMapper.addTrustedPackages("com.baeldung.spring.kafka");
```

결과적으로 이 MessageConverter의 최종 정의는 다음과 같다.

```java
@Bean
public RecordMessageConverter multiTypeConverter() {
    StringJsonMessageConverter converter = new StringJsonMessageConverter();
    DefaultJackson2JavaTypeMapper typeMapper = new DefaultJackson2JavaTypeMapper();
    typeMapper.setTypePrecedence(Jackson2JavaTypeMapper.TypePrecedence.TYPE_ID);
    typeMapper.addTrustedPackages("com.baeldung.spring.kafka");
    Map<String, Class<?>> mappings = new HashMap<>();
    mappings.put("greeting", Greeting.class);
    mappings.put("farewell", Farewell.class);
    typeMapper.setIdClassMapping(mappings);
    converter.setTypeMapper(typeMapper);
    return converter;
}
```

이제 ConcurrentKafkaListenerContainerFactory에게 MessageConverter와 다소 기본적인 ConsumerFactory를 사용하도록 지시해야 한다.

```java
@Bean
public ConsumerFactory<String, Object> multiTypeConsumerFactory() {
    HashMap<String, Object> props = new HashMap<>();
    props.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrapAddress);
    props.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
    props.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, JsonDeserializer.class);
    return new DefaultKafkaConsumerFactory<>(props);
}

@Bean
public ConcurrentKafkaListenerContainerFactory<String, Object> multiTypeKafkaListenerContainerFactory() {
    ConcurrentKafkaListenerContainerFactory<String, Object> factory = new ConcurrentKafkaListenerContainerFactory<>();
    factory.setConsumerFactory(multiTypeConsumerFactory());
    factory.setMessageConverter(multiTypeConverter());
    return factory;
}
```

### 3) 리스너에서 @KafkaHandler 사용
마지막으로 KafkaListener에서 가능한 모든 개체를 검색하는 처리기 메서드를 만든다. 각 처리기는 `@KafkaHandler`로 주석을 달아야 한다.

마지막으로 Greeting 또는 Farewell 클래스 중 하나에 바인딩할 수 없는 개체에 대한 기본 처리기를 정의할 수도 있다.

```java
@Component
@KafkaListener(id = "multiGroup", topics = "multitype")
public class MultiTypeKafkaListener {

    @KafkaHandler
    public void handleGreeting(Greeting greeting) {
        System.out.println("Greeting received: " + greeting);
    }

    @KafkaHandler
    public void handleF(Farewell farewell) {
        System.out.println("Farewell received: " + farewell);
    }

    @KafkaHandler(isDefault = true)
    public void unknown(Object object) {
        System.out.println("Unkown type received: " + object);
    }
}
```

## [출처 및 참고]
* [https://www.baeldung.com/spring-kafka](https://www.baeldung.com/spring-kafka)
