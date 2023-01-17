---
title: Java Streams
author: dejavuhyo
date: 2023-01-17 21:30:00 +0900
categories: [Language, Java]
tags: [java-streams, streams, parallelstream, stream-foreach, stream-map, stream-filter, stream-limit, stream-sorted, parallel-processing, stream-collectors, stream-statistics, 자바-스트림, 스트림]
---

## 1. Streams
Stream은 Java 8에 도입된 새로운 추상 계층이다. Stream을 사용하면 SQL 문과 유사한 선언적 방식으로 데이터를 처리할 수 있다.

```sql
SELECT max(salary), employee_id, employee_name FROM Employee
```

위의 SQL 표현 식은 개발자 측에서 계산을 수행하지 않고 최대 급여 직원의 세부 정보를 자동으로 반환한다. Java의 컬렉션 프레임워크를 사용하면 개발자는 루프를 사용하고 반복적으로 확인해야 한다.

또 다른 관심사는 효율성이다. 멀티 코어 프로세서를 쉽게 사용할 수 있으므로 Java 개발자는 오류가 발생하기 쉬운 병렬 코드 처리를 작성해야 한다.

이러한 문제를 해결하기 위해 Java 8에서는 개발자가 데이터를 선언적으로 처리하고 특정 코드를 작성할 필요 없이 멀티코어 아키텍처를 활용할 수 있는 스트림 개념을 도입했다.

Stream은 집계 작업을 지원하는 소스의 개체 시퀀스를 나타낸다. 다음은 스트림의 특성이다.

* Sequence of elements: Stream은 특정 유형의 요소 집합을 순차적으로 제공한다. 스트림은 필요에 따라 요소를 얻거나 계산한다. 요소를 저장하지 않는다.

* Source: Stream은 컬렉션, 배열 또는 I/O 리소스를 입력 소스로 사용한다.

* Aggregate operations: Stream은 필터, 매핑, 제한, 축소, 찾기, 일치 등과 같은 집계 작업을 지원한다.

* Pipelining: 대부분의 Stream 작업은 결과가 파이프라인 될 수 있도록 스트림 자체를 반환한다. 이러한 작업을 중간 작업이라고 하며 해당 작업의 기능은 입력받아 처리하고 출력을 대상으로 반환하는 것이다. collect() 메서드는 일반적으로 스트림의 끝을 표시하기 위해 파이프라인 작업의 끝에 있는 터미널 작업이다.

* Automatic iterations: 스트림 작업은 명시적 반복이 필요한 컬렉션과 달리 제공된 소스 요소에 대해 내부적으로 반복을 수행한다.

## 2. 스트림 생성
Java 8에서 Collection 인터페이스에는 스트림을 생성하는 두 가지 방법이 있다.

* `stream()`: 컬렉션을 소스로 고려하여 순차 스트림을 반환한다.

* `parallelStream()`: 컬렉션을 소스로 고려하여 병렬 스트림을 반환한다.

```java
List<String> strings = Arrays.asList("abc", "", "bc", "efg", "abcd","", "jkl");
List<String> filtered = strings.stream().filter(string -> !string.isEmpty()).collect(Collectors.toList());
```

### 1) forEach
Stream은 스트림의 각 요소를 반복하는 새로운 메서드 `forEach`를 제공했다. 다음 코드는 `forEach`를 사용하여 10개의 난수를 인쇄하는 방법이다.

```java
Random random = new Random();
random.ints().limit(10).forEach(System.out::println);
```

### 2) map
`map` 메서드는 각 요소를 해당 결과에 매핑하는 데 사용된다. 다음 코드는 맵을 사용하여 고유한 숫자 제곱을 인쇄한다.

```java
List<Integer> numbers = Arrays.asList(3, 2, 2, 3, 7, 3, 5);

//get list of unique squares
List<Integer> squaresList = numbers.stream().map( i -> i*i).distinct().collect(Collectors.toList());
```

### 3) filter
`filter` 방법은 기준에 따라 요소를 제거하는 데 사용된다. 다음 코드는 필터를 사용하여 빈 문자열의 수를 인쇄한다.

```java
List<String>strings = Arrays.asList("abc", "", "bc", "efg", "abcd","", "jkl");

//get count of empty string
int count = strings.stream().filter(string -> string.isEmpty()).count();
```

### 4) limit
스트림의 크기를 줄이기 위해 `limit` 방법이 사용된다. 다음 코드는 `limit`를 사용하여 10개의 난수를 인쇄하는 방법이다.

