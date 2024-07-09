---
title: Kafka SASL/PLAIN 설정
author: dejavuhyo
date: 2024-07-09 10:25:00 +0900
categories: [DevOps, Kafka]
tags: [kafka, kafka-sasl-plain, sasl-plain, 카프카, 카프카-인증]
---

## 1. 인증정보 conf 파일 구성

### 1) zookeeper_jaas.conf 생성

* 생성 위치: `../config/zookeeper_jaas.conf`

```text
Server {
    org.apache.kafka.common.security.plain.PlainLoginModule required
    username="admin"
    password="adminpwd"
    user_admin="adminpwd"
    user_kafka="kafkapwd";
};
```

* username, password: Super User 설정

* `user_[username]="[passwords]"`

### 2) kafka_jaas.conf 생성

* 생성 위치: `../config/kafka_jaas.conf`

```text
KafkaServer {
    org.apache.kafka.common.security.plain.PlainLoginModule required
    username="admin"
    password="adminpwd"
    user_admin="adminpwd"
    user_kafka="kafkapwd";
};
Client {
    org.apache.kafka.common.security.plain.PlainLoginModule required
    username="admin"
    password="adminpwd"
    user_admin="adminpwd"
    user_kafka="kafkapwd";
};
```

* KafkaServer: `zookeeper_jaas.conf`와 같이 Super 유저 및 일반 유저 설정

* Client: Zookeeper Server 접속 유저 설정

## 2. server 설정

### 1) zookeeper.properties 편집

* 경로: `../config/zookeeper.properties`

* 추가

```properties
# 모든 클라이언트 SASL 인증 설정
zookeeper.sasl.client=true
 
# 인증되지 않은 사용자 연결 유지, 조작 불가능
authProvider.1=org.apache.zookeeper.server.auth.SASLAuthenticationProvider
 
# 모든 연결 SASL 인증 방식 사용
requireClientAuthScheme=sasl
```

### 2) server.properties 편집

* 경로: `../config/server.properties`

* 추가

```properties
# Kafka Server Super 유저 이름
super.users=User:admin
 
# SASL 인증 방식 설정
security.inter.broker.protocol=SASL_PLAINTEXT

# 브로커간 통신 사용할 SASL 메커니즘, 기본값은 GSSAPI
sasl.mechanism.inter.broker.protocol=PLAIN

# Kafka 서버 활성화 SASL 메커니즘 리스트
sasl.enabled.mechanisms=PLAIN

# ACL 저장시 사용 가능한 Authorizer 구현 제공
authorizer.class.name=kafka.security.authorizer.AclAuthorizer

# Super User를 제외한 User도 해당 리소스 액세스 제어 가능
allow.everyone.if.no.acl.found=true

# Broker 사용 호스트 및 포트 지정
listeners=SASL_PLAINTEXT://localhost:9092
 
# Producer 및 Consumer 접근 호스트 포트 지정
advertised.listeners=SASL_PLAINTEXT://localhost:9092
```

## 3. 인증정보 conf 파일 설정
에디터로 파일을 열고 추가한다.

* Linux: `../bin/zookeeper-server-start.sh`

```text
export KAFKA_OPTS=-Djava.security.auth.login.config=../../config/zookeeper_jaas.conf
```

* Windows: `../bin/windows/zookeeper-server-start.bat`

```text
set KAFKA_OPTS=-Djava.security.auth.login.config=../../config/zookeeper_jaas.conf
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
./zookeeper-server-start.sh ../config/server.properties
```

* Windows

```shell
kafka-server-start.bat ../../config/server.properties
```

* server.log 일부

```text
[2024-07-09 08:35:57,217] INFO KafkaConfig values:
        advertised.listeners = SASL_PLAINTEXT://localhost:9092
        alter.config.policy.class.name = null
        alter.log.dirs.replication.quota.window.num = 11
        alter.log.dirs.replication.quota.window.size.seconds = 1
        authorizer.class.name = kafka.security.authorizer.AclAuthorizer
        auto.create.topics.enable = true
        auto.include.jmx.reporter = true
        auto.leader.rebalance.enable = true
        listener.security.protocol.map = PLAINTEXT:PLAINTEXT,SSL:SSL,SASL_PLAINTEXT:SASL_PLAINTEXT,SASL_SSL:SASL_SSL
        listeners = SASL_PLAINTEXT://localhost:9092
        log.cleaner.backoff.ms = 15000
        log.cleaner.dedupe.buffer.size = 134217728
        log.cleaner.delete.retention.ms = 86400000
        security.inter.broker.protocol = SASL_PLAINTEXT
```

## 5. 계정 권한 부여

### 1) Single

* Linux

```shell
./kafka-acls.sh --authorizer-properties zookeeper.connect=localhost:2181 --add --allow-principal User:kafka --operation All --topic '*' --group '*'
```

* Windows

```shell
kafka-acls.bat --authorizer-properties zookeeper.connect=localhost:2181 --add --allow-principal User:kafka --operation All --topic '*' --group '*'
```

### 2) Cluster

* Linux

```shell
./kafka-acls.sh --authorizer-properties zookeeper.connect=localhost:2181 --add --allow-principal User:kafka --operation All --topic '*' --group '*' --cluster
```

* Windows

```shell
kafka-acls.bat --authorizer-properties zookeeper.connect=localhost:2181 --add --allow-principal User:kafka --operation All --topic '*' --group '*' --cluster
```

* `--allow-principal User:[username]`: 권한 부여할 유저

* `--operation`: producer, consumer, All 권한

* `--topic`: topic name, '*'

* `--group`: group id (producer만 지정할 땐 필요 없음)

## 6. Producer 및 Consumer Console 적용

### 1) producer_jaas.conf 생성

* 생성 위치: `../config/producer_jaas.conf`

```text
bootstrap.servers=SASL_PLAINTEXT://localhost:9092
security.protocol=SASL_PLAINTEXT
sasl.mechanism=PLAIN

sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="kafka" password="kafkapwd";
```

### 2) consumer_jaas.conf 생성

* 생성 위치: `../config/consumer_jaas.conf`

```text
client.id=kafka
group.id=local-group

bootstrap.servers=SASL_PLAINTEXT://localhost:9092
security.protocol=SASL_PLAINTEXT
sasl.mechanism=PLAIN

sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="kafka" password="kafkapwd";
```

### 3) Producer Console 실행

* Linux

```shell
./kafka-console-producer.sh --broker-list localhost:9092 --topic test-topic --producer.config=../config/producer_jaas.conf
```

* Windows

```shell
kafka-console-producer.bat --broker-list localhost:9092 --topic test-topic --producer.config=../../config/producer_jaas.conf
```

### 4) Consumer Console 실행

* Linux

```shell
./kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test-topic --group local-group --consumer.config=../config/consumer_jaas.conf
```

* Windows

```shell
kafka-console-consumer.bat --bootstrap-server localhost:9092 --topic test-topic --group local-group --consumer.config=../../config/consumer_jaas.conf
```

## [출처 및 참고]
* [https://velog.io/@limsubin/Kafka-SASLPALIN-%EC%9D%B8%EC%A6%9D-%EC%84%A4%EC%A0%95%EC%9D%84-%ED%95%B4%EB%B3%B4%EC%9E%90](https://velog.io/@limsubin/Kafka-SASLPALIN-%EC%9D%B8%EC%A6%9D-%EC%84%A4%EC%A0%95%EC%9D%84-%ED%95%B4%EB%B3%B4%EC%9E%90)
* [https://kafka.apache.org/documentation/#security_sasl_plain](https://kafka.apache.org/documentation/#security_sasl_plain)
