---
title: Kafka 대용량 메시지 보내기
author: dejavuhyo
date: 2024-07-10 11:35:00 +0900
categories: [DevOps, Kafka]
tags: [kafka, kafka-message, kafka-large-message, 카프카-메시지, 카프카-대용량-메시지]
---

kafka-send-large-message.md

## 1. 문제
Kafka 구성은 보낼 수 있는 메시지의 크기를 제한한다. 기본적으로 이 제한은 1MB이다. 그러나 큰 메시지를 보내야 하는 요구 사항이 있는 경우 요구 사항에 따라 이러한 구성을 조정해야 한다.

## 2. 설정
단일 브로커를 사용한 기본 Kafka 설정을 사용한다. 또한 프로듀서 애플리케이션은 Kafka Client를 사용하여 정의된 토픽을 통해 Kafka Broker로 메시지를 보낼 수 있다. 또한 단일 파티션 토픽을 사용한다.

![kafka-setup](/assets/img/2024-07-10-kafka-send-large-message/kafka-setup.png)

Kafka Producer, Kafka Broker, Topic, Kafka Consumer와 같은 여러 상호 작용 지점을 관찰할 수 있다. 따라서 이 모든 것은 한 쪽에서 다른 쪽으로 큰 메시지를 보낼 수 있도록 구성 업데이트가 필요하다.

20MB의 대용량 메시지를 보내기 위해 이러한 구성을 확인한다.

## 3. Producer 구성
메시지가 처음 나오는 곳이다. 그리고 Spring Kafka를 사용하여 애플리케이션에서 Kafka 서버로 메시지를 보낸다.

