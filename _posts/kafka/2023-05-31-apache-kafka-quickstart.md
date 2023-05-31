---
title: Apache Kafka Quickstart
author: dejavuhyo
date: 2023-05-31 09:20:00 +0900
categories: [DevOps, Kafka]
tags: [apache-kafka, kafka, kafka-quickstart, 아파치-카프카, 카프카, 카프카-빠른-시작]
---

## 1. 카프카 가져오기
최신 Kafka 릴리스를 다운로드하고 압축을 푼다.

```shell
$ tar -xzf kafka_2.13-3.4.0.tgz
$ cd kafka_2.13-3.4.0
```

## 2. Kafka 환경 시작

> 참고: 로컬 환경에 Java 8+가 설치되어 있어야 한다.

Apache Kafka는 ZooKeeper 또는 KRaft를 사용하여 시작할 수 있다. 두 구성 중 하나를 시작하려면 아래 중 하나를 따르되 둘 다 따르면 안된다.

### 1) ZooKeeper 사용
모든 서비스를 올바른 순서로 시작하려면 다음 명령을 실행한다.

```shell
# Start the ZooKeeper service
$ bin/zookeeper-server-start.sh config/zookeeper.properties
```

다른 터미널 세션을 열고 다음을 실행한다.

```shell
# Start the Kafka broker service
$ bin/kafka-server-start.sh config/server.properties
```

모든 서비스가 성공적으로 시작되면 기본 Kafka 환경이 실행되고 사용할 준비가 된 것이다.

### 2) KRaft 사용

클러스터 UUID 생성

```shell
$ KAFKA_CLUSTER_ID="$(bin/kafka-storage.sh random-uuid)"
```

로그 디렉토리 포맷

```shell
$ bin/kafka-storage.sh format -t $KAFKA_CLUSTER_ID -c config/kraft/server.properties
```

카프카 서버 시작

```shell
$ bin/kafka-server-start.sh config/kraft/server.properties
```

Kafka 서버가 성공적으로 시작되면 기본 Kafka 환경이 실행되고 사용할 준비가 된 것이다.

