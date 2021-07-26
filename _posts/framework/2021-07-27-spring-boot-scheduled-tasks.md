---
title: Spring Boot 스케줄 작업
date: 2021-07-27 06:00:00 +0900
categories: [Application, Framework]
tags: [spring-boot-scheduled-tasks, spring-boot-scheduled, scheduled-tasks, spring-scheduled-tasks spring-boot-스케줄-작업, spring-boot-스케줄, 스케줄-작업]
---

## 1. 스케줄 시간대 변경
기본적으로 Spring은 cron 표현식에 서버의 로컬 시간대를 사용한다. 그러나 zone 속성을 사용하여 이 시간대를 변경할 수 있다.

```text
@Scheduled(cron = "0 * * * * ?", zone = "Asia/Seoul")
```

## 2. 스케줄 매개 변수 지정
이러한 스케줄을 하드 코딩하는 것은 간단하지만, 일반적으로 전체 앱을 다시 컴파일하고 다시 배포하지 않고도 스케줄을 제어할 수 있어야 한다.

Spring Expressions를 사용하여 태스크의 구성을 외부화하고 속성 파일에 저장한다.

### 1) fixedDelay

```text
@Scheduled(fixedDelayString = "${fixedDelay.in.milliseconds}")
```

### 2) fixedRate

```text
@Scheduled(fixedRateString = "${fixedRate.in.milliseconds}")
```

### 3) cron expression based

```text
@Scheduled(cron = "${cron.expression}")
```

## [출처 및 참고]
* <https://www.baeldung.com/spring-scheduled-tasks>
