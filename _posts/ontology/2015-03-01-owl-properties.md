---
title: OWL의 속성
author: dejavuhyo
date: 2015-03-01 23:01:00 +0900
categories: [Language, Ontology]
tags: [owl-properties, ontology, owl-속성, 온톨로지]
---

## 1. OWL의 속성
OWL에서는 속성을 '공리'로서 표현한다. 속성공리는 RDF스키마 어휘를 이용한 광역적인 정의역과 치역, 계층관계 외에, OWL의 어휘를 사용한 논리적 성질의 기술 등으로 구성된다. 또한, OWL에서는, 속성을 여러 개로 불리도니 타입으로 나누어서 다룰 수 있다. 속성공리는, 클래스의 속성제약과는 다르며, 어떤 클래스에 적용할 것인지를 묻지 않는 광역적인 성질을 나타낸다.

## 2. OWL의 기본적인 속성공리
OWL의 속성에는, 개체들 간의 관계를 기술하는 개체값형 속성(individualvalued Property = owl:ObjectProperty)과, 개체를 데이터값과 관련시키는 데이터값형 속성(datavalueProperty = owl:DatatypeProperty)이 있다. 또한, 온톨로지의 시룻에 사용되는 특수한 타입으로서 주석형 속성과 온톨로지 속성이 제공되고 있다.

### 1) owl:ObjectProperty
어떤 개체를 또다른 개체에 관련시키는 개체값형 속성이 속하는 클래스이다. 이와 같은 타입의 속성은 목적어가 URI참조를 갖는 노드 또는 공백노드가 되며, 데이터값(리터럴)은 목적으로 되지 않는다. owl:ObjectProperty는 rdf:Property의 서브클래스이다. OWL Full에서는, 데이터값이 개체가 될 수도 있기 때문에, owl:ObejctProperty는 rdf:Property와 동등(equivalentClass)하게 된다.

### 2) owl:DatatypeProperty
개체를 데이터값에 연결시키는 데이터값형 속성이 속하는 클래스이다. 이와 같은 유형의 속성은, 목적어가 리터럴이 되며 URI참조를 갖는 노드와 공백노드는 목적어가 되지 않는다. owl:DatatypePropeerty은 rdf:Property의 서브클래스이다. OWL DL/Lite에서는, ObejctProperty와 DatatypeProperty는 엄밀하게 구별되지만, OWL Full에서는 명확하게 구분되지 않는다. OWL Full에스는 owl:DatatypeProperty느느 owl:ObjectProperty의 서브클래스이다.

### 3) owl:AnnotationProperty
온톨로지의 보충정보를 기술하기 위해서 사용되는 주석형 속성이 속하는 클래스로서, rdf:Property의 서브클래스이다. 이와 같은 유형의 속성에는, 정의영과 치역, 서브속성 등을 기술할 수 없다.

### 4) owl:OntologyProperty
온톨로지 자체의 관리정보를 헤더에 기술하기 위한 특수한 속성이 속한 클래스로서, rdf:Property의 서브클래스이다. 이와 같은 유형의 속성은 정의역과 치역이 모두 owl:Ontology가 된다.

속성의 공리는, 이와 같은 4가지 유형 중에서 어떤 것을 나타내는 트리플을 가지고(RDF/XML을 사용하여 기술할 경우에는 일반적으로 해당되는 유형화된 노드요수), RDF스키마의 rdfs:domain와 rdfs:range를 사용하여 정의역과 치역을 추가하여 기술할 수 있다.

rdfs:domain과 rdfs:range의 기술방법은 RDF스키마의 기술방법과 같지만, OWL에서는 다음의 사항들에 주의할 필요가 있다.

* 정의역 및 개체값형 속성의 치역은, OWL Lite에서는 클래스며응로 한정된다.

* 데이터값형 속성의 치역은, OWL Lite에서는 데이터형 또는 rdfs:Literal로 한정된다(OWL DL에서는 열거식 데이터형도 사용할 수 있다).

* 주석형 속성과 온톨로지 속성에는 정의역과 치역을 기술할 수 없다.

## 3. 다른 속성과의 관계에 의한 표현
개체값형 속성과 데이터값형 속성은, 계층관계(rdfs:subClassOf), 동치관계(owl:equivalentProperty), 반대관계(owl:inverseOf)등을 사용하여, 다른 속성과의 관계에 의해 공리를 기술할 수 있다. rdfs:subPropertyOf의 사용방법은 RDF스키마와 같으며, OWL에서는 다음과 같은 사항들에 주의할 필요가 있다.

