---
title: Apache Jena
author: dejavuhyo
date: 2026-03-25 15:20:00 +0900
categories: [Language, Ontology]
tags: [apache-jena, jena, graphdb, rdf, owl, triples, sparql, inference, ontology, arq, triple-store]
---

## 1. Apache Jena란
Apache Jena는 시맨틱 웹(Semantic Web) 및 링크드 데이터(Linked Data) 애플리케이션을 구축하기 위한 강력한 오픈 소스 Java 프레임워크이다.

단순히 데이터를 저장하는 것을 넘어, 데이터 간의 의미를 컴퓨터가 이해할 수 있는 방식으로 처리하는데 특화되어 있다.

## 2. RDF

### 1) RDF API

#### (1) Graphs, Models
데이터의 기본 단위인 Triple(주어, 술어, 목적어)을 다루는 핵심 인터페이스이다.

Jena에서는 RDF 트리플 모음이 제공하는 모든 상태 정보가 모델이라는 데이터 구조에 저장된다.

모델은 RDF 그래프를 나타내는데, 이는 레이블이 지정된 관계로 서로 연결된 RDF 노드들의 모음을 포함하기 때문이다.

각 관계는 단방향으로만 이루어지므로 트리플은 다음과 같다.

```text
example:ijd foaf:name "Ian"
```

#### (2) Triples
RDF 그래프에서 관계는 항상 하나의 주제 리소스를 다른 리소스 또는 리터럴에 연결한다.

```text
example:ijd foaf:firstName "Ian".
example:ijd foaf:knows example:mary.
```

관계 또는 predicate는 항상 두 노드를 연결한다(형식적으로는 인자가 2개이다).

predicate의 첫 번째 인자는 연결하려는 노드이고, 두 번째 인자는 연결 하려는 대상 노드이다. 이들을 RDF 문장의 subject와 object라고 부른다.

subject-predicate-object 패턴은 매우 일반적이어서 때때로 SPO라는 약어를 사용하기도 한다.

더 일반적으로는 하나의 subject, predicate, object로 이루어진 문장을 트리플 이라고 부르며, 이는 RDF 정보를 저장하는 방법을 트리플스토어라고 부르는데 자연스럽게 이어진다.

![rdf-graph-and-triples](/assets/img/2026-03-25-apache-jena/rdf-graph-and-triples.png)

#### (3) Properties
RDF 그래프에서 두 리소스 또는 리소스와 리터럴 간의 연결은 속성 식별자로 표시된다.

RDF 자체가 리소스 이름으로 URI를 사용하여 이름 충돌 가능성을 최소화하는 것처럼 속성도 URI로 식별된다.

사실 RDF 속성은 RDF 리소스의 특수한 경우이다.

Jena에서는 속성을 Property 객체로 표현하는데, 이 객체는 Resource(Resource 자체는 RDFNode의 Java 하위 클래스)의 Java 하위 클래스이다.

### 2) ARQ (SPARQL)
ARQ는 SPARQL RDF 쿼리 언어를 지원하는 Jena용 쿼리 엔진이다.

SPARQL은 W3C RDF 데이터 액세스 워킹 그룹에서 개발한 쿼리 언어이다.

Jena의 질의 처리 엔진인 ARQ는 W3C 표준 SPARQL 업데이트 언어를 완벽하게 지원한다.

복잡한 그래프 패턴 매칭이나 집계 기능을 수행한다.

## 3. Triple Store

### 1) 저장소 시스템 (TDB & TDB2)
TDB는 Jena의 RDF 저장 및 쿼리 구성 요소이다.

Jena의 모든 API를 지원하며, 단일 머신에서 고성능 RDF 저장소로 사용할 수 있다.

TDB와 TDB2 데이터베이스는 호환되지 않는다.

* TDB: 고성능 원자적(Atomic) 트랜잭션을 지원하는 비관계형 그래프 저장소이다.

* TDB2: 최신 버전으로, 확장성과 안정성이 개선된 차세대 저장소이다.

### 2) Fuseki
Jena 데이터를 HTTP를 통해 접근할 수 있게 해주는 SPARQL 서버이다. 독립형 서버로 실행하거나 애플리케이션에 내장하여 사용할 수 있다.

쿼리 및 업데이트를 위한 SPARQL 1.1 프로토콜과 SPARQL 그래프 스토어 프로토콜을 제공한다.

TDB와 통합되어 강력하고 트랜잭션 기반의 영구 저장소 계층을 제공한다. 또한 Jena 텍스트 쿼리 기능도 내장되어 있다.

Fuseki 엔진은 Maven 아티팩트로도 사용할 수 있다.

```xml
<dependency>
   <groupId>org.apache.jena</groupId>
   <artifactId>jena-fuseki-main</artifactId>
   <version>X.Y.Z</version>
</dependency>
```

