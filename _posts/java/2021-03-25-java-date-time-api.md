---
title: Java 날짜와 시간 API
author: dejavuhyo
date: 2021-03-25 06:10:00 +0900
categories: [Language, Java]
tags: [java-date, java-time, java-date-time, sr-310, joda-time, java-날짜, java-시간, java-날짜-시간]
---

## 1. 날짜와 시간 API
Java의 기본 SDK에서 날짜와 시간을 다루는 java.util.Date 클래스와 java.util.Calendar 클래스는 사용하기 불편하기 때문에, 이 클래스를 대체하려고 Joda-Time 같은 오픈소스 라이브러리도 있다. 그러나 JDK 8에서는 개선된 날짜와 시간 API가 제공되고 있다.

## 2. JDK 기본 날짜 클래스의 문제점

* 불변 객체가 아니다(not immutable)

* int 상수 필드의 남용

* 헷갈리는 월 지정

* 일관성 없는 요일 상수

* Date와 Calendar의 불편한 역할 분담

* 오류에 둔감한 시간대 ID지정

* java.util.Date 하위 클래스의 문제

## 3. 기본 JDK 클래스 날짜 연산 테스트

```java
import static org.fest.assertions.api.Assertions.*;

import java.text.SimpleDateFormat;
import java.util.Calendar;
import java.util.Date;
import java.util.TimeZone;

import org.junit.Test;

public class OldJdkDateTest {
    @Test
    public void shouldGetAfterOneDay() {
        TimeZone utc = TimeZone.getTimeZone("UTC");
        Calendar calendar = Calendar.getInstance(utc);
        calendar.set(1582, Calendar.OCTOBER , 4);
        String pattern = "yyyy.MM.dd";
        String theDay = toString(calendar, pattern, utc);
        assertThat(theDay).isEqualTo("1582.10.04");
        
        calendar.add(Calendar.DATE, 1);
        String nextDay = toString(calendar, pattern, utc);
        assertThat(nextDay).isEqualTo("1582.10.15");
    }
    
    @Test
    public void shouldGetAfterOneHour() {
        TimeZone seoul = TimeZone.getTimeZone("Asia/Seoul");
        Calendar calendar = Calendar.getInstance(seoul);
        calendar.set(1988, Calendar.MAY , 7, 23, 0);
        String pattern = "yyyy.MM.dd HH:mm";
        String theTime = toString(calendar, pattern, seoul);
        assertThat(seoul.inDaylightTime(calendar.getTime())).isFalse();
        assertThat(theTime).isEqualTo("1988.05.07 23:00");
        
        calendar.add(Calendar.HOUR_OF_DAY, 1);
        String after1Hour = toString(calendar, pattern, seoul);
        assertThat(seoul.inDaylightTime(calendar.getTime())).isTrue();
        assertThat(after1Hour).isEqualTo("1988.05.08 01:00");
    }
    
    @Test
    public void shouldGetAfterOneMinute() {
        TimeZone seoul = TimeZone.getTimeZone("Asia/Seoul");
        Calendar calendar = Calendar.getInstance(seoul);
        calendar.set(1961, Calendar.AUGUST, 9, 23, 59);
        String pattern = "yyyy.MM.dd HH:mm";
        String theTime = toString(calendar, pattern, seoul);
        assertThat(theTime).isEqualTo("1961.08.09 23:59");
        
        calendar.add(Calendar.MINUTE, 1);
        String after1Minute = toString(calendar, pattern, seoul);
        assertThat(after1Minute).isEqualTo("1961.08.10 00:30");
    }
    
    @Test
    public void shouldGetAfterTwoSecond() {
        TimeZone utc = TimeZone.getTimeZone("UTC");
        Calendar calendar = Calendar.getInstance(utc);
        calendar.set(2012, Calendar.JUNE, 30, 23, 59, 59);
        String pattern = "yyyy.MM.dd HH:mm:ss";
        String theTime = toString(calendar, pattern, utc);
        assertThat(theTime).isEqualTo("2012.06.30 23:59:59");
        
        calendar.add(Calendar.SECOND, 2);
        String afterTwoSeconds = toString(calendar, pattern, utc);
        assertThat(afterTwoSeconds).isEqualTo("2012.07.01 00:00:01");
    }
    
    @Test
    public void shouldSetGmtWhenWrongTimeZoneId(){
        TimeZone zone = TimeZone.getTimeZone("Seoul/Asia");
        assertThat(zone.getID()).isEqualTo("GMT");
    }
    
    private String toString(Calendar calendar, String pattern, TimeZone zone) {
        SimpleDateFormat format = new SimpleDateFormat(pattern);
        format.setTimeZone(zone);
        return format.format(calendar.getTime());
    }
    
    @Test
    public void shouldGetDate() {
        Calendar calendar = Calendar.getInstance();
        calendar.set(1999, 12, 31);
        assertThat(calendar.get(Calendar.YEAR)).isEqualTo(2000);
        assertThat(calendar.get(Calendar.MONTH)).isEqualTo(Calendar.JANUARY);
        assertThat(calendar.get(Calendar.DAY_OF_MONTH)).isEqualTo(31);
    }
    
    @Test (expected = IllegalArgumentException.class)
    public void shouldNotAcceptWrongMonth() {
        Calendar calendar = Calendar.getInstance();
        calendar.setLenient(false);
        calendar.set(1999, 12, 31);
        calendar.get(Calendar.MONTH);
    }
    
    @Test
    @SuppressWarnings("deprecation")
    public void shouldGetDayOfWeek() {
        Calendar calendar = Calendar.getInstance();
        calendar.set(2014, Calendar.JANUARY, 1);
        
        int dayOfWeek = calendar.get(Calendar.DAY_OF_WEEK);
        assertThat(dayOfWeek).isEqualTo(Calendar.WEDNESDAY);
        assertThat(dayOfWeek).isEqualTo(4);
        Date theDate = calendar.getTime();
        assertThat(theDate.getDay()).isEqualTo(3);
    }
}
```