* OWL DL/Lite에서 rdfs:subPropertyOf를 사용하는 경우, 개체값형 속성은 개체값형 속성의 서브속성이 되고, 데이터값형 속성은 데이터값형 속성의 서브속성으로 한다.

* 주석형 속성과 온톨로지 속성에서는 서브속성관계를 기술할 수 없다.

### 1) owl:equivalentProperty
2개의 속성이 동일한 속성외연을 갖는다는 것을 의미한다. 이것은 2개 속성이 서로 서브속성이라는 것과 같다. owl:equivalentProperty의 정의역과 치역은 모두 rdf:Property이다.

### 2) owl:inverseOf
2개의 속성이 반대관계에 있음을 나타낸다. 속성 P1과 P2가 owl:inverseOf관계에 있는 경우, 만약 P1(x, y)라면 P2(y, x)라는 관계가 성립한다. owl:inverseOf의 정의역과 치역은 모두 owl:ObjectProperty이다.

owl:equivalentProperty라는 관계는, 2개의 속성이 '같은 의미'임을 나타내는 것이 아니다. dataPublished와 dataCopyrighted는 같은 속성외연을 갖는 동등한 속성들일 수도 있겠지만, 반드시 같은 의미를 표현하고 있지는 않다. 온톨로지 및 스키마를 적성할 경우에 '~의 멤버이다'와  '~라는 멤버가 있다'와 같은 반대 관계를 나타내는 속성들을 함께 정의하는 경우도 있다.

## 4. 속성출현횟수에 대한 광역적인 제약
속성의 값이 1개로 규정된 함수형 속성(owl:FunctionalProperty)과, 이와는 반대로 속성의 값으로부터 주어가 되는 개체가 규정되는 역함수형 속성(owl:InverseFunctionalProperty)은, 출현횟수에 대한 광역적인 제약을 갖는 속성이다.

### 1) owl:FunctionalProperty
속성P가 owl:FunctionalProperty인 경우, 모든 x에 대해서 P(x, y)의 y가 1개로 규정된다. y=P(x)라는 함수의 대응관계가 성립하므로 funcational이라고 부른다. 출현 횟수가 0 또는 1이라는 제약과 동등하다.

### 2) owl:InverseFunctionalProperty
속성P가 owl:InverseFunctionalProperty는 owl:ObjectProperty의 서브클래스이다. 즉, OWL LD/Lite에서는 데이터값형 InverseFunctionalProperty라고 할 수는 없다. OWL Full의 경우, 데이터 값형 속성은 개체값형 속성의 서브클래스이므로 InverseFunctionalProperty로 할 수 있다.

## 4. 속성의 논리적인 성질
속성이 추이형 속성(owl:TransitiveProperty)인지, 아니면 대칭형 속성(owl:SymmetricProperty)인지를 나타내두면, 이와 같은 관계를 이용하여 추론을 수행할 수 있다.

### 1) owl:TransitiveProperty
owl:TransitiveProperty클래스에 속하는 속성P는, P(x, y) ^ P(y, z)이라면 P(x, z)가 성립하는 추이적인(transitive) 성질을 갖는다는 것을 나타낸다. owl:TransitiveProperty은 owl:ObjectProperty의 서브클래스이다.

### 2) owl:SymmetricProperty
owl:SymmetricProperty클래스에 속하는 속성P는, P(x, y)이면 P(y, x)가 성립하는 대칭적인(symmetric)성질을 갖는다는 것을 나타낸다. owl:SymmetricProperty는  owl:ObjectProperty의 서브클래스이다.

OWL DL/Lite에서는 추이형 속성에서는 지역적이든 광역적이든 상관없이 출현횟수에 대한 제약을 설정할 수 없으며, 또한, 해당 속성의 상위속성, 그리고 해당속성과 owlP:inverseOf관계에 있는 속성 및 그 상위속성에도 출현횟수의 제약을 설정할 수 없다.

rdfs:subClassOf 및 rdfs:subPropertyOf는 추이형 속성의 대표적인 예이다.

대칭형 속성은, 주어와 목적어를 바꾸더라도 같은 관계가 성립한다. 이와 같은 속성의 정의역과 치역은 같은 클래스가 된다.

## [출처 및 참고]
* 시맨틱 웹을 위한 RDF/OWL 입문