사용자 인터페이스는 다음과 같이 제공된다.

```xml
<dependency>
   <groupId>org.apache.jena</groupId>
   <artifactId>jena-fuseki-ui</artifactId>
   <version>X.Y.Z</version>
</dependency>
```

## 4. OWL

### 1) Jena Ontology API

#### (1) RDFS
RDFS는 Jena에서 지원하는 가장 약한 온톨로지 언어이다. RDFS를 사용하면 온톨로지스트는 개념의 간단한 계층 구조와 속성의 계층 구조를 구축할 수 있다.

![simple-hierarchy](/assets/img/2026-03-25-apache-jena/simple-hierarchy.png)

#### (2) OWL
일반적으로 OWL은 관계형 필드 정의(RDFS)에서 허용하는 모든 것을 표현할 수 있을 뿐만 아니라 훨씬 더 많은 것을 표현할 수 있게 해준다.

OWL의 핵심은 클래스를 더욱 흥미롭고 복잡한 방식으로 표현할 수 있다는 점이다.

OWL의 속성에 대해 자세히 설명하면, RDFS에서는 속성 계층 구조를 통해 속성 간의 관계를 설정할 수 있다.

OWL은 속성을 전이적, 대칭적 또는 함수적으로 지정할 수 있도록 확장했으며, 한 속성이 다른 속성의 역속성으로 선언될 수 있도록 했다.

또한 OWL은 속성을 개체(RDF 리소스)로 정의하는 속성과 데이터 값(RDF 용어로는 리터럴)으로 정의하는 속성을 구분한다.

OWL 언어는 OWL2 Full, OWL2 DL, OWL2 RL, OWL2 EL, OWL2 QL, 그리고 OWL1 Lite, OWL1 DL, OWL1 Full과 같은 여러 구문 클래스로 세분화된다. 이 중 OWL1 Lite, OWL1 DL, OWL1 Full은 현재 사용이 권장되지 않는다.

OWL2 EL, OWL2 QL, OWL2 RL은 OWL2 Full과 OWL2 DL에서 허용되는 일부 구문을 허용하지 않는다. OWL1은 사용이 권장되지 않지만, Jena Ontology API는 여전히 OWL1을 지원한다.

OWL2 RL, EL, QL, 그리고 OWL1 Lite, OWL1 DL의 목적은 해당 구문 집합을 사용한 추론 작업을 보다 쉽게 ​​수행할 수 있도록 하는 것이다.

구체적으로, OWL(1 & 2) DL은 기술 논리 추론기가 효율적으로 처리할 수 있도록 설계되었다.

OWL1 Lite는 다양한 비교적 간단한 추론 알고리즘으로 처리할 수 있도록 설계되었지만, 해당 분야 전문가들은 이러한 목표 달성 여부에 대해 의문을 제기해 왔다.

OWL 2 EL은 속성 또는 클래스 수가 매우 많은 온톨로지를 사용하는 응용 프로그램에 특히 유용하다.

EL이라는 약어는 존재량화만을 제공하는 기술 논리 EL 계열에 기반을 두고 있음을 반영한다.

OWL 2 QL은 매우 많은 양의 인스턴스 데이터를 사용하고 질의 응답이 가장 중요한 추론 작업인 애플리케이션을 대상으로 한다. QL이라는 약어는 이 프로파일에서 질의 응답을 표준 관계형 쿼리 언어로 재작성하여 구현할 수 있다는 사실을 반영한다.

OWL 2 RL은 표현력을 크게 희생하지 않고 확장 가능한 추론이 필요한 애플리케이션을 대상으로 한다. RL이라는 약어는 이 프로파일에서 추론을 표준 규칙 언어를 사용하여 구현할 수 있다는 사실을 반영한다.

OWL 표준 문서에서는 OWL이 (개정된) RDF 사양을 기반으로 구축되었다고 명시하고 있지만, OWL을 RDF 기반이 아닌 독립적인 언어로 간주하는 것도 가능하다.

이 관점에서는 RDF를 직렬화 구문으로 사용하고, RDF 중심적 관점에서는 RDF 트리플을 OWL 형식의 핵심으로 간주한다. 두 관점 모두 타당하지만, Jena에서는 RDF 중심적 관점을 따른다.

#### (3) Ontology languages and the Jena Ontology API
Jena 온톨로지 API는 언어에 구애받지 않는다. Java 클래스 이름은 특정 언어에 국한되지 않는다.

Jena에서 온톨로지를 사용할 때, 모든 상태 정보는 RDF 모델에 저장된 RDF 트리플(Jena Statement로 접근 가능)로 인코딩된 상태로 유지된다.

온톨로지 API는 온톨로지의 RDF 표현을 변경하지 않는다. 대신, 기본 RDF 트리플을 조작하는 프로그램을 더 쉽게 작성할 수 있도록 편의 클래스와 메서드 세트를 추가한다.

