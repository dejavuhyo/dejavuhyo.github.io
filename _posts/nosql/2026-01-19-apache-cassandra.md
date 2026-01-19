---
title: Apache Cassandra
author: dejavuhyo
date: 2026-01-19 09:50:00 +0900
categories: [Database, NoSQL]
tags: [apache-cassandra, cassandra, 아파치-카산드라, 카산드라, nosql]
---

## 1. 카산드라란
아파치 카산드라(Apache Cassandra)는 대규모의 데이터를 처리하기 위해 설계된 오픈 소스 분산 NoSQL 데이터베이스 관리 시스템이다.

페이스북에서 개발하여 현재는 아파치 소프트웨어 재단에서 관리하고 있으며, 넷플릭스, 애플, 인스타그램 등 전 세계적으로 거대한 트래픽을 감당하는 기업들이 필수적으로 사용하는 기술이다.

## 2. 아키텍처
카산드라는 기존의 관계형 데이터베이스(RDBMS)와는 근본적으로 다른 구조를 가진다.

* 마스터리스(Masterless) 구조: 모든 노드가 대등한 지위를 갖는 'P2P' 방식이다. 특정 '마스터' 노드가 없기 때문에 단일 장애점(Single Point of Failure)이 존재하지 않는다.

* 가십 프로토콜(Gossip Protocol): 노드들끼리 서로 상태 정보를 주고받으며 네트워크의 상태를 파악한다.

* 링(Ring) 구조: 데이터가 원형의 링 구조에 따라 여러 노드에 분산 저장된다.

## 3. 주요 특징

### 1) 선형적 확장성 (Scalability)
노드를 추가하는 만큼 정직하게 성능과 저장 용량이 늘어난다. 수천 개의 노드로 구성된 클러스터에서도 성능 저하 없이 운영이 가능하다.

### 2) 고가용성 (High Availability)
데이터를 여러 노드에 복제(Replication)하여 저장한다. 특정 서버나 심지어 특정 데이터 센터 전체가 다운되더라도 서비스는 중단되지 않는다.

### 3) 높은 쓰기 성능
카산드라는 LSM Tree(Log-Structured Merge-Tree) 기반의 저장 방식을 사용하여, 읽기보다 쓰기 작업에 최적화되어 있다.

로그를 기록하듯 데이터를 순차적으로 쌓기 때문에 매우 빠른 쓰기 속도를 자랑한다.

### 4) 유연한 스키마
컬럼 패밀리(Column Family) 기반의 데이터 모델을 사용하여, 행(Row)마다 다른 컬럼을 가질 수 있는 유연함을 제공한다.

## 4. 데이터 모델링: CQL (Cassandra Query Language)
카산드라는 SQL과 유사한 CQL이라는 언어를 사용한다. 문법은 비슷하지만 내부 동작 방식은 완전히 다르다.

* 조인(Join) 불가: 성능을 위해 여러 테이블을 합치는 Join 기능을 지원하지 않는다.

* 쿼리 기반 설계: RDBMS는 데이터를 먼저 설계하지만, 카산드라는 "어떤 쿼리를 날릴 것인가"를 먼저 결정하고 그에 맞춰 테이블을 설계한다. (Denormalization 권장)

## 5. CAP 이론과 카산드라
분산 시스템의 CAP 이론(일관성, 가용성, 단절 내성) 중에서 카산드라는 기본적으로 가용성(A)과 단절 내성(P)을 선택한 시스템이다.

하지만 Tunable Consistency(조정 가능한 일관성)라는 강력한 기능을 제공하여, 사용자가 상황에 따라 성능과 일관성 사이의 균형을 직접 조절할 수 있다.

| 설정 | 설명 |
|:-----:|:-----:|
| ONE | 데이터가 최소 하나의 노드에만 써지면 성공으로 간주 (매우 빠름) |
| QUORUM | 과반수의 노드에 써지면 성공으로 간주 (균형) |
| ALL | 모든 복제 노드에 써져야 성공으로 간주 (강력한 일관성, 느림) |

## 6. 사용 환경

* 권장 상황: 대규모 시계열 데이터(IoT 센서 데이터), 메시징 서비스, 사기 탐지(Fraud Detection), 실시간 로그 수집

* 비권장 상황: 데이터의 빈번한 수정/삭제가 일어나는 경우, 복잡한 트랜잭션 처리가 필요한 금융 결제 시스템, 조인 연산이 필수적인 서비스

카산드라는 "절대 죽지 않는 데이터베이스"가 필요하고, 데이터 양이 기하급수적으로 늘어날 것이 예상될 때 최고의 선택지이다.

## [출처 및 참고]
* [https://github.com/apache/cassandra](https://github.com/apache/cassandra)
* [https://cassandra.apache.org/](https://cassandra.apache.org/)
* [https://www.pulumi.com/what-is/amazon-dynamodb-vs-google-cloud-bigtable/](https://www.pulumi.com/what-is/amazon-dynamodb-vs-google-cloud-bigtable/)
* [https://www.geeksforgeeks.org/dbms/the-cap-theorem-in-dbms/](https://www.geeksforgeeks.org/dbms/the-cap-theorem-in-dbms/)