## 3. 이벤트를 저장할 주제 만들기
[Kafka는 여러 시스템에서 이벤트](https://kafka.apache.org/documentation/#messages) (문서에서 레코드 또는 메시지 라고도 함)를 읽고, 쓰고, 저장하고, 처리할 수 있는 분산 이벤트 스트리밍 플랫폼 이다.

이벤트의 예로는 결제 거래, 휴대폰의 지리적 위치 업데이트, 배송 주문, IoT 장치 또는 의료 장비의 센서 측정 등이 있다. 이러한 이벤트는 주제에 구성되고 저장된다. 매우 단순화된 토픽은 파일 시스템의 폴더와 유사하며 이벤트는 해당 폴더의 파일이다.

따라서 첫 번째 이벤트를 작성하기 전에 주제를 만들어야 한다. 다른 터미널 세션을 열고 다음을 실행한다.

```shell
$ bin/kafka-topics.sh --create --topic quickstart-events --bootstrap-server localhost:9092
```

Kafka의 모든 명령줄 도구에는 추가 옵션이 있다. `kafka-topics.sh` 사용 정보를 표시하려면 인수 없이 명령을 실행한다. 예를 들어 새 항목의 [파티션 수와 같은 세부 정보](https://kafka.apache.org/documentation/#intro_concepts_and_terms)도 표시할 수 있다.

```shell
$ bin/kafka-topics.sh --describe --topic quickstart-events --bootstrap-server localhost:9092
Topic: quickstart-events        TopicId: NPmZHyhbR9y00wMglMH2sg PartitionCount: 1       ReplicationFactor: 1	Configs:
    Topic: quickstart-events Partition: 0    Leader: 0   Replicas: 0 Isr: 0
```

## 4. 주제에 몇 가지 이벤트 쓰기
Kafka 클라이언트는 이벤트 쓰기(또는 읽기)를 위해 네트워크를 통해 Kafka 브로커와 통신한다. 일단 수신하면 브로커는 필요한 기간 동안(심지어는 영원히) 지속적이고 내결함성 있는 방식으로 이벤트를 저장한다.

콘솔 생산자 클라이언트를 실행하여 주제에 몇 가지 이벤트를 쓴다. 기본적으로 입력하는 줄마다 주제에 별도의 이벤트가 기록된다.

```shell
$ bin/kafka-console-producer.sh --topic quickstart-events --bootstrap-server localhost:9092
This is my first event
This is my second event
```

언제든지 `Ctrl-C`로 생산자 클라이언트를 중지할 수 있다.

## 5. 이벤트 읽기
다른 터미널 세션을 열고 콘솔 소비자 클라이언트를 실행하여 방금 만든 이벤트를 읽는다.

```shell
$ bin/kafka-console-consumer.sh --topic quickstart-events --from-beginning --bootstrap-server localhost:9092
This is my first event
This is my second event
```

언제든지 `Ctrl-C`를 사용하여 소비자 클라이언트를 중지할 수 있다.

예를 들어 생산자 터미널로 다시 전환하여(이전 단계) 추가 이벤트를 작성하고 이벤트가 소비자 터미널에 즉시 표시되는 방식을 확인한다.

이벤트는 Kafka에 영구적으로 저장되기 때문에 원하는 만큼 많은 소비자가 이벤트를 읽을 수 있다. 다른 터미널 세션을 열고 이전 명령을 다시 실행하여 이를 쉽게 확인할 수 있다.

## 6. Kafka Connect를 사용하여 이벤트 스트림으로 데이터 가져오기/내보내기
이미 이러한 시스템을 사용하는 많은 응용 프로그램과 함께 관계형 데이터베이스 또는 기존 메시징 시스템과 같은 기존 시스템에 많은 데이터가 있을 수 있다. Kafka Connect를 사용 하면 외부 시스템에서 Kafka로 또는 그 반대로 데이터를 지속적으로 수집할 수 있다. 외부 시스템과 상호 작용하기 위한 사용자 지정 논리를 구현하는 커넥터를 실행하는 확장 가능한 도구이다. 따라서 기존 시스템을 Kafka와 통합하는 것이 매우 쉽다. 이 프로세스를 더욱 쉽게 하기 위해 수백 개의 커넥터를 즉시 사용할 수 있다.

이 빠른 시작에서는 파일에서 Kafka 주제로 데이터를 가져오고 Kafka 주제에서 파일로 데이터를 내보내는 간단한 커넥터로 Kafka Connect를 실행하는 방법을 살펴본다.

먼저 Connect 작업자 구성에서 속성을 `connect-file-3.4.0.jar` 추가 해야 한다. `plugin.path`이 빠른 시작의 목적을 위해 상대 경로를 사용하고 빠른 시작 명령이 설치 디렉터리에서 실행될 때 작동하는 uber jar로 커넥터의 패키지를 고려한다. 그러나 프로덕션 배포의 경우 절대 경로를 사용하는 것이 항상 바람직하다. 이 구성을 설정하는 방법에 대한 자세한 설명은 [plugin.path](https://kafka.apache.org/documentation/#connectconfigs_plugin.path)를 참조한다.

`config/connect-standalone.properties` 파일을 편집하고 다음과 일치하는 `plugin.path` 구성 속성을 추가 또는 변경한 후 파일을 저장한다.

```shell
> echo "plugin.path=libs/connect-file-3.4.0.jar"
```

그런 다음 테스트할 일부 시드 데이터를 생성하여 시작한다.

```shell
> echo -e "foo\nbar" > test.txt
```

Windows에서 실행

```shell
> echo foo> test.txt
> echo bar>> test.txt
```

다음으로 독립 실행형 모드에서 실행되는 두 개의 커넥터를 시작한다. 즉, 단일 로컬 전용 프로세스에서 실행된다. 세 가지 구성 파일을 매개변수로 제공한다. 첫 번째는 항상 Kafka Connect 프로세스에 대한 구성으로, 연결할 Kafka 브로커와 데이터의 직렬화 형식과 같은 공통 구성을 포함한다. 나머지 구성 파일은 각각 만들 커넥터를 지정한다. 이러한 파일에는 고유한 커넥터 이름, 인스턴스화할 커넥터 클래스 및 커넥터에 필요한 기타 구성이 포함된다.

```shell
> bin/connect-standalone.sh config/connect-standalone.properties config/connect-file-source.properties config/connect-file-sink.properties
```

Kafka에 포함된 이 샘플 구성 파일은 이전에 시작한 기본 로컬 클러스터 구성을 사용하고 두 개의 커넥터를 생성한다. 첫 번째는 입력 파일에서 라인을 읽고 각각을 Kafka 주제에 생성하는 소스 커넥터이고 두 번째는 싱크 커넥터이다. Kafka 주제에서 메시지를 읽고 각각을 출력 파일의 한 줄로 생성한다.

시작하는 동안 커넥터가 인스턴스화되고 있음을 나타내는 몇 가지 로그 메시지를 볼 수 있다. Kafka Connect 프로세스가 시작되면 소스 커넥터는 `test.txt`에서 줄을 읽기 시작하여 `connect-test` 항목으로 생성해야 하며, 싱크 커넥터는 `connect-test` 항목의 메시지를 읽기 시작하여 `test.sink.txt` 파일에 기록해야 한다. 출력 파일의 내용을 검사하여 데이터가 전체 파이프라인을 통해 전달되었는지 확인할 수 있다.

```shell
> more test.sink.txt
foo
bar
```

데이터는 Kafka 주제에 저장되므로 `connect-test` 콘솔 소비자를 실행하여 주제의 데이터를 볼 수도 있다(또는 사용자 지정 소비자 코드를 사용하여 처리).

```shell
> bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic connect-test --from-beginning
{"schema":{"type":"string","optional":false},"payload":"foo"}
{"schema":{"type":"string","optional":false},"payload":"bar"}
...
```

커넥터는 계속해서 데이터를 처리하므로 파일에 데이터를 추가하고 데이터가 파이프라인을 통해 이동하는 것을 볼 수 있다.

```shell
> echo Another line>> test.txt
```

콘솔 소비자 출력과 싱크 파일에 해당 줄이 표시되어야 한다.

## 7. Kafka Streams로 이벤트 처리
데이터가 Kafka에 이벤트로 저장되면 Java/Scala용 [Kafka Streams](https://kafka.apache.org/documentation/streams/) 클라이언트 라이브러리를 사용하여 데이터를 처리할 수 있다. 입력 및/또는 출력 데이터가 Kafka 주제에 저장되는 미션 크리티컬 실시간 애플리케이션 및 마이크로 서비스를 구현할 수 있다. Kafka Streams는 클라이언트 측에서 표준 Java 및 Scala 애플리케이션을 작성하고 배포하는 단순성과 Kafka의 서버 측 클러스터 기술의 이점을 결합하여 이러한 애플리케이션을 확장성, 탄력성, 내결함성 및 분산성을 높인다. 라이브러리는 정확히 1회 처리, 상태 저장 작업 및 집계, 기간 설정, 조인, 이벤트 시간 기반 처리 등을 지원한다.

첫 번째 예제를 위해 WordCount 알고리즘을 구현하는 방법은 다음과 같다.

```java
KStream<String, String> textLines = builder.stream("quickstart-events");

KTable<String, Long> wordCounts = textLines
            .flatMapValues(line -> Arrays.asList(line.toLowerCase().split(" ")))
            .groupBy((keyIgnored, word) -> word)
            .count();

wordCounts.toStream().to("output-topic", Produced.with(Serdes.String(), Serdes.Long()));
```

Kafka [Streams 데모](https://kafka.apache.org/documentation/streams/quickstart) 및 [앱 개발 자습서](https://kafka.apache.org/34/documentation/streams/tutorial)는 이러한 스트리밍 애플리케이션을 처음부터 끝까지 코딩하고 실행하는 방법을 보여준다.

## 8. Kafka 환경 종료
이제 빠른 시작을 마쳤으므로 자유롭게 Kafka 환경을 해체하거나 계속 사용해 본다.

* 아직 수행하지 않은 경우 `Ctrl-C`를 사용하여 생산자 및 소비자 클라이언트를 중지한다 .

* `Ctrl-C`를 사용하여 카프카 브로커를 중지한다.

* 마지막으로, Kafka with Zookeeper 섹션이 이어진 경우 `Ctrl-C`로 Zookeeper 서버를 중지한다.

도중에 생성한 이벤트를 포함하여 로컬 Kafka 환경의 데이터도 삭제하려면 다음 명령을 실행한다.

```shell
$ rm -rf /tmp/kafka-logs /tmp/zookeeper /tmp/kraft-combined-logs
```

## [출처 및 참고]
* [https://kafka.apache.org/quickstart](https://kafka.apache.org/quickstart)