## 4. Joda-Time 날짜 연산 테스트

```java
import static org.fest.assertions.api.Assertions.*;

import org.joda.time.Chronology;
import org.joda.time.DateTime;
import org.joda.time.DateTimeConstants;
import org.joda.time.DateTimeZone;
import org.joda.time.IllegalFieldValueException;
import org.joda.time.LocalDate;
import org.joda.time.chrono.GJChronology;
import org.joda.time.chrono.GregorianChronology;
import org.junit.Test;

public class JodaTimeTest {

    @Test
    public void shouldGetAfterOneDay() {
        Chronology chrono = GregorianChronology.getInstance();
        LocalDate theDay = new LocalDate(1582, 10, 4, chrono);
        String pattern = "yyyy.MM.dd";
        assertThat(theDay.toString(pattern)).isEqualTo("1582.10.04");
        
        LocalDate nextDay = theDay.plusDays(1);
        assertThat(nextDay.toString(pattern)).isEqualTo("1582.10.05");
    }
    
    @Test
    public void shouldGetAfterOneDayWithGJChronology() {
        Chronology chrono = GJChronology.getInstance();
        LocalDate theDay = new LocalDate(1582, 10, 4, chrono);
        String pattern = "yyyy.MM.dd";
        assertThat(theDay.toString(pattern)).isEqualTo("1582.10.04");
        
        LocalDate nextDay = theDay.plusDays(1);
        assertThat(nextDay.toString(pattern)).isEqualTo("1582.10.15");
    }
    
    @Test
    public void shouldGetAfterOneHour() {
        DateTimeZone seoul = DateTimeZone.forID("Asia/Seoul");
        DateTime theTime = new DateTime(1988,5,7,23,0, seoul);
        String pattern = "yyyy.MM.dd HH:mm";
        assertThat(theTime.toString(pattern)).isEqualTo("1988.05.07 23:00");
        assertThat(seoul.isStandardOffset(theTime.getMillis())).isTrue();
        
        DateTime after1Hour = theTime.plusHours(1);
        assertThat(after1Hour.toString(pattern)).isEqualTo("1988.05.08 01:00");
        assertThat(seoul.isStandardOffset(after1Hour.getMillis())).isFalse();
    }
    
    @Test
    public void shouldGetAfterOneMinute() {
        DateTimeZone seoul = DateTimeZone.forID("Asia/Seoul");
        DateTime theTime = new DateTime(1961, 8, 9, 23, 59, seoul);
        String pattern = "yyyy.MM.dd HH:mm";
        assertThat(theTime.toString(pattern)).isEqualTo("1961.08.09 23:59");
        
        DateTime after1Minute = theTime.plusMinutes(1);
        assertThat(after1Minute.toString(pattern)).isEqualTo("1961.08.10 00:30");
    }
    
    @Test
    public void shouldGetAfterTwoSecond() {
        DateTimeZone utc = DateTimeZone.forID("UTC");
        DateTime theTime = new DateTime(2012, 6, 30, 23, 59, 59, utc);
        String pattern = "yyyy.MM.dd HH:mm:ss";
        assertThat(theTime.toString(pattern)).isEqualTo("2012.06.30 23:59:59");
        
        DateTime after2Seconds = theTime.plusSeconds(2);
        assertThat(after2Seconds.toString(pattern)).isEqualTo("2012.07.01 00:00:01");
    }
    
    @Test(expected=IllegalArgumentException.class)
    public void shouldThrowExceptionWhenWrongTimeZoneId(){
        DateTimeZone.forID("Seoul/Asia");
    }
    
    @Test
    public void shouldGetDate() {
        LocalDate theDay = new LocalDate(1999, 12, 31);
        
        assertThat(theDay.getYear()).isEqualTo(1999);
        assertThat(theDay.getMonthOfYear()).isEqualTo(12);
        assertThat(theDay.getDayOfMonth()).isEqualTo(31);
    }
    
    @Test (expected=IllegalFieldValueException.class)
    public void shouldNotAcceptWrongMonth() {
        new LocalDate(1999, 13, 31);
    }
    
    @Test
    public void shouldGetDayOfWeek() {
        LocalDate theDay = new LocalDate(2014, 1, 1);
        
        int dayOfWeek = theDay.getDayOfWeek();
        assertThat(dayOfWeek).isEqualTo(DateTimeConstants.WEDNESDAY);
        assertThat(dayOfWeek).isEqualTo(3);
    }
}
```

