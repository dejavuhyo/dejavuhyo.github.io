---
title: MQTT 개념 및 특징
author: dejavuhyo
date: 2020-05-26 17:00:00 +0900
categories: [DevOps, MQTT]
tags: [mqtt, mqtt-concept, mqtt-개념, mqtt-특징]
---

## 1. 개념
MQTT(Message Queue Telemetry Transport)란 IoT에서 각광 받고 있는 메세지 전송 프로토콜이다. MQTT는 앤디 스탠포드 클라크 (IBM)와 알렌 니퍼 (EUROTECH)가 1999년에 개발하였다.

![img001](/assets/img/2020-05-26-mqtt-concept/img001.png)

초기 MQTT는 멀티 레벨의 QoS(Quality of Service)를 경량 및 효율적인 대역폭을 사용하기 위해 설계 되었고, 현재는 MQTT의 특성인 메세지 경량화로 인해 IoT 또는 M2M(machine to machine communications) 통신에 많이 사용되고 있다.

2011년, IBM과 EUROTECH는 PAHO라고 제안 된 이클립스 프로젝트에 MQTT를 기증했고, 2013년, OASIS에 제출하여 해당 MQTT는 표준이 되었다.

MQTT 프로토콜은 푸시 기술(push technology)에서 일반적으로 사용되는 클라이언트/서버 방식 대신, 메시지 매개자(broker)를 통해 송신자가 특정 메시지를 발행(publish)하고 수신자가 메시지를 구독(subscribe)하는 방식을 사용한다. 즉, 매개자(broker)를 통해 메시지가 송수신된다.

## 2. 특징

### 1) Broker/Publisher/Subscriber
MQTT 프로토콜은 메시지를 발행(publishing) 하고, 관심 있는 주제를 구독(subscribe) 하는 것을 기본 원칙으로 한다.

![img002](/assets/img/2020-05-26-mqtt-concept/img002.png)

Publisher과 Subscriber은 모두 Broker에 대한 클라이언트로 작동한다. Publisher는 토픽을 발행하기 위한 목적으로 Subscriber은 토픽을 구독하기 위한 목적으로 Broker 서버에 연결한다. 하나 이상의 Pub와 Sub가 브로커에 연결해서 토픽을 발행 하거나 구독할 수 있다. 또한 다수의 클라이언트가 하나의 주제를 구독할 수도 있다.​

### 2) 토픽
Pub와 Sub는 토픽을 기준으로 작동한다. 토픽은 슬래시(/)를 이용해서 계층적으로 구성할 수 있어서 대량의 센서 기기들을 효율적으로 관리 할 수 있다. 예를들어 컴퓨터의 다양한 상태를 측정하는 센서가 있다고 다음과 같이 구성할 수 있을 것이다.

![img003](/assets/img/2020-05-26-mqtt-concept/img003.png)

### 3) 메시지 버스
MQTT는 메시지 버스 시스템이다. MQTT Broker가 메시지 버스를 만들고 여기에 메시지를 흘려보내면, 버스에 붙은 애플리케이션들이 메시지를 읽어가는 방식이다. 메시지 버스에는 다양한 주제의 메시지들이 흐를 수 있는데, 메시지를 구분하기 위해서 "Topic"을 이름으로 하는 메시지 채널을 만든다.

![img004](/assets/img/2020-05-26-mqtt-concept/img004.png)

애플리케이션들은 Message Bus에 연결하고 관심있는 토픽(Topic)을 등록 해서 메시지를 구독(Sub)하거나 발행(Pub)한다.

### 4) QoS
MQTT는 3단계의 QoS(Quality of service)를 제공한다.

* **QoS 0:** 메시지는 한번만 전달하며, 전달여부를 확인하지 않는다. Fire and Forget 타입이다.
* **QoS 1:** 메시지는 반드시 한번 이상 전달된다. 하지만 메시지의 핸드셰이킹 과정을 엄밀하게 추적하지 않기 때문에, 중복전송될 수도 있다.
* **QoS 2:** 메시지는 한번만 전달된다. 메시지의 핸드셰이킹 과정을 추적한다. 높은 품질을 보장하지만 성능의 희생이 따른다.

서비스의 종류에 따라서 적당한 QoS 레벨을 선택해야 한다.

No TCP/IP와 TCP/IP가 섞여있는 로컬 네트워크에서는 QoS 1, 2를 선택하는게 좋을 것 같다. 네트워크 구간을 신뢰할 수 없으며, 메시지 전송이 실패 했을 때, 메시지 박스에 저장하는 일을 하는 소프트웨어 시스템을 구축하기가 쉽지 않기 때문이다.

원격 네트워크에서는 0번이 좋을 것 같다. QoS 1이 적당할 것 같지만, 1이나 2를 선택할 경우 메시지 관리가 힘들어진다. 예를들어 offline 모드에서의 메시징을 지원하기 위해서 메시지 박스 서비스를 제공한다고 가정해 보자. QoS 1로 설정을 하면, 다음 연결에 메시지를 보내기 위해서 자체 queue에 저장을 한다. 그러면 시스템 입장에서는 MQTT queue에 있는 메시지를 읽어야 하는데, 메시지 박스에서 읽어야 하는지를 판단해야 한다.

## 4. MQTT 브로커
MQTT 서버라고 하지 않고 중개인(브로커)라고 하는 이유는 MQTT가 발행인과 구독자가 메시지를 주고 받을 수 있도록 다리를 놔주는 역할만을 하기 때문이다. 다른 기능들은 중계를 도와주는 부가 기능일 뿐이다.

### 1) Mosquitto
모스키토(Mosquitto)는 (2014년 6월 현재)MQTT 프로토콜 3.1을 구현한 BSD 라이센스 기반의 오픈소스 메시지 브로커이다. MQTT 전용 브로커로 매우 가벼우며, MQTT 브로커가 가져야 할 대부분의 기능을 충실히 지원한다는 장점이 있다. 소형기기에 올려서 사용하기에 적당하다. 클러스터를 지원하지 않는다.

![img005](/assets/img/2020-05-26-mqtt-concept/img005.png)

### 2) RabbitMQ
AMQP를 지원하는 메시지 큐 서버로 플러그인(Plug-in)방식으로 MQTT를 지원한다. AMQP를 이용한 범용 메시징 서버라서 좀 무겁기는 하지만 클러스터링을 지원하고 다양한 플러그인을 이용해서 기능을 확장할 수 있다. STOMP, 웹 소켓, HTTP(S)등 다양한 통신 프로토콜을 지원하기 때문에, 메시징 서버 인프라의 개발에 적당하다.

![img006](/assets/img/2020-05-26-mqtt-concept/img006.png)

## [출처 및 참고]
* [https://www.joinc.co.kr/w/man/12/MQTT/Tutorial#toc](https://www.joinc.co.kr/w/man/12/MQTT/Tutorial#toc)
