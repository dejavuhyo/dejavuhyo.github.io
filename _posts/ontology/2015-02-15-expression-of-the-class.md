---
title: 클래스의 표현
author: dejavuhyo
date: 2015-02-15 01:02:00 +0900
categories: [Language, Ontology]
tags: [rdf, subclassof, owl, 클래스, 리소스, 계층구조, 온톨로지]
---

## 1. 클래스의 표현
클래스는 다양한 사물(리소스)들을 그룹화하는 유형, 또는 카테고리라고 생각할 수 있다. RDF스키마는, 클래스를 표현하기 위한 기본어휘(기본클래스, 기본속성)를 제공한다.

## 2. RDF스키마의 기본 클래스
아리스토텔레스 이후, 세상의 사물들을 카테고리에 따라서 정리하고 이해하기 위한 방법이 널리 사용되어 왔다. RDF스키마는, 대부분의 프로그래밍언어 및 지식표현 기법과 마찬가지로, 클래스(Class)에 의해 사물(리소스)들의 그룹을 나타낸다. 이와 같은 그룹에 속하는 리소스를 클래스의 인스턴스(Instance)라고 부른다.

RDF스키마는, 클래스를 표현하기 위하여 몇 가지 기본 클래스들을 제공하고 있다.

### 1) rdfs:Resource
Resource Description이라는 이름이 나타내고 있는 바와 같이, RDF가 표현하는 것들은 모두 "리소스" 클래스에 속한다. 존재하는 모든 것들은 rdfs:Resource의 인스턴스이다. 또한, 모든 클래스는 rdfs:Resource의 서브클래스가 된다.

### 2) rdfs:Class
RDF의 클래스전체를 나타내는 클래스이다. RDF스키마에서 표현하는 클래스는, 모두 rdfs:Class의 인스턴스가 된다.

### 3) rdfs:Literal
RDF로 기술하는 리터럴(문자열)은 rdfs:Literal클래스에 속한다. rdfs:Literal은 rdfs:Resource의 서브클래스이다. 또한, rdfs:Literal은 클래스이므로 rdfs:Class의 인스턴스가 된다.

### 4) rdfs:Datatype
데이터형을 나타내는 클래스이다. 모든 데이터형은 rdfs:Datatype의 인스턴스이다. 또한, 모든 rdfs:Datatype의 인스턴스는 rdfs:Literal의 서브클래스가 된다. rdfs:Datatype자신은 rdfs:Class의 서브클래스이며, 동시에 rdfs:Class의 인스턴스이기도 하다.

### 5) rdf:XMLLiteral
태그를 포함한 XML문자열(XML리터럴)을 나타내는 데이터형의 클래스이다. 데이터형의 일종이므로, rdfs:Datatype의 인스턴스 이며, rdfs:Literal의 서브클래스이다. XML리터럴은 RDF의 기본어휘에 포함되므로, 접두사는 rdfs:가 아니라 rdf:가 된다.

### 6) rdf:Property
RDF의 속성을 나타내는 클래스이다. 클래스라는 개념은 RDF스키마에서 도입되었지만, 속성은 RDF의 트리플을 구성하는 기본개념이므로, rdf:의 이름공간에 속하는 것으로서 표현된다.

![relationship-between-base-classes](/assets/img/2015-02-15-expression-of-the-class/relationship-between-base-classes.png)

## 2. RDF의 클래스표현
RDF스키마로 클래스를 정의(표현)하기 위해서는, 리소스가 rdfs:Class클래스에 속한다는 사실을 rdf:type속성으로 나타낸다. 예를 들면, ex:Cat이라는 이름을 갖는 리소스가 클래스라는 것을, ex:Cat rdf:type rdfs:Class .와 같은 트리플로 효현한다.

## 3. RDF클래스의 계층구조
클래스의 계층관계는 서브클래스를 나타내는 속성(rdfs:subClassOf)을 사용해서 나타낸다.

### 1) rdfs:subClassOf
주어가 되는 리소스(클래스)가, 목적어가 되는 리소스(클래스)의 서브클래스임을 나타낸다.

클래스 C1이 클래스 C2의 서브클래스인 경우, C1 rdfs:subClassOf C2 .와 같은 트리플로 표현한다.
이때, 클래스C1의 모든 인스턴스들은 클래스C2의 인스턴스가 된다. 즉, C1과 C2,의 인스턴스 집합을 각각 S1과 S2라고 했을 때, C1 rdfs:subClassOf C2 ⇒ S1⊆S2이다.
이것은, C1 rdfs:subClassOf C2이고, C2 rdfs:subClassOf C3인 경우, 클래스C1은 클래스C3의 서브클래스임을 의미한다(이와 같은 관계를 속성rdfs:subClassOf가 추이적이다(transitive)라고 말한다).

## 4. 클래스와 클래스의연
RDF에서는 모든 것이 리소스로서 취급된다. 클래스의 인스턴스는 물론 리소스이지만, 클래스 자체도 어떤 개념에 대응하는 추상적인 리소스이다. 특히, 어떤 클래스의 인스턴스들의 집합은 클래스 자체가 아니라는 점에 주의하기 바란다. 클래스 ex:Cat은 '고양이'라는 개념을 나타내는 리소스이지만, Mika와 같은 인스턴스들의 집합은 ex:Cat라는 이름으로 그룹화되는 리소스(고양이 자체)의 전체를 나타내며, 추상적인 개념(=클래스)과는 구별된다.

인스턴스들의 집합은 리소스로서의 클래스와 구별하기 위해서, 해당 클래스의 외연(class extension)이라고 부른다. 일반적으로 클래스 자신과 클래스의 외연은 중첩되지 않지만, RDFS는 클래스가 자신의 클래스외연의 멤버라는 사실(즉, 자기자신의 인스턴스라는 사실)도 인정하고 있다.

## [출처 및 참고]
* 시맨틱 웹을 위한 RDF/OWL 입문
