---
title: Observability에서 Metric, Log, Trace
author: dejavuhyo
date: 2026-06-16 11:00:00 +0900
categories: [DevOps, Observability]
tags: [observability, metric, log, trace]
---

## 1. Metric, Log, Trace
시스템 모니터링과 운영에서 오브저버빌리티(Observability)의 핵심은 메트릭(Metric), 로그(Log), 트레이스(Trace)이다.

시스템에 문제가 생겼을 때 언제(메트릭), 무엇이(로그), 어디서(트레이스) 발생했는지를 알려주는 역할을 한다.

## 2. 메트릭 (Metric) - 시스템의 건강 상태 (숫자 데이터)
메트릭은 일정 시간 동안 시스템에서 수집된 시계열(Time-series) 숫자 데이터이다.

시스템이 지금 잘 돌아가고 있는지 전체적인 요약본을 보여준다.

* 특징: 데이터 구조가 단순하고 용량이 작아서 장기간 보관하기 좋다. 실시간 알림(Alerting)을 설정하기에 가장 적합하다.

* 알림: 문제가 언제 발생했는가?, 시스템에 과부하가 걸렸는가?

* 주요 예시
  - CPU 사용률 85%, 메모리 잔여량 1.2GB
  - 초당 요청 수 (RPS - Requests Per Second)
  - 에러율 (Error Rate) 2%

## 3. 로그 (Log) - 무슨 일이 일어났는가? (기록 데이터)
로그는 특정 이벤트가 발생했을 때 시스템이 남기는 타임스탬프가 찍힌 텍스트 기록이다. 개발자에게 가장 익숙한 형태의 데이터이기도 하다.

* 특징: 상세한 문맥(Context)을 담고 있어 구체적인 원인을 파악할 때 유용하다. 데이터 양이 엄청나게 방대해질 수 있어 저장 비용이 많이 든다.

* 알림: 정확히 무슨 일이 왜 일어났는가?

* 주요 예시
  - 2026-06-16 10:15:30 [ERROR] User ID 1234 결제 실패: 잔액 부족
  - 2026-06-16 10:15:32 [INFO] 데이터베이스 연결 성공

## 4. 트레이스 (Trace) - 어디서 막혔는가? (경로 데이터)
트레이스는 분산 시스템(예: 마이크로서비스 아키텍처)에서 하나의 요청(Request)이 여러 서비스와 컴포넌트를 거쳐 처리되는 전체 여정을 추적하는 데이터이다.

* 특징: 요청이 A 서비스 → B 서비스 → DB로 이동할 때 각 구간에서 걸린 시간(Latency)과 성공 여부를 시각적으로 보여준다. 각 구간의 기록을 스팬(Span)이라고 부르며, 이 스팬들이 모여 하나의 트레이스를 이룬다.

* 알림: 요청이 어디서 지연되거나 실패했는가?

* 주요 예시
  - 사용자가 주문 버튼을 누른 후 응답까지 3초가 걸림
  - 트레이스 분석 결과: API 게이트웨이(0.1초) → 주문 서비스(0.2초) → 결제 외부 API 호출(2.7초 지연)

## [출처 및 참고]
* [https://www.elastic.co/blog/3-pillars-of-observability](https://www.elastic.co/blog/3-pillars-of-observability)
* [https://www.ibm.com/think/insights/observability-pillars](https://www.ibm.com/think/insights/observability-pillars)
* [https://www.crowdstrike.com/en-us/cybersecurity-101/observability/three-pillars-of-observability/](https://www.crowdstrike.com/en-us/cybersecurity-101/observability/three-pillars-of-observability/)
* [https://www.strongdm.com/blog/three-pillars-of-observability](https://www.strongdm.com/blog/three-pillars-of-observability)
