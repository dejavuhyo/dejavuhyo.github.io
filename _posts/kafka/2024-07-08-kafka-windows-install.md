---
title: Kafka Windows 설치 및 테스트
author: dejavuhyo
date: 2024-07-08 13:05:00 +0900
categories: [DevOps, Kafka]
tags: [kafka, kafka-windows, kafka-install, 카프카-설치, 카프카-윈도우]
---

## 1. 다운로드
다운받고 파일을 압축을 풀어준다.

[Kafka 다운로드](https://kafka.apache.org/downloads)

![kafka-download](/assets/img/2024-07-08-kafka-windows-install/kafka-download.png)

압축을 풀고 cmd를 이용해 `(kafka 압축 해제 경로)\bin\windows`로 이동한다.

![install-url](/assets/img/2024-07-08-kafka-windows-install/install-url.png)

> Broker는 Kafka의 서버를 의미하며 동일 노드 내에서 여러개의 Broker를 띄울 수 있다. 여러개의 Broker가 띄워져 있으면 이 분산 Message Queue를 Zookeeper가 관리한다. Kafka 서버를 띄우기 전에 Zookeeper를 반드시 띄워야 한다.

## 2. Zookeeper 실행

```shell
zookeeper-server-start.bat ..\..\config\zookeeper.properties
```

![run-zookeeper](/assets/img/2024-07-08-kafka-windows-install/run-zookeeper.png)

## 3. Kafka 서버 실행

```shell
kafka-server-start.bat ..\..\config\server.properties
```

![run-kafka](/assets/img/2024-07-08-kafka-windows-install/run-kafka.png)

## 4. Zookeeper 및 Kafka 실행 확인

```shell
netstat -a
```

![run-check](/assets/img/2024-07-08-kafka-windows-install/run-check.png)

* 2181 포트: Zookeeper

* 9002 포트: Kafka

## 5. Kafka 테스트

### 1) topic 생성 (broker)

```shell
kafka-topics.bat --create --bootstrap-server localhost:9092 --topic kafka-test
```

* 생성한 topic 조회

```shell
kafka-topics.bat --list --bootstrap-server localhost:9092
```

![create-topic](/assets/img/2024-07-08-kafka-windows-install/create-topic.png)

### 2) topic에 message 보내기 (producer)

```shell
kafka-console-producer.bat --broker-list localhost:9092 --topic kafka-test
```

![producer](/assets/img/2024-07-08-kafka-windows-install/producer.png)

### 3) topic에 message 가져오기 (consumer)

```shell
kafka-console-consumer.bat --bootstrap-server localhost:9092 --topic kafka-test --from-beginning
```

![consumer](/assets/img/2024-07-08-kafka-windows-install/consumer.png)

## [출처 및 참고]
* [https://oingdaddy.tistory.com/274](https://oingdaddy.tistory.com/274)
