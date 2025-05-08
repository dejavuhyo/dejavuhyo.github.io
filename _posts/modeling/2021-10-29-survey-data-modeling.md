---
title: 설문조사 데이터 모델링
author: dejavuhyo
date: 2021-10-29 06:00:00 +0900
categories: [DevOps, Modeling]
tags: [survey-data-modeling, survey-modeling, survey-erd, 설문조사-데이터-모델링, 설문조사-모델링, 설문조사-erd]
---

## 1. 비즈니스 규칙

* 한 사람이 제로-하나 또는 다수의 설문조사에 등록

* 설문조사는 0명 또는 다수의 사용자를 등록

* 설문조사는 일대다 질문으로 통합

* 질문에는 제로-하나 또는 다수의 설문조사가 통합

* 질문에 대해 하나 또는 여러 개의 응답을 받지 못함

* 응답은 정확히 한 명의 사람이 정확히 한 명의 설문 조사 상황에서 제공

## 2. 다이어그램

![survey-erd](/assets/img/2021-10-29-survey-data-modeling/survey-erd.png)

### 1) PersonSurvey 관계
PersonSurvey 관계는 Person이 주어진 Survey에 참여할 수 있도록 승인 수단을 제공하는데 필요하다. 이러한 방식으로 특정 사람이 특정 설문조사에 등록되면 해당 설문조사를 통합하는 질문에 대한 응답을 제공할 수 있는 권한이 부여된다.

### 2) SurveyQuestion 관계
다이어그램에서 suvery_question.question_number라는 속성이 특정 설문조사와 관련하여 주어진 질문 인스턴스의 표시 순서를 나타내는데 사용 된다고 가정한다. SurveyQuestion.PresentationOrder와 같은 속성을 표시했으며 둘 이상의 Question.QuestionNumber 값이 같은 SurveyQuestion 발생에서 동일한 PresentationOrder 값을 공유하는 것을 방지해야 한다.

이러한 요구를 표현하기 위해 속성 조합(SurveyNumber, QuestionNumber, PresentationOrder)으로 구성된 이 엔터티 유형을 나타내는 상자에 복합 ALTERNATE KEY(AK)를 포함했습니다. 잘 알고 있듯이 복합 AK는 다중 열 UNIQUE 제약 조건을 사용하여 논리적 DDL 설계에서 선언할 수 있다.

### 3) 응답 개체 유형
응답 엔터티 유형을 사용하여 다른 두 관계 간의 관계를 묘사하고 있습니다. 처음에는 어색해 보일 수 있지만, (a) 관심 있는 비즈니스 컨텍스트의 특징을 정확하게 나타내고 (b) 논리 수준 레이아웃에서 적절하게 표현한다면 이 접근 방식에는 아무런 문제가 없다.

### 4) Response 테이블에 있는 두 개의 복합 FOREIGN KEY
주어진 Response 행에서 SurveyQuestion.SurveyNumber, 그리고 SurveyPerson.SurveyNumber와 일치하는 값이 있어야 한다. 이 조건을 선언적 방식으로 적용하는 가장 좋은 방법은 두 개의 복합 FOREIGN KEY(FK)를 사용하는 것이다.

DDL 설계에서 볼 수 있듯이, 제 FK가 참조하고있다 PersonSurvey 테이블 PRIMARY KEY (PK), 즉, (PersonId, SurveyNumber) 응답 열에 의해 준수된다. Response.PersonId과 Response.SurveyNumber.

FK가 가리키는 SurveyQuestion 즉, 테이블 PK (SurveyNumber, QuestionNumber)를 가리키며, 따라서 응답 열로 구성된다. Response.SurveyNumber하고 Response.QuestionNumber.

이러한 방식으로 Response.SurveyNumber열은 두 가지 다른 제약 조건에서 FK 참조의 일부로 사용되기 때문에 매우 유용하다.

이 방법을 사용하면 데이터베이스 관리 시스템 보증 참조 무결성이 다음으로부터 보장된다.

* Response 받는 PersonSurvey

* Response 받는 SurveyQuestion

