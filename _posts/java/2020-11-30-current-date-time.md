--- 
title: 현재시간, 날짜 구하기
author: Hyosik
date: 2020-11-30 13:00:00 +0900
categories: [Language, Java]
tags: [current_date_time, date_time, date, time, 현재_시간_날짜, 시간_날짜]
---

## 1. Date 클래스 활용

```java
import java.text.SimpleDateFormat;
import java.util.Date;

public class CurrentDateTime {

    public static void main(String[] args) {

        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd hh:mm:ss");

        Date date = new Date();

        String currentDt = sdf.format(date);
        System.out.println(currentDt);
    }
}
```

## 2. Calendar 클래스 활용
Calendar 클래스가 추가되면서 Date 클래스는 대부분의 메소드가 deprecated 되었다.

* getInstance() 메서드 활용

```java
import java.text.SimpleDateFormat;
import java.util.Calendar;
import java.util.Date;

public class CurrentDateTime {

    public static void main(String[] args) {

        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd hh:mm:ss");
    
        // 에러: 추상클래스는 인스턴스를 생성할 수 없다.
        // Calendar calendar = new Calendar();
        
        // getInstance()는 Calendar 클래스를 구현한 클래스의 인스턴스를 반환
        Calendar calendar = Calendar.getInstance();
        calendar.setTime(new Date());

        String current = sdf.format(calendar.getTime());
        System.out.println(current);
    }
}
```

## 3. System 클래스 활용

* currentTimeMillis() 메서드 활용

```java
import java.text.SimpleDateFormat;

public class CurrentDateTime {

    public static void main(String[] args) {

        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd hh:mm:ss");

        String currentDt = sdf.format(System.currentTimeMillis());
        System.out.println(currentDt);
    }
}
```

## 4. StringBuffer로 날짜, 시간 출력

```java
import java.text.FieldPosition;
import java.text.SimpleDateFormat;
import java.util.Date;

public class CurrentDateTime {

    public static void main(String[] args) {

        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd hh:mm:ss");

        StringBuffer stringBuffer = new StringBuffer();
        Date date = new Date();

        sdf.format(date, stringBuffer, new FieldPosition(0));

        System.out.println(sb);
    }
}
```

## 5. 패턴

| 패턴 | 설명 |
|:---:|:---:|
| G | 연대(BC, AD)
| y | 년도
| M | 월(1~12)
| w | 해당 년도의 몇 번째 주(1~53)
| W | 해당 월의 몇 번째 주(1~5)
| D | 해당 연도의 몇 번째 일(1~366)
| d | 해당 월의 몇 번째 일(1~31)
| F | 해당 월의 몇 번째 요일(1~5)
| E | 요일(월~일)
| a | 오전/오후(AM, PM)
| H | 시간(0~23)
| h | 시간(1~12)
| K | 시간(0~11)
| k | 시간(1~24)
| m | 분(0~59)
| s | 초(0~59)
| S | 1/1000초(0~999)
| Z | 타임존
| z | 타임존(RFC 822)

## [출처 및 참고]
* <https://coding-factory.tistory.com/259>
* <https://ryan-han.com/post/java/java-calendar-date/>
* <https://the-illusionist.me/41>
