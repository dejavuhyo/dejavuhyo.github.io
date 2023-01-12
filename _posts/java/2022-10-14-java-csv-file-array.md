---
title: Java CSV 파일을 배열로 읽기
author: dejavuhyo
date: 2022-10-14 15:30:00 +0900
categories: [Language, Java]
tags: [java-csv-array, java-csv, csv-array, java-bufferedreader, java-scanner, 자바-csv-배열, csv-배열, csv-읽기, cvs-배열-읽기]
---

## 1. java.io의 BufferedReader
먼저 BufferedReader에서 `readLine()`을 사용하여 레코드를 한 줄씩 읽는다. 그런 다음 쉼표 구분 기호에 따라 줄을 토큰으로 나눈다.

```java
List<List<String>> records = new ArrayList<>();
try (BufferedReader br = new BufferedReader(new FileReader("book.csv"))) {
    String line;
    while ((line = br.readLine()) != null) {
        String[] values = line.split(COMMA_DELIMITER);
        records.add(Arrays.asList(values));
    }
}
```

> 보다 정교한 CSV(예: 따옴표 또는 쉼표를 값으로 포함)는 이 접근 방식에서 의도한 대로 구문 분석되지 않는다.

## 2. java.util의 Scanner
다음으로 java.util.Scanner를 사용 하여 파일 내용을 실행하고 줄을 하나씩 차례로 검색한다.

```java
List<List<String>> records = new ArrayList<>();
try (Scanner scanner = new Scanner(new File("book.csv"));) {
    while (scanner.hasNextLine()) {
        records.add(getRecordFromLine(scanner.nextLine()));
    }
}
```

그런 다음 라인을 구문 분석하고 배열에 저장한다.

```java
private List<String> getRecordFromLine(String line) {
    List<String> values = new ArrayList<String>();
    try (Scanner rowScanner = new Scanner(line)) {
        rowScanner.useDelimiter(COMMA_DELIMITER);
        while (rowScanner.hasNext()) {
            values.add(rowScanner.next());
        }
    }
    return values;
}
```

## 3. OpenCSV
OpenCSV로 더 복잡한 CSV 파일을 처리할 수 있다. OpenCSV는 CSV 파일 작업을 위한 API를 제공하는 라이브러리이다.

CSVReader의 `readNext()` 메서드를 사용하여 파일의 레코드를 읽는다.

```java
List<List<String>> records = new ArrayList<List<String>>();
try (CSVReader csvReader = new CSVReader(new FileReader("book.csv"));) {
    String[] values = null;
    while ((values = csvReader.readNext()) != null) {
        records.add(Arrays.asList(values));
    }
}
```

## [출처 및 참고]
* [https://www.baeldung.com/java-csv-file-array](https://www.baeldung.com/java-csv-file-array)