```java
Random random = new Random();
random.ints().limit(10).forEach(System.out::println);
```

### 5) sorted
`sorted` 메서드는 스트림을 정렬하는 데 사용된다. 다음 코드는 10개의 난수를 정렬된 순서로 인쇄하는 방법이다.

```java
Random random = new Random();
random.ints().limit(10).sorted().forEach(System.out::println);
```

### 6) Parallel Processing
`parallelStream`은 병렬 처리를 위한 스트림의 대안이다. `parallelStream`을 사용하여 빈 문자열의 수를 인쇄하는 코드이다.

```java
List<String> strings = Arrays.asList("abc", "", "bc", "efg", "abcd","", "jkl");

//get count of empty string
long count = strings.parallelStream().filter(string -> string.isEmpty()).count();
```

순차 스트림과 병렬 스트림 사이를 전환하는 것은 매우 쉽다.

### 7) Collectors
`Collectors`는 스트림 요소에 대한 처리 결과를 결합하는 데 사용된다. Collectors는 목록이나 문자열을 반환하는 데 사용할 수 있다.

```java
List<String>strings = Arrays.asList("abc", "", "bc", "efg", "abcd","", "jkl");
List<String> filtered = strings.stream().filter(string -> !string.isEmpty()).collect(Collectors.toList());

System.out.println("Filtered List: " + filtered);
String mergedString = strings.stream().filter(string -> !string.isEmpty()).collect(Collectors.joining(", "));
System.out.println("Merged String: " + mergedString);
```

### 8) Statistics
Java 8에서는 스트림 처리가 수행될 때 모든 통계를 계산하기 위해 통계 수집기가 도입되었다.

```java
List numbers = Arrays.asList(3, 2, 2, 3, 7, 3, 5);

IntSummaryStatistics stats = numbers.stream().mapToInt((x) -> x).summaryStatistics();

System.out.println("Highest number in List : " + stats.getMax());
System.out.println("Lowest number in List : " + stats.getMin());
System.out.println("Sum of all numbers : " + stats.getSum());
System.out.println("Average of all numbers : " + stats.getAverage());
```

## 3. 스트림 예

* Java8Tester.java