## 5. SR-310의 JDK 7 백포트 모듈 테스트
JDK 7 이상 설치

* JSR-310의 백포트 모듈의 의존성 선언

```xml
<dependency>
    <groupId>org.threeten</groupId>
    <artifactId>threetenbp</artifactId>
    <version>0.8.1</version>
</dependency>
```

```java
import static org.fest.assertions.api.Assertions.*;

import org.junit.Test;
import org.threeten.bp.DateTimeException;
import org.threeten.bp.DayOfWeek;
import org.threeten.bp.Instant;
import org.threeten.bp.LocalDate;
import org.threeten.bp.ZoneId;
import org.threeten.bp.ZonedDateTime;
import org.threeten.bp.chrono.IsoChronology;
import org.threeten.bp.format.DateTimeFormatter;
import org.threeten.bp.zone.ZoneRules;
import org.threeten.bp.zone.ZoneRulesException;

public class Jsr310BackportTest {
    @Test
    public void shouldGetAfterOneDay() {
        LocalDate theDay = IsoChronology.INSTANCE.date(1582, 10, 4);
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy.MM.dd");
        assertThat(theDay.toString(formatter)).isEqualTo("1582.10.04");
        
        LocalDate nextDay = theDay.plusDays(1);
        assertThat(nextDay.toString(formatter)).isEqualTo("1582.10.05");
    }
    
    @Test
    public void shouldGetAfterOneHour() {
        ZoneId seoul = ZoneId.of("Asia/Seoul");
        ZonedDateTime theTime = ZonedDateTime.of(1988, 5, 7, 23, 0, 0, 0, seoul);
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy.MM.dd HH:mm");
        assertThat(theTime.toString(formatter)).isEqualTo("1988.05.07 23:00");
        ZoneRules seoulRules = seoul.getRules();
        assertThat(seoulRules.isDaylightSavings(Instant.from(theTime))).isFalse();
        
        ZonedDateTime after1Hour = theTime.plusHours(1);
        assertThat(after1Hour.toString(formatter)).isEqualTo("1988.05.08 01:00");
        assertThat(seoulRules.isDaylightSavings(Instant.from(after1Hour))).isTrue();
    }
    
    @Test
    public void shouldGetAfterOneMinute() {
        ZoneId seoul = ZoneId.of("Asia/Seoul");
        ZonedDateTime theTime = ZonedDateTime.of(1961, 8, 9, 23, 59, 59, 0, seoul);
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy.MM.dd HH:mm");
        assertThat(theTime.toString(formatter)).isEqualTo("1961.08.09 23:59");
        
        ZonedDateTime after1Minute = theTime.plusMinutes(1);
        assertThat(after1Minute.toString(formatter)).isEqualTo("1961.08.10 00:30");
    }
    
    @Test
    public void shouldGetAfterTwoSecond() {
        ZoneId utc = ZoneId.of("UTC");
        ZonedDateTime theTime = ZonedDateTime.of(2012, 6, 30, 23, 59, 59, 0, utc);
        DateTimeFormatter format = DateTimeFormatter.ofPattern("yyyy.MM.dd HH:mm:ss");
        assertThat(theTime.toString(format)).isEqualTo("2012.06.30 23:59:59");
        
        ZonedDateTime after2Seconds = theTime.plusSeconds(2);
        assertThat(after2Seconds.toString(format)).isEqualTo("2012.07.01 00:00:01");
    }
    
    @Test(expected=ZoneRulesException.class)
    public void shouldThrowExceptionWhenWrongTimeZoneId(){
        ZoneId.of("Seoul/Asia");
    }
    
    @Test
    public void shouldGetDate() {
        LocalDate theDay = LocalDate.of(1999, 12, 31);
        
        assertThat(theDay.getYear()).isEqualTo(1999);
        assertThat(theDay.getMonthValue()).isEqualTo(12);
        assertThat(theDay.getDayOfMonth()).isEqualTo(31);
    }
    
    @Test(expected=DateTimeException.class)
    public void shouldNotAcceptWrongDate() {
        LocalDate.of(1999, 13, 31);
    }
    
    @Test
    public void shouldGetDayOfWeek() {
        LocalDate theDay = LocalDate.of(2014, 1, 1);
        
        DayOfWeek dayOfWeek = theDay.getDayOfWeek();
        assertThat(dayOfWeek).isEqualTo(DayOfWeek.WEDNESDAY);
    }
}
```

