---
title: Spring Boot Batch 속성
author: dejavuhyo
date: 2022-06-07 12:10:00 +0900
categories: [Application, Framework]
tags: [spring-boot-batch-properties, spring-batch-properties, batch-properties]
---

## 1. Batch 프로퍼티 통합 속성

| 이름 | 설명 | 기본값 |
|-----|-----|-----|
| spring.batch.jdbc.initialize-schema | 데이터베이스 스키마 초기화 모드 | embedded |
| spring.batch.jdbc.isolation-level-for-create | 새 작업에 대한 작업 메타데이터를 생성할 때 사용할 트랜잭션 격리 수준이다. JPA 사용 여부에 따라 자동 감지된다. |  |
| spring.batch.jdbc.platform | `@@platform@@` 자리 표시 자가 사용되는 경우 초기화 스크립트에서 사용할 플랫폼이다. 기본적으로 자동 감지된다. |  |
| spring.batch.jdbc.schema | 데이터베이스 스키마를 초기화하는데 사용할 SQL 파일의 경로dl다. | `classpath:org/quartz/impl/jdbcjobstore/tables_@@platform@@.sql` |
| spring.batch.jdbc.table-prefix | 모든 배치 메타데이터 테이블에 대한 테이블 접두사 |  |
| spring.batch.job.enabled | 시작시 컨텍스트에서 모든 Spring Batch 작업을 실행한다. | true |
| spring.batch.job.names | 시작시 실행할 작업 이름의 쉼표로 구분된 목록이다(예: 'job1,job2'). 기본적으로 컨텍스트에서 찾은 모든 작업이 실행된다. |  |

## [출처 및 참고]
* <https://docs.spring.io/spring-boot/docs/current/reference/html/application-properties.html#appendix.application-properties.integration>