따라서 `max.request.size` 속성을 먼저 업데이트해야 한다. 이 producer 구성에 대한 추가 세부 정보는 [Kafka Documentation](https://kafka.apache.org/documentation/#producerconfigs_max.request.size)에서 확인할 수 있다. 이는 Kafka 클라이언트 라이브러리에서 상수 `ProducerConfig.MAX_REQUEST_SIZE_CONFIG`로 제공되며, Spring Kafka 종속성의 일부로 제공된다.

이 값을 `20971520` 바이트로 구성한다.

```java
public ProducerFactory<String, String> producerFactory() {
    Map<String, Object> configProps = new HashMap<>();
    configProps.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrapAddress);
    configProps.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
    configProps.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
    configProps.put(ProducerConfig.MAX_REQUEST_SIZE_CONFIG, "20971520");

    return new DefaultKafkaProducerFactory<>(configProps);
}
```

## 4. Topic 구성
메시지 생성 애플리케이션은 정의된 토픽에서 Kafka Broker로 메시지를 보낸다. 따라서 다음 요구 사항은 사용된 Kafka 토픽을 구성하는 것이다. 즉, 기본값이 1MB인 `max.message.bytes` 속성을 업데이트해야 한다.

이는 압축 후 Kafka의 가장 큰 레코드 배치 크기 값을 유지한다(압축이 활성화된 경우). 추가 세부 정보는 [Kafka Documentation](https://kafka.apache.org/25/documentation.html#max.message.bytes)에서 확인할 수 있다.

CLI 명령을 사용하여 토픽 생성 시 이 속성을 수동으로 구성한다.

```shell
./kafka-topics.sh --bootstrap-server localhost:9092 --create --topic longMessage --partitions 1 --replication-factor 1 --config max.message.bytes=20971520
```

또는 Kafka 클라이언트를 통해 이 속성을 구성할 수 있다.

```java
public NewTopic topic() {
    NewTopic newTopic = new NewTopic(longMsgTopicName, 1, (short) 1);
    Map<String, String> configs = new HashMap<>();
    configs.put("max.message.bytes", "20971520");
    newTopic.configs(configs);
    return newTopic;
}
```

최소한 다음 두 가지 properties를 구성해야 합니다.

## 5. Broker 구성
선택적 구성 속성인 `message.max.bytes`를 사용하면 broker의 모든 토픽이 크기가 1MB를 넘는 메시지를 허용 할 수 있다.

그리고 이것은 압축 후 Kafka에서 허용하는 가장 큰 레코드 배치 크기의 값을 유지한다(압축이 활성화된 경우). 추가 세부 정보는 [Kafka Documentation](https://kafka.apache.org/25/documentation.html#message.max.bytes)에서 확인할 수 있다.

Kafka Broker의 `server.properties` 구성 파일에 이 속성을 추가한다.

```properties
message.max.bytes=20971520
```

또한, `message.max.bytes`와 `max.message.bytes` 중 최대값이 유효한 값으로 사용된다.

## 6. Consumer 구성
Kafka consumer에게 사용 가능한 구성 설정이다. 이러한 변경 사항은 대용량 메시지를 소비하는데 필수는 아니지만 이를 피하면 consumer 애플리케이션의 성능에 영향을 미칠 수 있다. 따라서 이러한 구성도 적용하는 것이 좋다.

* `max.partition.fetch.bytes`: 이 속성은 소비자가 토픽의 파티션에서 페치 할 수 있는 바이트 수를 제한한다. 추가 세부 정보는 [Kafka Documentation](https://kafka.apache.org/documentation/#consumerconfigs_max.partition.fetch.bytes)에서 확인할 수 있다. Kafka 클라이언트 라이브러리에서 `ConsumerConfig.MAX_PARTITION_FETCH_BYTES_CONFIG`라는 상수로 제공된다.

* `fetch.max.bytes`: 이 속성은 소비자가 Kafka 서버 자체에서 페치 할 수 있는 바이트 수를 제한한다. Kafka 소비자는 여러 파티션에서도 수신할 수 있다. 추가 세부 정보는 [Kafka Documentation](https://kafka.apache.org/documentation/#consumerconfigs_fetch.max.bytes)에서 확인할 수 있다. 이는 Kafka 클라이언트 라이브러리에서 상수 `ConsumerConfig.FETCH_MAX_BYTES_CONFIG`로 제공된다.

따라서 consumer를 구성하려면 KafkaConsumerFactory를 만들어야 한다. 항상 `Topic/Broker` 구성 보다 더 높은 값을 사용해야 한다.

```java
public ConsumerFactory<String, String> consumerFactory(String groupId) {
    Map<String, Object> props = new HashMap<>();
    props.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrapAddress);
    props.put(ConsumerConfig.GROUP_ID_CONFIG, groupId);
    props.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
    props.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
    props.put(ConsumerConfig.MAX_PARTITION_FETCH_BYTES_CONFIG, "20971520");
    props.put(ConsumerConfig.FETCH_MAX_BYTES_CONFIG, "20971520");
    return new DefaultKafkaConsumerFactory<>(props);
}
```

여기서는 단일 파티션 Topic을 사용하고 있기 때문에 두 속성에 대해 동일한 config 값인 `20971520` 바이트를 사용했다. 그러나 `FETCH_MAX_BYTES_CONFIG`의 값은 `MAX_PARTITION_FETCH_BYTES_CONFIG`보다 높아야 한다. 여러 파티션에서 수신하는 소비자가 있는 경우 `FETCH_MAX_BYTES_CONFIG`는 여러 파티션에서 페치할 수 있는 메시지 크기를 나타낸다. 반면 config `MAX_PARTITION_FETCH_BYTES_CONFIG`는 단일 파티션에서 페치할 수 있는 메시지 크기를 나타낸다.

## 7. 대안
Kafka producer, Topic, Broker, Kafka consumer의 다양한 구성을 업데이트하여 대용량 메시지를 보내는 방법을 확인하였다. 그러나 일반적으로 Kafka를 사용하여 대용량 메시지를 보내는 것은 피해야 한다. 대용량 메시지를 처리하면 프로듀서와 소비자의 CPU와 메모리가 더 많이 소모된다. 따라서 궁극적으로 다른 작업에 대한 처리 능력이 다소 제한된다. 또한 이로 인해 최종 사용자에게 눈에 띄게 높은 지연 시간이 발생할 수 있다.


다른 가능한 옵션이다.

* Kafka producer는 메시지를 압축하는 기능을 제공한다. 또한, `compression.type` 속성을 사용하여 구성할 수 있는 다양한 압축 유형을 지원한다.

* 공유 저장 위치에 있는 파일에 큰 메시지를 저장하고 Kafka 메시지를 통해 위치를 보낼 수 있다. 이것은 더 빠른 옵션이 될 수 있으며 최소한의 처리 오버헤드가 있다.

* 또 다른 옵션은 producer 측에서 큰 메시지를 각각 1KB 크기의 작은 메시지로 분할하는 것이다. 그런 다음 파티션 키를 사용하여 모든 메시지를 단일 파티션으로 보내 올바른 순서를 보장할 수 있다. 따라서 나중에 소비자 측에서 작은 메시지에서 큰 메시지를 재구성할 수 있다.

위의 옵션 중 어느 것도 요구 사항에 맞지 않으면 앞에서 구성한 구성을 선택할 수 있다.

## [출처 및 참고]
* [https://www.baeldung.com/java-kafka-send-large-message](https://www.baeldung.com/java-kafka-send-large-message)