## 6. JSR-310 + JDK 8 테스트

```java
import static org.fest.assertions.api.Assertions.*;

import org.junit.Test;

import java.time.DayOfWeek;
import java.time.Instant;
import java.time.LocalDate;
import java.time.ZoneId;
import java.time.ZonedDateTime;
import java.time.chrono.IsoChronology;
import java.time.format.DateTimeFormatter;
import java.time.zone.ZoneRules;
import java.time.zone.ZoneRulesException;
import java.time.DateTimeException;

public class Jsr310Test {
    @Test
    public void shouldGetAfterOneDay() {
        LocalDate theDay = IsoChronology.INSTANCE.date(1582, 10, 4);
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy.MM.dd");
        assertThat(theDay.format(formatter)).isEqualTo("1582.10.04");
        
        LocalDate nextDay = theDay.plusDays(1);
        assertThat(nextDay.format(formatter)).isEqualTo("1582.10.05");
    }
    
    @Test
    public void shouldGetAfterOneHour() {
        ZoneId seoul = ZoneId.of("Asia/Seoul");
        ZonedDateTime theTime = ZonedDateTime.of(1988, 5, 7, 23, 0, 0, 0, seoul);
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy.MM.dd HH:mm");
        assertThat(theTime.format(formatter)).isEqualTo("1988.05.07 23:00");
        ZoneRules seoulRules = seoul.getRules();
        assertThat(seoulRules.isDaylightSavings(Instant.from(theTime))).isFalse();
        
        ZonedDateTime after1Hour = theTime.plusHours(1);
        assertThat(after1Hour.format(formatter)).isEqualTo("1988.05.08 01:00");
        assertThat(seoulRules.isDaylightSavings(Instant.from(after1Hour))).isTrue();
    }
    
    @Test
    public void shouldGetAfterOneMinute() {
        ZoneId seoul = ZoneId.of("Asia/Seoul");
        ZonedDateTime theTime = ZonedDateTime.of(1961, 8, 9, 23, 59, 59, 0, seoul);
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy.MM.dd HH:mm");
        assertThat(theTime.format(formatter)).isEqualTo("1961.08.09 23:59");
        
        ZonedDateTime after1Minute = theTime.plusMinutes(1);
        assertThat(after1Minute.format(formatter)).isEqualTo("1961.08.10 00:30");
    }
    
    @Test
    public void shouldGetAfterTwoSecond() {
        ZoneId utc = ZoneId.of("UTC");
        ZonedDateTime theTime = ZonedDateTime.of(2012, 6, 30, 23, 59, 59, 0, utc);
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy.MM.dd HH:mm:ss");
        assertThat(theTime.format(formatter)).isEqualTo("2012.06.30 23:59:59");
        
        ZonedDateTime after2Seconds = theTime.plusSeconds(2);
        assertThat(after2Seconds.format(formatter)).isEqualTo("2012.07.01 00:00:01");
    }
    
    @Test(expected=ZoneRulesException.class)
    public void shouldThrowExceptionWhenWrongTimeZoneId(){
        ZoneId.of("Seoul/Asia");
    }
    
    @Test
    public void shouldGetDate() {
        LocalDate theDay = LocalDate.of(1999, 12, 31);
        
        assertThat(theDay.getYear()).isEqualTo(1999);
        assertThat(theDay.getMonthValue()).isEqualTo(12);
        assertThat(theDay.getDayOfMonth()).isEqualTo(31);
    }
    
    @Test(expected=DateTimeException.class)
    public void shouldNotAcceptWrongDate() {
        LocalDate.of(1999, 13, 31);
    }
    
    
    @Test
    public void shouldGetDayOfWeek() {
        LocalDate theDay = LocalDate.of(2014, 1, 1);
        
        DayOfWeek dayOfWeek = theDay.getDayOfWeek();
        assertThat(dayOfWeek).isEqualTo(DayOfWeek.WEDNESDAY);
    }
}
```

## [출처 및 참고]
* [https://d2.naver.com/helloworld/645609](https://d2.naver.com/helloworld/645609)
* [https://jeong-pro.tistory.com/163](https://jeong-pro.tistory.com/163)
