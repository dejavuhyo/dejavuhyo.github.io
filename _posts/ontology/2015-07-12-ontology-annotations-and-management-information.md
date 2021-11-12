---
title: 온톨로지의 주석과 관리정보
author: dejavuhyo
date: 2015-07-12 23:35:00 +0900
categories: [Language, Ontology]
tags: [온톨로지-주석, 온톨로지-관리, 온톨로지-헤더, 온톨로지-import, 온톨로지-버전관리, 온톨로지]
---

## 1. 온톨로지 관리
온톨로지를 관리하거나 이해하기 위한 정보를 적절하게 기술해 두는 것은 중요하다. 또한, import와 버전관리기능 등을 사용함으로써, 온톨로지의 연동 및 발전성이 확보된다.

## 2. 온톨로지의 주석
owl:AnnotationProperty 타입의 속성을 사용하여 주석달기를 할 수 있다. OWL에서는, RDF스키마의 다음과 같은 4가지를 주석형 속성으로서 정의하고 있다.

* rdfs:label

* rdfs:comment

* rdfs:seeAlso

* rdfs:isDefinedBy

* owl:versionInfo

임의의 속성을 온톨로지의 주석에 이용하기 위해서는, 주석형 속성임을 명시적으로 표명해야 한다.

## 2. 온톨로지 헤더
온톨로지 자체의 정보는, owl:Ontology 클래스의 인스턴스에 온톨로지 속성(owl:OntologyProperty)과 주석형 속성을 사용하여 기술한다.

### 1) owl:Ontology
온톨로지를 나타내는 클래스이다. 이와 같은 클래스의 인스턴스를 주어로 하는 RDF그래프는, 해당 온톨로지에 관한 정보를 나타낸다. ```<owl:Ontology>``` 요소는, 온톨로지 문서내에서는 정보를 관리하는 '헤어'로서의 역할을 수행한다.

온톨로지 자신은 owl:Ontology클래스의 인스턴스로서 표현된다. 온톨로지의 이름(URI참조)은 문서의 URI가 되며, 일반적으로 rdf:about=""으로서, 상대URI의 해결에 의해 자신을 나타낸다. ```<owl:Ontology>``` 요소내에는, 온톨로지 속성인 owl:import 등 과 같은 주석형 속성을 기술한다.

구상구문에서는, 온톨로지 전체를 Ontology()로 나타내고, 온톨로지 속성과 주석형 속성은 모두 Annotation()으로 기술한다.

## 3. 온톨로지의 import
온톨로지 헤더에서 owl:import속성을 사용하여 외부의 온톨로지를 포함시켜서, import된 온톨로지에 표명되어 있는 내용을 현재의 온톨로지에 추가할 수 있다. 웹 온톨로지의 공유 및 재이용을 위한 기능이다.

외부온톨로지를 포함시키면, 해당 온톨로지의 모든 표명(RDF그래프)이 현재의 온토로지의 그래프에 추가된다. 예를 들면, 온톨로지A, B의 이름공간을 각각 a:와 b:로 나타낼 경우, A에서 a:Violin rdfs:subClassOf b:StringInstrument. 라고 정의하고 있다면, A owl:imports B.라고 함으로써 비로소 b:StringInstrument의 공리도 A를 읽는 어플리케이션에 의해서 이용가능하게 되며, a:Violin의 의미를 명확하게 할 수 있게 된다.

### 1) owl:imports
온톨로지를 외부 온톨로지와 연결하여, 외부온톨로지를 포함시킨다. owl:Ontology Property클래스의 인스턴스이며, 정의역과 치역은 모두 owl:Ontology이다. owl:imports는 추이적이므로, A owl:imports B이고, B owl:imports C 이면, A owl:imports C.이 된다. 즉, 포함시킨 외부온톨로지의 헤어데 owl:imports가 기술되어 있는 경우, 해당 온톨로지도 포함대상이 된다는 것이다.