```java
import java.util.*;
import java.util.stream.Collectors;

public class Java8Tester {
    public static void main(String args[]) {
        System.out.println("Using Java 7: ");

        // Count empty strings
        List<String> strings = Arrays.asList("abc", "", "bc", "efg", "abcd", "", "jkl");
        System.out.println("List: " + strings);
        long count = getCountEmptyStringUsingJava7(strings);

        System.out.println("Empty Strings: " + count);
        count = getCountLength3UsingJava7(strings);

        System.out.println("Strings of length 3: " + count);

        //Eliminate empty string
        List<String> filtered = deleteEmptyStringsUsingJava7(strings);
        System.out.println("Filtered List: " + filtered);

        //Eliminate empty string and join using comma.
        String mergedString = getMergedStringUsingJava7(strings, ", ");
        System.out.println("Merged String: " + mergedString);
        List<Integer> numbers = Arrays.asList(3, 2, 2, 3, 7, 3, 5);

        //get list of square of distinct numbers
        List<Integer> squaresList = getSquares(numbers);
        System.out.println("Squares List: " + squaresList);
        List<Integer> integers = Arrays.asList(1, 2, 13, 4, 15, 6, 17, 8, 19);

        System.out.println("List: " + integers);
        System.out.println("Highest number in List : " + getMax(integers));
        System.out.println("Lowest number in List : " + getMin(integers));
        System.out.println("Sum of all numbers : " + getSum(integers));
        System.out.println("Average of all numbers : " + getAverage(integers));
        System.out.println("Random Numbers: ");

        //print ten random numbers
        Random random = new Random();

        for (int i = 0; i < 10; i++) {
            System.out.println(random.nextInt());
        }

        System.out.println("Using Java 8: ");
        System.out.println("List: " + strings);

        count = strings.stream().filter(string -> string.isEmpty()).count();
        System.out.println("Empty Strings: " + count);

        count = strings.stream().filter(string -> string.length() == 3).count();
        System.out.println("Strings of length 3: " + count);

        filtered = strings.stream().filter(string -> !string.isEmpty()).collect(Collectors.toList());
        System.out.println("Filtered List: " + filtered);

        mergedString = strings.stream().filter(string -> !string.isEmpty()).collect(Collectors.joining(", "));
        System.out.println("Merged String: " + mergedString);

        squaresList = numbers.stream().map(i -> i * i).distinct().collect(Collectors.toList());
        System.out.println("Squares List: " + squaresList);
        System.out.println("List: " + integers);

        IntSummaryStatistics stats = integers.stream().mapToInt((x) -> x).summaryStatistics();

        System.out.println("Highest number in List : " + stats.getMax());
        System.out.println("Lowest number in List : " + stats.getMin());
        System.out.println("Sum of all numbers : " + stats.getSum());
        System.out.println("Average of all numbers : " + stats.getAverage());
        System.out.println("Random Numbers: ");

        random.ints().limit(10).sorted().forEach(System.out::println);

        //parallel processing
        count = strings.parallelStream().filter(string -> string.isEmpty()).count();
        System.out.println("Empty Strings: " + count);
    }

    private static int getCountEmptyStringUsingJava7(List<String> strings) {
        int count = 0;

        for (String string : strings) {

            if (string.isEmpty()) {
                count++;
            }
        }
        return count;
    }

    private static int getCountLength3UsingJava7(List<String> strings) {
        int count = 0;

        for (String string : strings) {

            if (string.length() == 3) {
                count++;
            }
        }
        return count;
    }

    private static List<String> deleteEmptyStringsUsingJava7(List<String> strings) {
        List<String> filteredList = new ArrayList<String>();

        for (String string : strings) {

            if (!string.isEmpty()) {
                filteredList.add(string);
            }
        }
        return filteredList;
    }

    private static String getMergedStringUsingJava7(List<String> strings, String separator) {
        StringBuilder stringBuilder = new StringBuilder();

        for (String string : strings) {

            if (!string.isEmpty()) {
                stringBuilder.append(string);
                stringBuilder.append(separator);
            }
        }
        String mergedString = stringBuilder.toString();
        return mergedString.substring(0, mergedString.length() - 2);
    }

    private static List<Integer> getSquares(List<Integer> numbers) {
        List<Integer> squaresList = new ArrayList<Integer>();

        for (Integer number : numbers) {
            Integer square = new Integer(number.intValue() * number.intValue());

            if (!squaresList.contains(square)) {
                squaresList.add(square);
            }
        }
        return squaresList;
    }

    private static int getMax(List<Integer> numbers) {
        int max = numbers.get(0);

        for (int i = 1; i < numbers.size(); i++) {

            Integer number = numbers.get(i);

            if (number.intValue() > max) {
                max = number.intValue();
            }
        }
        return max;
    }

    private static int getMin(List<Integer> numbers) {
        int min = numbers.get(0);

        for (int i = 1; i < numbers.size(); i++) {
            Integer number = numbers.get(i);

            if (number.intValue() < min) {
                min = number.intValue();
            }
        }
        return min;
    }

    private static int getSum(List numbers) {
        int sum = (int) (numbers.get(0));

        for (int i = 1; i < numbers.size(); i++) {
            sum += (int) numbers.get(i);
        }
        return sum;
    }

    private static int getAverage(List<Integer> numbers) {
        return getSum(numbers) / numbers.size();
    }
}
```

* 결과

```text
Using Java 7:
List: [abc, , bc, efg, abcd, , jkl]
Empty Strings: 2
Strings of length 3: 3
Filtered List: [abc, bc, efg, abcd, jkl]
Merged String: abc, bc, efg, abcd, jkl
Squares List: [9, 4, 49, 25]
List: [1, 2, 13, 4, 15, 6, 17, 8, 19]
Highest number in List : 19
Lowest number in List : 1
Sum of all numbers : 85
Average of all numbers : 9
Random Numbers:
-1279735475
903418352
-1133928044
-1571118911
628530462
18407523
-881538250
-718932165
270259229
421676854
Using Java 8:
List: [abc, , bc, efg, abcd, , jkl]
Empty Strings: 2
Strings of length 3: 3
Filtered List: [abc, bc, efg, abcd, jkl]
Merged String: abc, bc, efg, abcd, jkl
Squares List: [9, 4, 49, 25]
List: [1, 2, 13, 4, 15, 6, 17, 8, 19]
Highest number in List : 19
Lowest number in List : 1
Sum of all numbers : 85
Average of all numbers : 9.444444444444445
Random Numbers:
-1009474951
-551240647
-2484714
181614550
933444268
1227850416
1579250773
1627454872
1683033687
1798939493
Empty Strings: 2
```

## [출처 및 참고]
* [https://www.tutorialspoint.com/java8/java8_streams.htm](https://www.tutorialspoint.com/java8/java8_streams.htm)
