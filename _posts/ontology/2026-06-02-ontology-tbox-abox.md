---
title: 온톨로지 TBox와 ABox
author: dejavuhyo
date: 2026-06-02 11:00:00 +0900
categories: [Language, Ontology]
tags: [ontology, tbox, terminological-box, abox, assertion-box, 온톨로지, 추론]
---

## 1. TBox와 ABox
온톨로지(Ontology)를 이해할 때 TBox와 ABox는 지식베이스(Knowledge Base)를 구성하는 가장 핵심적인 두 가지 개념이다.

쉽게 설명하면 TBox는 데이터베이스의 스키마(Schema)이고, ABox는 실제 저장되는 데이터(Data)라고 볼 수 있다.

## 2. TBox (Terminological Box)
TBox는 용어 사전 또는 개념의 틀이다. 도메인(분야)에 존재하는 개념(Class/Concept)들과 그 개념들 간의 관계(Relation/Property)를 정의한다.

* 역할: 지식의 구조와 규칙을 추상적으로 정의 한다.

* 특징: 구체적인 특정 개체(인스턴스)는 등장하지 않으며, 전반적인 설계도 역할을 한다.

* 주요 구성 요소: 클래스(Class), 서브클래스 관계(Subclass), 속성 정의(Property)

## 3. ABox (Assertion Box)
ABox는 TBox에서 정의한 틀을 바탕으로, 실제 세상에 존재하는 구체적인 사실(Assertion)을 기록한다. 개별 인스턴스(Instance/Individual)와 그들 사이의 관계로 나타낸다.

* 역할: 구체적인 개체 정보와 상태를 구체적으로 기술 한다.

* 특징: TBox의 규칙을 위배할 수 없으며, 실제 데이터가 채워지는 공간이다.

* 주요 구성 요소: 인스턴스 타입 지정(Concept Assertion), 관계 및 속성 값 지정(Role Assertion)

## 4. TBox와 ABox 비교

| 구분 | TBox (Terminological Box) | ABox (Assertion Box) |
|:-----:|:-----:|:-----:|
| 성격 | 개념적, 선언적, 스키마(Schema) | 사실적, 구체적, 데이터(Data) |
| 대상 | 클래스(Class), 속성(Property) | 인스턴스(Instance), 값(Value) |
| 추상도 | 높음 (틀을 만드는 작업) | 낮음 (틀에 내용을 채우는 작업) |
| 변경 빈도 | 낮음 (지식 구조는 자주 바뀌지 않음) | 높음 (새로운 데이터는 계속 추가됨) |
| DB 비유 | 테이블 정의 (CREATE TABLE) | 데이터 삽입 (INSERT INTO) |

## 5. 구분 이유
온톨로지 기반의 시맨틱 웹이나 지식 그래프에서 이 둘을 나누는 이유는 추론(Reasoning) 때문이다.

AI(추론기)는 TBox의 규칙을 보고, ABox에 명시되지 않은 새로운 사실을 스스로 찾아낼 수 있다.

## [출처 및 참고]
* [https://www.w3.org/OWL/](https://www.w3.org/OWL/)
* [https://en.wikipedia.org/wiki/Web_Ontology_Language](https://en.wikipedia.org/wiki/Web_Ontology_Language)
