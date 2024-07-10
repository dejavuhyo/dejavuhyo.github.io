---
title: Kafka SASL/PLAIN 설정
author: dejavuhyo
date: 2024-07-09 10:25:00 +0900
categories: [DevOps, Kafka]
tags: [kafka, kafka-sasl-plain, sasl-plain, 카프카, 카프카-인증]
---

## 1. 인증정보 conf 파일 구성

### 1) kafka_server_jaas.conf 생성
두 사용자(admin 및 alice)를 정의한다.

* 생성 위치: `../config/kafka_server_jaas.conf`

```text
KafkaServer {
    org.apache.kafka.common.security.plain.PlainLoginModule required
    username="admin"
    password="admin-secret"
    user_admin="admin-secret"
    user_alice="alice-secret";
};
```

* username, password: Super 유저 설정

* user_[userName]="[passwords]"

## 2. Server 설정

### 1) server.properties 편집

* 경로: `../config/server.properties`

* 추가

```properties
listeners=SASL_PLAINTEXT://localhost:9092
security.inter.broker.protocol=SASL_PLAINTEXT
sasl.mechanism.inter.broker.protocol=PLAIN
sasl.enabled.mechanisms=PLAIN
```

## 3. Server 인증정보 conf 파일 설정
에디터로 파일을 열고 JAAS 구성 파일 위치를 JVM 매개변수로 설정을 한다.

### 1) kafka-server-start 설정

* Linux: `../bin/kafka-server-start.sh`

```text
export KAFKA_OPTS=-Djava.security.auth.login.config=../../config/kafka_server_jaas.conf
```

* Windows: `../bin/windows/kafka-server-start.bat`

```text
set KAFKA_OPTS=-Djava.security.auth.login.config=../../config/kafka_server_jaas.conf
```

## 4. Zookeeper 및 Kafka 실행

### 1) Zookeeper 실행

* Linux

```shell
./zookeeper-server-start.sh ../config/zookeeper.properties
```

* Windows

```shell
zookeeper-server-start.bat ../../config/zookeeper.properties
```

### 2) Kafka 실행

* Linux

```shell
./kafka-server-start.sh ../config/server.properties
```

* Windows

```shell
kafka-server-start.bat ../../config/server.properties
```

## 6. Producer 인증정보 conf 파일 설정

### 1) producer_jaas.conf 생성

* 생성 위치: `../config/producer_jaas.conf`

```text
security.protocol=SASL_PLAINTEXT
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="alice" password="alice-secret";
```

## 7. Consumer 인증정보 conf 파일 설정

### 1) consumer_jaas.conf 생성

* 생성 위치: `../config/consumer_jaas.conf`

```text
security.protocol=SASL_PLAINTEXT
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="alice" password="alice-secret";
```

## 8. Producer 및 Consumer 실행

### 1) Producer Console 실행

* Linux

```shell
./kafka-console-producer.sh --broker-list localhost:9092 --topic topic-test --producer.config=../config/producer_jaas.conf
```

* Windows

```shell
kafka-console-producer.bat --broker-list localhost:9092 --topic topic-test --producer.config=../../config/producer_jaas.conf
```

### 2) Consumer Console 실행

* Linux

```shell
./kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic topic-test --group local-group --consumer.config=../config/consumer_jaas.conf
```

* Windows

```shell
kafka-console-consumer.bat --bootstrap-server localhost:9092 --topic topic-test --group local-group --consumer.config=../../config/consumer_jaas.conf
```

## [출처 및 참고]
* [https://devidea.tistory.com/62](https://devidea.tistory.com/62)
* [https://kafka.apache.org/documentation/#security_sasl_plain](https://kafka.apache.org/documentation/#security_sasl_plain)
* [https://velog.io/@limsubin/Kafka-SASLPALIN-%EC%9D%B8%EC%A6%9D-%EC%84%A4%EC%A0%95%EC%9D%84-%ED%95%B4%EB%B3%B4%EC%9E%90](https://velog.io/@limsubin/Kafka-SASLPALIN-%EC%9D%B8%EC%A6%9D-%EC%84%A4%EC%A0%95%EC%9D%84-%ED%95%B4%EB%B3%B4%EC%9E%90)
