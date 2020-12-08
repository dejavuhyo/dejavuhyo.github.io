---
title: Spring Boot Scheduler 설정
author: Hyosik
date: 2020-10-27 15:40:00 +0900
categories: [Application, Architecture]
tags: [scheduler, springboot_scheduler, 스프링부트_스케쥴러, 스케쥴러]
---

## 1. 스케쥴러 사용
Application 클레스에 @EnableScheduling Annotation을 추가하면 스케쥴러 기능을 사용할 수 있다.

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.scheduling.annotation.EnableScheduling;

@SpringBootApplication
@EnableScheduling
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }

}
```

## 2. 속성

* **cron:** cron 표현식을 지원한다. "초 분 시 일 월 주 (년)"으로 표현한다.
* **fixedDelay:** milliseconds 단위로, 이전 작업이 끝난 시점으로부터 고정된 시간을 설정한다.
* **fixedDelayString:** fixedDelay와 같은데 property의 value만 문자열로 넣는 것이다.
* **fixedRate:** milliseconds 단위로, 이전 작업이 수행되기 시작한 시점으로부터 고정된 시간을 설정한다.
* **fixedRateString:** fixedDelay와 같은데 property의 value만 문자열로 넣는 것이다.
* **initialDelay:** 스케쥴러에서 메서드가 등록되자마자 수행하는 것이 아닌 초기 지연시간을 설정하는 것이다.
* **initialDelayString:** 위와 마찬가지로 문자열로 값을 표현하겠다는 의미다.
* **zone:** cron 표현식을 사용했을 때 사용할 time zone으로 따로 설정하지 않으면 기본적으로 서버의 time zone이다.

## 3. cron 속성 사용

### 1) cron 구성

| * | * | * | * | * | * |
|:---|:---|:---|:---|:---|:---|
| 초(0-59) | 분(0-59) | 시간(0-23) | 일(1-31) | 월(1-12) | 요일(0-7) |

* 요일에서 0과 7은 일요일이며, 1부터 월요일이고 6이 토요일이다.
* [CronMaker](http://www.cronmaker.com/) 사이트에서 cron 표현식을 생성할 수 있다.

### 2) 구현

```java
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

import java.text.SimpleDateFormat;
import java.util.Date;

@Component
public class Schedule {

    @Scheduled(cron = "0 0/1 * * * ?")
    public void cronRun() {
        SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        System.out.println("### Cron Run 1 Minutes : " + simpleDateFormat.format(new Date()));
    }
}
```

### 3) 결과

```text
### Cron Run 1 Minutes : 2020-10-27 15:39:00
### Cron Run 1 Minutes : 2020-10-27 15:40:00
### Cron Run 1 Minutes : 2020-10-27 15:41:00
```

## 4. fixedDelay 속성 사용

### 1) 구현

```java
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

import java.text.SimpleDateFormat;
import java.util.Date;

@Component
public class Schedule {

    @Scheduled(fixedDelay = 5000)
    public void fixedDelayRun() {
        SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        System.out.println("### Fixed Delay Run 5 Seconds : " + simpleDateFormat.format(new Date()));
    }
}
```

### 2) 결과

```text
### Fixed Delay Run 5 Seconds : 2020-10-27 15:55:17
### Fixed Delay Run 5 Seconds : 2020-10-27 15:55:22
### Fixed Delay Run 5 Seconds : 2020-10-27 15:55:27
### Fixed Delay Run 5 Seconds : 2020-10-27 15:55:32
### Fixed Delay Run 5 Seconds : 2020-10-27 15:55:37
### Fixed Delay Run 5 Seconds : 2020-10-27 15:55:42
### Fixed Delay Run 5 Seconds : 2020-10-27 15:55:47
```

## [출처 및 참고]
* <https://jeong-pro.tistory.com/186>
* <http://jmlim.github.io/spring/2018/11/27/spring-boot-schedule/>
* <http://www.cronmaker.com/>
