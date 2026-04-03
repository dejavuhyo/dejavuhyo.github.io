---
title: ODRL Ontology
author: dejavuhyo
date: 2026-04-02 17:30:00 +0900
categories: [Language, Ontology]
tags: [ontology, odrl, open-digital-rights-language, 온톨로지]
---

## 1. ODRL Ontology
ODRL (Open Digital Rights Language) 온톨로지는 디지털 자산에 대한 권리(Rights), 의무(Obligations), 금지 사항(Prohibitions)을 표현하기 위한 국제 표준 정책 언어이다.

단순히 "복사 금지" 같은 텍스트가 아니라, 컴퓨터가 이해할 수 있는 기계 판독형(Machine-Readable) 형식을 제공하여 자동화된 라이선스 관리와 데이터 거버넌스를 가능하게 한다.

## 2. 핵심 모델 구조 (Core Model)
ODRL의 가장 큰 특징은 자산(Asset), 권한(Permission), 주체(Party) 간의 관계를 정의하는 것이다.

### 1) 정책 (Policy)
모든 ODRL 문장의 최상위 컨테이너이다. 정책의 성격에 따라 종류가 나뉜다.

* Offer: 서비스 제공자가 제안하는 조건 (예: "이 사진을 10달러에 쓸 수 있습니다.")

* Agreement: 양자 간의 합의 (예: "A는 B의 사진을 사용할 권리가 있습니다.")

* Privacy: 개인정보 처리 방침 정의

### 2) 자산 (Asset)
권리의 대상이 되는 디지털 또는 물리적 콘텐츠이다.

* Relation: 자산이 정책에서 어떤 역할을 하는지 정의한다 (주로 target).

### 3) 주체 (Party)
권리를 행사하거나 의무를 지는 개인, 조직, 혹은 소프트웨어 에이전트이다.

* Assigner: 권리를 부여하는 주체 (권리 소유자).

* Assignee: 권리를 부여받는 주체 (이용자).

### 4) 규칙 (Rule)
정책의 핵심으로, 세 가지 유형이 있다.

* Permission (허용): 수행할 수 있는 작업 (예: play, display).

* Prohibition (금지): 해서는 안 되는 작업 (예: modify).

* Duty (의무): 허용된 권리를 행사하기 위해 반드시 수행해야 하는 작업 (예: pay, attribute).

## 3. 제약 조건 (Constraint)
단순히 "복사 허용"이 아니라, "특정 상황에서의 허용"을 정의하기 위해 Constraint를 사용한다.

* Count: 실행 횟수 제한 (예: 10회 출력).

* DateTime: 유효 기간 설정 (예: 2026년 말까지).

* Spatial: 지리적 제한 (예: 한국 내에서만 허용).

* Operator: 논리 연산자 (`eq`, `lt`, `gte` 등)를 사용하여 정교한 조건을 만든다.

## 4. ODRL 사용 이유

* 상호운용성: 서로 다른 시스템 간에 라이선스 정보를 표준화된 방식으로 주고받을 수 있다.

* 자동화: AI나 스마트 계약(Smart Contracts)이 인간의 개입 없이 정책을 해석하고 위반 여부를 판단할 수 있다.

* 유연성: 단순 저작권뿐만 아니라 개인정보 보호 정책(GDPR 대응), 데이터 시장의 거래 규칙 등 확장성이 매우 뛰어나다.

## 5. IAM/RBAC과 ODRL 차이
IAM이나 RBAC 시스템은 "누가(Role) 어떤 자원(Resource)에 접근할 수 있는가"라는 이분법적(Yes/No) 결정에 집중한다.

반면 ODRL은 그 결정을 내리기 위한 상세한 조건과 맥락을 설명하는데 특화되어 있다.

| 구분 | IAM / RBAC (일반적) | ODRL (온톨로지 기반) |
|:-----:|:-----:|:-----:|
| 주요 질문 | "관리자 권한이 있는가?" | "이 자원을 어떤 조건으로 사용할 수 있는가?" |
| 제어 수준거친 제어 (Coarse-grained) | 정밀한 제어 (Fine-grained) |
| 표현력 | Allow / Deny 중심 | Permission, Prohibition, Duty(의무) |
| 맥락(Context) | 정적 (Role 기반) | 동적 (시간, 횟수, 위치, 결제 여부 등) |

## 6. ODRL 사용 환경

* 단순 "접근"을 넘어 "이용 행위"를 세분화할 때

* "의무(Duty)"와 "사후 조건"이 강제되어야 할 때

* 데이터가 조직 외부로 유통될 때 (Data Ecosystem)

* 계약 조건이 수시로 변하는 동적 환경 (Dynamic Policy)

단순한 "보안"이 목적이라면 기존 IAM이 빠르고 효율적이다. 하지만 서비스가 "데이터를 자산으로 보고, 그 자산의 가치와 권리를 정밀하게 추적/거래"해야 한다면 ODRL을 사용할 필요가 있다.

## [출처 및 참고]
* [https://www.w3.org/ns/odrl/2/](https://www.w3.org/ns/odrl/2/)
* [https://www.w3.org/TR/odrl-vocab/](https://www.w3.org/TR/odrl-vocab/)
* [https://www.w3.org/TR/odrl-vocab/](https://www.w3.org/TR/odrl-vocab/)
* [https://github.com/w3c/odrl](https://github.com/w3c/odrl)
