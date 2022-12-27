---
title: 속성의 제약에 의한 클래스 표현
author: dejavuhyo
date: 2015-02-26 00:39:00 +0900
categories: [Language, Ontology]
tags: [class-expression, attribute-constraint, ontology, 속성-제약, 클래스-표현, 온톨로지]
---

## 1. 속성제약
클래스의 성질은, 해당클래스가 갖는 속성의 조건을 규정함으로써 표현할 수 있다. RDF의 속성은, 클래스와는 독립적으로 정의(표현)되지만, 특정한 클래스와 함께 사용하는 경우, 값의 범위 및 출현횟수를 한정하는 것이 OWL의 속성제약이다.

## 2. 속성제약을 위한 Restriction클래스
속성제약은 owl:Restriction클래스를 사용하여 owl:onProperty에 제약사항을 부여하는 속성을 나타냄으로써 표현한다.

### 1) owl:Restriction
속성에 의한 제약을 기술한다. owl:Restriction은 owl:Class의 서브클래스이면서 동시에, rdfs:Class의 인스턴스이다. owl:Restriction은 익명클래스가 되며, 제약대상이되는 속성을 나타내는 owl:onProperty를 반드시 1개 가지며, 또한 다음페이지 이후에 설정하는 값 또는 출현횟수에 관한 제약식을 반드시 1개씩 갖는다.

### 2) owl:onProperty
owl:Restriction에 의한 제약대상이 되는 속성을 나타낸다. 정의역은 owl:Restriction이며, 치역은 rdf:Property이다.

## 3. 값에 관한 제약
값에 관한 제약은, 어떤 클래스와 함께 사용하는 경우의 속성에 대한 치역을 나타낸다. 이와 같은 제약은, OWL의 내장형 속성인 owl:allValuesFrom, owl:someValuesFrom, owl:hasValue에 의해 기술한다.

### 1) owl:allValuesFrom
owl:Restriction을 클래스표현 또는 데이터치역에 관련시킨다(OWL Lite에서는 목적어는 클래스명에 의한 클래스표현에 한정한다). 정의역은 owl:Restriction이며, 치역은 rdfs:Class이다.

owl:allValuesFrom은, 주어가 되는 Restriction클래스의 모든 인스턴스들에 대해서, 해당 제약대상인 속성의 값이 모든 목적어클래스의 인스턴스 또는 목적어 데이터치역으로 표현된 값임을 나타낸다. 이것은, 술어논리의 전칭기호(∀)와 같은 동작을 한다.

### 2) owl:someValuesFrom
owl:Restriction을 클래스표현 또는 데이터치역에 관련시킨다(OWL Lite에서는, 목적어는 클래스명에 의한 클래스표현에 한정한다). 정의역은 owl:Restriction이며, 치역은 rdfs:Class이다.

owl:someValuesFrom은, 주어가 되는 Restriction클래스의 모든 인스턴스에 대해서, 해당 제약대상인 속성들의 값들 중에서 적어도 1개가 목적어클래스의 인스턴스 또는 목적어 데이터치역으로 표현된 값임을 나타낸다. 이것은, 술어논리의 존재기호(∃)와 같은 동작을 한다.

### 3) owl:hasValue
owl:Restriction을 1개의 개체 또는 데이터값에 관련시킨다(OWL Lite에서는 owl:hasValue는 사용할 수 없다). 정의역은 owl:Restriction이지만, 치역은 규정되어 있지 않다. owl:hasValue는, 주어가 되는 Restriction클래스의 모든 인스턴스들에 대해서, 해당 제약대상인 속성 값들 중에서 적어도 1개가 목적어로 표현된 값과 같음을 나타낸다.

## 4. 출현횟수에 관한 제약
OWL에서는 RDF처럼, 개체는 같은 속성임을 임의 개수만큼 가질 수 있다. 이와 같은 속성의 출현횟수(cardinality)도 클래스의 성질을 표현하는 중요한 요소가 된다. 출현횟수에 관한 제약은, owl:maxCardinality, owl:minCardinality, owl:cardinality를 사용하여 기술한다.

### 1) owl:maxCardinality
주어Restriction클래스의 모든 인스턴스들에 대해서, 제약대상속성의 출현횟수가 목적어로서 지정된 값 이하임을 나타낸다. 정의역은 owl:Restriction이고 치역은 xsd:nonNegativeInteger이다(OWL Lite에서는, 치역은 0 또는 1이다).

이 값이 0인 경우에는, 해당 속성은 금지되어 있다(이런 속성을 갖는 개체는 주어클래스의 외연에는 속하지 않는다)는 것을 나타낸다. 또한, 이 값이 1인 경우에는, 주어클래스의 인스턴스가 해당 속성을 가질 때, 그 값이 한가지로 정해진다. 이와 같은 속성을 함수형(functional) 속성이라고 부른다(owl:FunctionalProperty를 참조).

### 2) owl:minCardinality
주어의 모든 인스턴스들에 대해서, 제약대상속성의 출현횟수가 목적어로서 지정된 값 이상임을 나타낸다. 정의역은 owl:Restriction이고 치역은 xsd:nonNegativeInteger이다(OWL Lite에서는 치역은 0 또는 1이다).

이 값이 1이상인 경우에는, 해당 속성은 필수적임을 의미한다.

### 3) owl:cardinality
주어의 모든 인스턴스들에 대해서, 제약대상속성의 출현횟수가 목적어로서 지정된 값과 항상 같음을 나타낸다. 정의역은 owl:Restriction이고, 치역은 xsd:nonNegativeInteger이다(OWL Lite에서는 치역은 0 또는 1이다). 이것은, owl:maxCardinality와 owl:minCardinality가 같은 경우에 간략하게 표기하기 위해 사용되는 속성이다.

## [출처 및 참고]
* 시맨틱 웹을 위한 RDF/OWL 입문
