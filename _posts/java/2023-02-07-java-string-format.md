---
title: Java String.format()
author: dejavuhyo
date: 2023-02-07 21:35:00 +0900
categories: [Language, Java]
tags: [java-string-format, string-format, java-format, 자바-문자열, 자바-문자열-형식, 문자열-형식, 자바-형식]
---

## 1. String.format()
리턴되는 문자열의 형태를 지정하는 메소드로, 서식 문자열의 앞에 `%`를 붙여 문자열에 사용하면 그 위치에 변수의 값을 형식화 하여 대입 가능하다.

대문자나 소문자를 지정할 수 있는 서식 문자열에서 대문자 지정시 대문자로 변환된 값이 표시된다.

## 2. 포맷 지정 서식
format에 지정할 수 있는 주요 서식을 나타낸 표이다. `%` 문자와 함께 조합하여 사용한다.

### 1) 숫자/문자 변환

| 서식 | 분류 | 설명 |
|-----|-----|-----|
| `b`, `B` | 참 거짓 | boolean 값 |
| `h`, `H` | 16진수 | 16진수 |
| `s`, `S` | 문자열 | 문자열 |
| `c`, `C` | 문자 | 문자 |
| `d` | 정수 | 10진 정수로 표시 |
| `o` | 정수 | 8진 정수로 표시 |
| `x`, `X` | 정수 | 16진 정수로 표시 |
| `e`, `E` | 부동 소수점 | 10진수(가수와 진수)로 표시 |
| `f` | 부동 소수점 | 10진수로 표시 |
| `g`, `G` | 부동 소수점 | 10진수(반올림)로 표시 |
| `a`, `A` | 부동 소수점 | 16진수(가수와 진수)로 표시 |
| `t`, `T` | 날짜, 시간 | 날짜 및 시간 서식 문자 앞에 지정 ex) `%tH`, `%tM`, `%tS` |
| `%` | 퍼센트 | 퍼센트 표시 |
| `n` | 줄 구분 | 개행문자 표시 |

### 2) 날짜/시간 변환

| 서식 | 분류 | 설명 |
|-----|-----|-----|
| `H` | 시간 | 24시간 형식의 시(00-23) |
| `I` | 시간 | 12시간 형식의 시(01-12) |
| `k` | 시간 | 24시간 형식의 시(0-23) |
| `l` | 시간 | 12시간 형식의 시(1-12) |
| `M` | 시간 | 분(00-59) |
| `S` | 시간 | 분초(00-60) |
| `L` | 시간 | 밀리초(000-999) |
| `N` | 시간 | 나노초(000000000-999999999) |
| `z` | 시간 | UTC 오프셋. 출력은 `+HHMM` 또는 `-HHMM` 형식의 문자열. `+`는 GMT 동쪽, `-`는 GMT 서쪽, HH는 GMT에서 시간 수, MM은 GMT에서의 분 수를 나타냄 |
| `Z` | 시간 | 시간대명 |
| `s` | 시간 | 1970년 1월 1일 시작 이후 초(00:00:00) |
| `Q` | 시간 | 1970년 1월 1일 시작 이후 밀리초(00:00:00) |
| `B` | 날짜 | 국가 고유의 전체 월명 ex) January, Februry |
| `b`, `h` | 날짜 | 국가 고유의 축약 월명 ex) Jan, Feb |
| `A` | 날짜 | 국가 고유의 전체 요일명 ex) Sunday, Monday |
| `a` | 날짜 | 국가 고유의 축약 요일명 ex) Sun, Mon |
| `C` | 날짜 | 세기 수(00-99) 연도를 100으로 나누고 정수로 자름 |
| `Y` | 날짜 | 년(4자리), 음수 가능 |
| `y` | 날짜 | 년(2자리, 00-99) |
| `j` | 날짜 | 연도의 일(001-366) |
| `m` | 날짜 | 월(01-12) |
| `d` | 날짜 | 월의 일(01-31) |
| `e` | 날짜 | 월의 일(1-31) |
| `R` | 날짜/시간 | 초가 없는 24시간 형식(`%H:%M`) |
| `T` | 날짜/시간 | 초가 있는 24시간 형식(`%H:%M:%S`) |
| `r` | 날짜/시간 | 국가 고유의 AM/PM 형식 시간. 국가 고유의 시간 형식을 사용할 수 없는 경우, 디폴트는 POSIX 시간 AM/PM 형식(`%I:%M:%S %p`) |
| `D` | 날짜/시간 | 날짜 형식(`%m/%d/%y`) |
| `F` | 날짜/시간 | ISO 날짜 형식(`%Y-%m-%d`) |
| `c` | 날짜/시간 | 국가 고유 형식의 날짜/시간 ex) `%ta %tb %td %tT %tZ %tY` → Sun Jul 20 16:17:00 EDT 1969 |

## 3. Argument Index
인수 목록에서 인수의 위치를 나타내는 10진수 정수(해당 인수를 인덱스로 지정)하고, 아라비아 숫자와 `$` 문자로 구성하며(첫 번째 인수 `1$`, 두 번째 인수 `2$`), `<` 플래그를 사용하면 이전 형식 지정자에 대한 인수를 재사용 할 수 있다.

### 1) 예시

* ArgumentIndexTest.java

```java
import java.time.LocalDateTime;

public class ArgumentIndexTest {
    public static void main(String[] args) {
        String str = "StringFormatExample";
        boolean bool = true;
        int n = 1234;
        LocalDateTime now = LocalDateTime.now();

        String result = String.format("문자열 서식: %s, %S", str, str);
        System.out.println(result);

        result = String.format("boolean 서식 문자열: %B, %b", bool, bool);
        System.out.println(result);

        result = String.format("정수 서식: %d, %o, %x, %05d", n, n, n, n);
        System.out.println(result);  // %05d → 5자릿수, 공백을 0으로 채움

        result = String.format("부동 소수점 서식: %4$e, %3$f, %2$g, %1$a", 1234.01, 1234.02, 1234.03, 1234.04);
        System.out.println(result);  // %숫자$ 로 절대 인수 인덱스 지정

        result = String.format("날짜/시간 서식: %tY년 %<tm월 %<td일 %<tH시 %<tM분 %<tS초", now);
        System.out.println(result);  // $< 로 상대 인덱스 지정하여 직전의 인수와 같은 인덱스를 이용
    }
}
```

* 결과

```text
문자열 서식: StringFormatExample, STRINGFORMATEXAMPLE
boolean 서식 문자열: TRUE, true
정수 서식: 1234, 2322, 4d2, 01234
부동 소수점 서식: 1.234040e+03, 1234.030000, 1234.02, 0x1.3480a3d70a3d7p10
날짜/시간 서식: 2021년 04월 09일 16시 21분 33초
```

## [출처 및 참고]
* [https://velog.io/@yu-jin-song/JAVA-문자열-형식-지정](https://velog.io/@yu-jin-song/JAVA-문자열-형식-지정)
