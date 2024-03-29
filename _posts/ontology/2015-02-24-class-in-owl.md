---
title: OWL의 클래스
author: dejavuhyo
date: 2015-02-24 00:33:00 +0900
categories: [Language, Ontology]
tags: [owl, 클래스, owl-class, owl-thing, owl-nothing, 클래스-표현, 클래스-공리]
---

## 1. OWL 클래스란
클래스는, 같은 성질을 갖는 리소스들을 그룹화하고 공통성질을 논리적으로 표현하기 위한 기능을 제공한다. 클래스에 의한 개념의 체계적인 기술은 온톨로지의 핵심이 된다.

## 2. OWL의 기본 클래스
OWL에서는 기본어휘 owl:Class에 의해서 클래스를 표현한다. RDF의 클래스처럼, OWL의 클래스도 URI참조를 이용하여 명명된다. 클래스의 인스턴스는 개체(Individual), 인스턴스의 집합은 클래스의 외연(Class Extension)이라고 부른다.

### 1) owl:Class
어떤 개념을 추상화하여 표현한다. OWL DL/Lite에서는, 클래스는 명시적으로 owl:Class타입을 보여해야 되며, 클래스를 명명하는 URI참조는 동시에 속성과 개체를 명명할 수는 없다. 또한, 모든 개체는 적어도 한 개의 owl:Class에 속하지 않으면 안된다. owl:Class는 rdfs:Class의 서브클래스이다.

### 2) owl:Thing
OWL에 내장되어 있는 클래스로서, OWL로 기술하는 세계의 개체 전체에 대응한다. 모든 개체들은 owl:Thing의 인스턴스이며, 모든 OWL의 클래스들은 owl:Thing의 서브클래스가 된다.

### 3) owl:Nothing
OWL에 내장되어 있는 클래스로서, 인스턴스를 한개도 갖지 않는 '공집합'에 대응한다. 정의에 의해 공집합은 모든 집합의 부분집합이 되므로, owl:Nothing은 모든 OWL 클래스의 서브클래스가 된다.

OWL이 RDF스키마의 클래스(rdfs:Class)와는 별도로 owl:Class를 규정하고 있는 것은, OWL DL/Lite에서의 타입분리에 대응하는 것으로서, owl:Class인 리소스는 동시에 속성 및 개체가 될 수는 없다. 한편, OWL Full에서는, owl:Class는 rdfs:Class와 같은 것이다. 또한, OWL Full에서 owl:Thing은 rdfs:Resource와 같다.

## 2. 클래스표현과 클래스공리
OWL의 클래스는, 클래스표현(class description)이라는 구성요소를 조합하여 클래스공리(class axiom)로서 '정의'된다.

OWL의 클래스표현에는 6가지 방법이 있다.

* URI참조에 의한 클래스의 명칭

* 클래스의 인스턴스가 되는 개체의 열거

* 속성의 제약

* 2개 이상의 클래스표현의 교집합

* 2개 이상의 클래스표현의 합집합

* 클래스표현의 부정

클래스 명명은, 클래스를 규정하는데 있어서 기본적인 요소이므로, 이것 또한 '클래스표현'의 한 가지 방법으로 생각할 수 있다. 나머지 방법들은, 모두 "해당 클래스의 외연 멤버이기 위한 조건"을 기술하여 어떤 익명의 클래스를 표현하는 것으로서, 클래스의 명명과 조합하여 클래스 공리를 구성한다.

RDFS에서의 클래스 기술은, 서브클래스관계에 의한 의미네트워크적인 표현이 중심이었으나, OWL에서의 클래스공리는 지식표현의 '프레임'과 유사한 구조에 의해 해당 개념의 속성을 상세히 기술한다. 클래스표현은, 프레임의 '슬롯(slot)'에 상응하는 역할이라고 생각할 수 있다.

## [출처 및 참고]
* 시맨틱 웹을 위한 RDF/OWL 입문
