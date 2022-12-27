---
title: 개체에 관한 사실의 기술
author: dejavuhyo
date: 2015-03-09 22:04:00 +0900
categories: [Language, Ontology]
tags: [instance, ontology-instance, owl-sameas, owl-differentfrom, owl-alldifferent, owl-distinctmembers]
---

## 1. 개체
클래스의 인스턴스(개체)에 관한 공리를 '사실'이라고 부르며, 소속클래스와 속성의 값, 또는 개체의 동일성 및 상위성의 식별에 의해 기술한다. 클래스공리와 속성공리에 의해 준비된 어휘를 사용하여 다양한 사항을 표현한다.

## 2. 소속클래스와 속성값에 의한 사실
OWL에서는 클래스외연의 멤버를 개체(Individual)라고 부르고, 개체에 관한 공리를 사실(fact)이라고 부른다. '사실'을 나타내는 방법들 중에서 한 가지는, 어떤 개체가 어떤 클래스에 속하는지, 그리고 어떤 속성값을 갖는지를 기술하는 것이다.

## 3. 개체의 식별에 의한 사실
웹상에서는 상이한 URI참조가 같은 객체(개체)를 가리키는 경우가 있다. 이를 위해서 OWL(및 RDF)에서는, 이름(URI참조)이 상이하다고 해서 2개의 개체가 서로 다르다고 결론을 내릴 수 없다. 따라서, 2개의 개체가 동일하다(owl:sameAs), 상이하다(owl:differentFrom), 또는 일련의 개체들이 서로 상이하다(owl:AllDifferent)는 것을 나타내는 것도 매우 중요한 '사실'이다. 개체의 동일성 및 분리성을 명시하는 것은 논리적인 추론을 수행하는 경우에 매우 중요하다.

### 1) owl:sameAs
2개의 개체가 (이름은 서로 다르더라도) 동일하다는 것을 나타낸다. 정의역과 치역은 모두 owl:Thing이다. OWL Full의 경우에는, 클래스와 속성을 개체로 간주하여, 2개의 클래스와 속성이 같다(외연 및 대응하는 개념이 같다)는 것을 나타내기 위하여 사용할 수도 있다.

### 2) owl:differentFrom
2개의 개체가 서로 다름을 나타낸다. 정의역과 치역은 모두 owl:Thing이다.

### 3) owl:AllDifferent
속성 owl:distinctMembers와 조합하여, 일련의 개체가 서로 다르다는 것을 나타낸다.

### 4) owl:distinctMembers
목적어 리스트에 열거된 일련의 개체들이 서로 다르다는 것을 나타낸다. 정의역은 owl:AllDifferent이고, 치역은 rdf:List이다.

## [출처 및 참고]
* 시맨틱 웹을 위한 RDF/OWL 입문
