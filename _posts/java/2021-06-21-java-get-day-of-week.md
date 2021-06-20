---
title: Java 날짜로 요일 구하기
date: 2021-06-21 06:00:00 +0900
categories: [Language, Java]
tags: [java-get-day-of-week, get-day-of-week, day-of-week, 자바-요일-구하기, 요일-구하기, 요일]
---

## 1. Joda Time

```java
import org.joda.time.DateTime;
import org.joda.time.format.DateTimeFormat;
import org.joda.time.format.DateTimeFormatter;

public class JodaTime {

    public static void main(String[] args) {
        DateTime dateTime = new DateTime();
        DateTimeFormatter dtf = DateTimeFormat.forPattern("YYYY-MM-dd HH:mm:ss");

        String today = dtf.print(dateTime);
        int dayOfWeek = dateTime.getDayOfWeek();

        String dayOfWeekStr = "";
        switch (dayOfWeek) {
            case 1:
                dayOfWeekStr = "월요일";
                break;
            case 2:
                dayOfWeekStr = "화요일";
                break;
            case 3:
                dayOfWeekStr = "수요일";
                break;
            case 4:
                dayOfWeekStr = "목요일";
                break;
            case 5:
                dayOfWeekStr = "금요일";
                break;
            case 6:
                dayOfWeekStr = "토요일";
                break;
            case 7:
                dayOfWeekStr = "일요일";
                break;
        }
        System.out.println(today);
        System.out.println(dayOfWeekStr);
    }
}
```

## 2. Java 8

```java
import java.time.DayOfWeek;
import java.time.LocalDate;

public class Java8 {

    public static void main(String[] args) {
        LocalDate localDate = LocalDate.now();
        DayOfWeek dayOfWeek = localDate.getDayOfWeek();

        System.out.println(localDate);
        System.out.println(dayOfWeek);
    }
}
```

## [출처 및 참고]
* <https://springboot.tistory.com/42?category=620230>