아래 그림은 온톨로지A를 읽어들인 에플리케이션은, owl:imports를 발견하여 온톨로지B를 읽어들여서 b:StringInstrument의 클래스공리도 그래프에 추가한다.

![imports](/assets/img/2015-07-12-ontology-annotations-and-management-information/imports.png)

RDF/XML을 필두로 하여, 일반적으로 온톨로지의 기술에 이용되는 RDF구문은, 맨 처음에 관련어휘의 이름공간을 선언한다. 그러나, 이것은 URI참조를 수식명으로 표기하기 위한 단축키에 불과하며, 이름공간URI에 있는 문서를 로드하고 있는 것은 아니다.

owl:imports는, 지정된 온톨로지의 그래프를 포함시킨다는 의도를 명시한 것이다(실제로 외부문서를 로드할지 여부는, 어플리케이션이 온톨로지를 어떻게 이용하는지에 따라서 다르다). 또한, owl:imports는 추이적이므로, 포함대상의 헤더에 owl:imports에 의해 기술되어 있는 온톨로지까지도 읽어들인다.

또한, 어휘의 이름공간URI와 어휘를 정의하는 온톨로지문서의 URI는 서로 다른 경우가 있으므로(예를들면, 정의문서는 확장자.rdf가 필요하다), 주의해야 한다. 그리고, OWL, RDF, RDF스키마의 어휘는 OWL의 해석모델에 미리 내장되어 있으므로, 이러한 정의들을 포함시킬 필요는 없다.

## 4. 버전 관리
OWL에서는, 온톨로지의 버전(owl:versionInfo)과 구버전에 대한 참조(owl:priorVersion), 그리고, 후방호환성이 있음(owl:backwordCompatibleWith)과 후방호환성이 없음(owl:incompatibleWith)을 헤더에 기술하여 버전관리를 수행할 수 있다. 또한, 각각의 이름에 대해서, 버전이 오래되었다는 등의 이유로 추천하지 않는다는 것을 나타내는 클래스(owl:DeprecatedClass, owl:DeprecatedProperty)가 제공되고 있다. 버전관리는, 웹 온톨로지의 발전성을 확보하기 위한 중요한 수단이다.

### 1) owl:versionInfo
버전번호와 작성(개정)년월일 등을, 인간을 위해서 텍스트 형태로 기술한다. owl:AnnotationProperty의 인스턴스로서, 정의역과 치역은 규정되어 있지 않다.

### 2) owl:priorVersion
현재의 온톨로지를 구버전과 연결시킨다. owl:OntologyProperty클래스의 인스턴스로서, 정의역과 치역이 모두 owl:Ontology이다.

### 3) owl:backwardCompatibleWith
현재의 온톨로지를 구버전과 연결시키고, 현재의 온톨로지가 구버전과 후방호환이 있음을 나타낸다. owl:OntologyProperty클래스의 인스턴스로서, 정의역과 치역이 모두 owl:Ontology이다.

### 4) owl: incompatibleWith
현재의 온톨로지를 다른 온톨로지와 연결시키고, 해당 온톨로지와는 (후방)호환성이 없음을 나타낸다. owl:OntologyProperty클래스의 인스턴스로서, 정의역과 치역이 머두 owl:Ontology이다.

### 5) owl:DeprecatedClass
클래스가 현재의 온톨로지에는 후방호환성을 위해서만 남아있으며, 새로운 도큐멘트에서는 사용해서는 안된다는 것을 나타낸다. rdfs:Class의 서브클래스이다.

### 6) owl:DeprecatedProperty
속성이 현재의 온톨로지에서는 후방호환성을 위해서만 남아있으며, 새로운 도큐멘트에서는 사용해서는 안된다는 것을 나타낸다. rdf:Property의 서브클래스이다.

## [출처 및 참고]
* 시맨틱 웹을 위한 RDF/OWL 입문