* 연관 엔티티 유형을 나타내는 각 테이블과 독립 엔티티 유형을 나타내는 테이블, 즉 Person, Survey 및 Question

## 3. SQL DDL

```sql
CREATE TABLE Person (
    PersonId        INT      NOT NULL,
    FirstName       CHAR(30) NOT NULL,
    LastName        CHAR(30) NOT NULL,
    GenderCode      CHAR(3)  NOT NULL,
    BirthDate       DATE     NOT NULL,
    CreatedDateTime DATETIME NOT NULL,

    CONSTRAINT Person_PK PRIMARY KEY (PersonId),
    CONSTRAINT Person_AK UNIQUE      (
        FirstName,
        LastName,
        GenderCode,
        BirthDate
    )
);

CREATE TABLE Survey (
    SurveyNumber    INT       NOT NULL,
    Description     CHAR(255) NOT NULL,
    CreatedDateTime DATETIME  NOT NULL,
    --
    CONSTRAINT Survey_PK PRIMARY KEY (SurveyNumber),
    CONSTRAINT Survey_AK UNIQUE      (Description)
);

CREATE TABLE PersonSurvey (
    PersonId           INT      NOT NULL,
    SurveyNumber       INT      NOT NULL,
    RegisteredDateTime DATETIME NOT NULL,

    CONSTRAINT PersonSurvey_PK         PRIMARY KEY (PersonId, SurveyNumber),
    CONSTRAINT PersonSurveyToPerson_FK FOREIGN KEY (PersonId)
        REFERENCES Person (PersonId),
    CONSTRAINT PersonSurveyToSurvey_FK FOREIGN KEY (SurveyNumber)
        REFERENCES Survey (SurveyNumber)
);

CREATE TABLE Question (
    QuestionNumber  INT       NOT NULL,
    Wording         CHAR(255) NOT NULL,
    CreatedDateTime DATETIME  NOT NULL,

    CONSTRAINT Question_PK PRIMARY KEY (QuestionNumber),
    CONSTRAINT Question_AK UNIQUE      (Wording)
);

CREATE TABLE SurveyQuestion (
    SurveyNumber       INT      NOT NULL,
    QuestionNumber     INT      NOT NULL,
    PresentationOrder  TINYINT  NOT NULL,
    IsMandatory        BIT      NOT NULL,
    IntegratedDateTime DATETIME NOT NULL,

    CONSTRAINT SurveyQuestion_PK PRIMARY KEY (SurveyNumber, QuestionNumber),
    CONSTRAINT SurveyQuestion_AK UNIQUE      (
        QuestionNumber,
        SurveyNumber,
        PresentationOrder
    ),
    CONSTRAINT SurveyQuestionToSurvey_FK   FOREIGN KEY (SurveyNumber)
        REFERENCES Survey   (SurveyNumber),
    CONSTRAINT SurveyQuestionToQuestion_FK FOREIGN KEY (QuestionNumber)
        REFERENCES Question (QuestionNumber)
);

CREATE TABLE Response (
    SurveyNumber     INT      NOT NULL,
    QuestionNumber   INT      NOT NULL,
    PersonId         INT      NOT NULL,
    Content          TEXT     NOT NULL,
    ProvidedDateTime DATETIME NOT NULL,

    CONSTRAINT Response_PK                 PRIMARY KEY (SurveyNumber, QuestionNumber, PersonId),
    CONSTRAINT ResponseToPersonSurvey_FK   FOREIGN KEY (PersonId, SurveyNumber)
        REFERENCES PersonSurvey   (PersonId, SurveyNumber),
    CONSTRAINT ResponseToSurveyQuestion_FK FOREIGN KEY (SurveyNumber, QuestionNumber)
        REFERENCES SurveyQuestion (SurveyNumber, QuestionNumber)
);
```

## [출처 및 참고]
* [https://qastack.kr/dba/120246/best-data-modelling-approach-to-handle-redundant-foreign-keys-in-a-database-abou](https://qastack.kr/dba/120246/best-data-modelling-approach-to-handle-redundant-foreign-keys-in-a-database-abou)