#### (4) Ontologies and reasoning
Jena 추론기의 공통적인 특징은 추론에서 도출된 트리플과 기본 모델에서 주장된 트리플을 모두 포함하는 것처럼 보이는 새로운 RDF 모델을 생성한다는 것이다.

이 확장된 모델은 여전히 ​​Jena 모델의 계약을 준수한다. 따라서 추론 모델이 아닌 일반 모델을 사용할 수 있는 모든 곳에서 확장된 추론 모델을 사용할 수 있다.

온톨로지 API는 이러한 특징을 활용하여, 온톨로지 API에서 제공하는 편의 메서드를 통해 일반 RDF 모델을 쿼리하는 것과 동일한 방식으로 확장된 추론 모델을 쿼리할 수 있다.

![ont-model-layers](/assets/img/2026-03-25-apache-jena/ont-model-layers.png)

Graph는 RDF 트리플 집합의 구성을 지원하는 Jena의 내부 인터페이스이다. 온톨로지 문서에서 읽어온 어설션 문장은 기본 그래프에 저장된다.

추론 엔진은 기본 그래프의 내용과 언어의 의미 규칙을 사용하여 기본 트리플과 파생 트리플의 보다 완전한 집합을 표시할 수 있다.

이 또한 Graph 인터페이스를 통해 제공되므로 OntModel은 가장 바깥쪽 인터페이스와만 상호 작용한다.

이러한 규칙성 덕분에 추론 엔진을 사용하거나 사용하지 않고도 온톨로지 모델을 매우 쉽게 구축할 수 있다.

또한 기본 그래프는 메모리 저장소, 데이터베이스 기반 영구 저장소 또는 LDAP 디렉터리와 같은 다른 저장 구조일 수 있으며, 이는 온톨로지 모델의 작동에 영향을 미치지 않는다(단, 이러한 다양한 접근 방식은 효율성 측면에서 큰 차이가 있을 수 있다).

### 2) Inference API

#### (1) Inference Support
Jena 추론 서브시스템은 다양한 추론 엔진 또는 추론기를 Jena에 연결할 수 있도록 설계되었다.

이러한 엔진은 기본 RDF와 선택적 온톨로지 정보, 그리고 추론기와 관련된 공리 및 규칙을 기반으로 추가적인 RDF 어설션을 도출하는데 사용된다.

이 메커니즘의 주요 용도는 인스턴스 데이터와 클래스 설명에서 추가적인 사실을 추론할 수 있는 RDFS 및 OWL과 같은 언어 사용을 지원하는 것이다.

그러나 이 시스템은 매우 범용적으로 설계되었으며, 특히 다양한 RDF 처리 또는 변환 작업에 사용할 수 있는 범용 규칙 엔진을 포함한다.

![reasoner-overview](/assets/img/2026-03-25-apache-jena/reasoner-overview.png)

추론기 API는 bindSchema 호출을 사용하여 추론기를 스키마 또는 온톨로지 데이터 집합에 바인딩함으로써 추론기를 특수화하는 개념을 지원한다.

특수화된 추론기는 bind 호출을 사용하여 다양한 인스턴스 데이터 집합에 연결할 수 있다.

동일한 스키마 정보를 서로 다른 인스턴스 데이터 집합과 함께 여러 번 사용해야 하는 경우, 이 기법을 통해 스키마의 다양한 사용 사례에서 추론 결과를 재사용할 수 있다.

RDF에서는 스키마(온톨로지 또는 tbox라고도 함) 데이터와 인스턴스(abox라고도 함) 데이터 간에 명확한 구분이 없으므로 클래스 또는 인스턴스 관련 데이터라면 어떤 데이터든 bind 또는 bindSchema 호출에 포함될 수 있다.

이러한 명칭은 제한적인 것이 아니라 암시적인 의미를 지닌다.

#### (2) Available Reasoners

* 전이 추론기: 클래스 및 속성 격자를 저장하고 순회하는 기능을 지원한다. 이 추론기는 rdfs:subPropertyOf 및 rdfs:subClassOf의 전이적 및 반사적 속성만 구현한다.

* RDFS 규칙 추론기: RDFS 함의의 구성 가능한 하위 집합을 구현한다.

* OWL, OWL Mini, OWL Micro 추론기: OWL/Full 언어의 OWL/Lite 하위 집합에 대한 유용하지만 불완전한 구현 모음이다.

* 일반 규칙 추론기: 사용자 정의 규칙을 지원하는 규칙 기반 추론기이다. 순방향 추론, 테이블 기반 역방향 추론 및 하이브리드 실행 전략을 지원한다.

## [출처 및 참고]
* [https://jena.apache.org/index.html](https://jena.apache.org/index.html)
