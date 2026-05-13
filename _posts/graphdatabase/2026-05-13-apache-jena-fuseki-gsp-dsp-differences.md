---
title: Apache Jena Fuseki에서 GSP와 DSP 차이점
author: dejavuhyo
date: 2026-05-13 10:50:00 +0900
categories: [Database, GraphDatabase]
tags: [apache-jena, jena, apache-jena-fuseki, fuseki, graphdb, gsp, dsp, graph-store]
---

![apache-jena-logo](/assets/img/2026-05-13-apache-jena-fuseki-gsp-dsp-differences/apache-jena-logo.png)

## 1. GSP (Graph Store HTTP Protocol)
GSP는 W3C 표준 프로토콜로, HTTP 메서드(GET, POST, PUT, DELETE)를 사용하여 RDF 그래프를 관리한다.

### 1) 성격
표준 규격 (W3C Standard)

### 2) 목적
SPARQL 엔드포인트를 거치지 않고도 특정 명명된 그래프(Named Graph)나 기본 그래프(Default Graph)를 직접 읽고 쓰기 위해 사용한다.

### 3) 작동 방식

* GET: 특정 그래프의 내용을 RDF 포맷으로 가져옴

* PUT: 기존 그래프를 지우고 새로운 데이터로 대체

* POST: 기존 그래프에 새로운 데이터를 추가

* DELETE: 특정 그래프 자체를 삭제

### 4) 특징
Fuseki뿐만 아니라 다른 모든 RDF 저장소(Graph DB)에서도 공통으로 사용하는 방식이다.

## 2. DSP (Data Service Protocol / Dataset Protocol)
DSP는 Apache Jena Fuseki가 자체적으로 제공하는 데이터셋 관리용 인터페이스이다.

### 1) 성격
Fuseki 전용 기능

### 2) 목적
개별 그래프 단위의 조작보다는 데이터셋 전체의 상태 관리 및 Fuseki 서버 내부의 데이터 서비스 설정을 위해 존재한다.

### 3) 주요 기능

* 데이터셋의 생성 및 삭제 (Admin API 연동)

* 데이터셋의 가용성(Online/Offline) 상태 제어

* 데이터 백업 및 통계 확인

### 4) 특징
표준 프로토콜이 아니기 때문에 Fuseki 환경에 특화된 관리 작업을 수행할 때 주로 사용된다.

## 3. 차이점 비교

| 구분 | GSP (Graph Store Protocol) | DSP (Data Service Protocol) |
|:-----:|:-----:|:-----:|
| 표준 여부 | W3C 표준 | Apache Jena Fuseki 전용 |
| 조작 단위 | 개별 RDF 그래프 (Named/Default) | 전체 데이터셋 (Dataset) |
| 주요 용도 | RDF 데이터 CRUD | 데이터셋 관리, 백업, 설정 변경 |
| 사용 예시 | 특정 그래프에 `.ttl` 파일 업로드 | Fuseki 서버에서 새 데이터셋 생성 |
| 호환성 | 다른 Graph DB와 호환됨 | Fuseki에서만 사용 가능 |


* [https://www.w3.org/TR/sparql11-http-rdf-update/](https://www.w3.org/TR/sparql11-http-rdf-update/)
* [https://jena.apache.org/documentation/fuseki2/fuseki-server-protocol.html](https://jena.apache.org/documentation/fuseki2/fuseki-server-protocol.html)
* [https://jena.apache.org/documentation/fuseki2/fuseki-config-endpoint.html](https://jena.apache.org/documentation/fuseki2/fuseki-config-endpoint.html)
