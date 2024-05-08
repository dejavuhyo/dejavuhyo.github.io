---
title: Java 스트림 문자열
author: dejavuhyo
date: 2024-05-08 18:37:00 +0900
categories: [Language, Java]
tags: [java-stream, stream-strings, java-strings, 스트림-문자열]
---

## 1. Stream API 문자열 Joining
Stream API를 사용하여 String 배열을 쉼표로 구분된 String으로 결합하는 함수를 만든다.

```java
public static String join(String[] arrayOfString){
    return Arrays.asList(arrayOfString)
      .stream()
      //.map(...)
      .collect(Collectors.joining(","));
}
```

주의할 점이다.

* `stream()` 함수는 모든 컬렉션을 데이터 스트림으로 변환한다.

* `map()` 함수는 데이터를 처리하는데 사용된다.

* 필터링 기준을 포함할 수 있는 `filter()`라는 또 다른 함수도 있다.

고정된 접두사와 접미사를 사용하여 문자열을 결합하려는 시나리오가 있을 수 있다. Stream API를 사용하면 다음과 같은 방법으로 이를 수행할 수 있다.

```java
public static String joinWithPrefixPostfix(String[] arrayOfString){
    return Arrays.asList(arrayOfString)
      .stream()
      //.map(...)
      .collect(Collectors.joining(",","[","]"));
}
```

`Collectors.joining()` 메소드에서 볼 수 있듯이 접두어를 `[`로 선언하고 접미어를 `]`로 선언한다. 따라서 생성된 문자열은 선언된 `[...]` 형식으로 생성된다.

## 2. Stream API 문자열 Splitting
Stream API를 사용하여 쉼표로 구분된 문자열을 문자열 목록으로 분할하는 함수를 만든다.

```java
public static List<String> split(String str){
    return Stream.of(str.split(","))
      .map (elem -> new String(elem))
      .collect(Collectors.toList());
}
```

Stream API를 사용하여 String을 Character list으로 직접 변환하는 것도 가능하다.

```java
public static List<Character> splitToListOfChar(String str) {
    return str.chars()
      .mapToObj(item -> (char) item)
      .collect(Collectors.toList());
}
```

`chars()` 메서드가 String을 Integer 스트림으로 변환한다. 여기서 각 Integer 값은 각각의 Char 시퀀스의 `ASCII` 값을 나타낸다. 그렇기 때문에 `mapToObj()` 메서드에서 매퍼 객체를 명시적으로 형변환해야 한다.

## 3. Stream API 문자열 Array to Map
배열의 각 항목에 구분 기호로 연결된 key-value 엔터티가 포함되어 있는 경우 분할 및 `Collectors.toMap`을 사용하여 문자열 배열을 매핑하도록 변환할 수도 있다.

```java
public static Map<String, String> arrayToMap(String[] arrayOfString) {
    return Arrays.asList(arrayOfString)
      .stream()
      .map(str -> str.split(":"))
      .collect(toMap(str -> str[0], str -> str[1]));
}
```

여기서 `:`은 문자열 배열의 모든 요소에 대한 key-value 구분 기호이다.

컴파일 오류를 방지하려면 코드가 Java 1.8을 사용하여 컴파일되었는지 확인해야 한다. 이렇게 하려면 `pom.xml`에 다음 플러그인을 추가해야 한다.

```xml
<build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.3</version>
        <configuration>
          <source>1.8</source>
          <target>1.8</target>
        </configuration>
      </plugin>
    </plugins>
</build>
```

## 4. 테스트
함수 생성이 완료되었으므로 결과를 확인하기 위한 테스트 케이스를 생성한다.

간단한 조인 방법을 테스트한다.

```java
@Test
public void givenArray_transformedToStream_convertToString() {
    String[] programmingLanguages = {"java", "python", "nodejs", "ruby"};
    String expectation = "java,python,nodejs,ruby";

    String result  = JoinerSplitter.join(programmingLanguages);
    assertEquals(result, expectation);
}
```

다음으로 간단한 분할 기능을 테스트하기 위해 또 다른 것을 만든다.

```java
@Test
public void givenString_transformedToStream_convertToList() {
    String programmingLanguages = "java,python,nodejs,ruby";

    List<String> expectation = new ArrayList<>();
    expectation.add("java");
    expectation.add("python");
    expectation.add("nodejs");
    expectation.add("ruby");

    List<String> result  = JoinerSplitter.split(programmingLanguages);

    assertEquals(result, expectation);
}
```

마지막으로 기능을 매핑하기 위해 문자열 배열을 테스트한다.

```java
@Test
public void givenStringArray_transformedToStream_convertToMap() {

    String[] programming_languages = new String[] {"language:java","os:linux","editor:emacs"};
    
    Map<String,String> expectation=new HashMap<>();
    expectation.put("language", "java");
    expectation.put("os", "linux");
    expectation.put("editor", "emacs");
    
    Map<String, String> result = JoinerSplitter.arrayToMap(programming_languages);
    assertEquals(result, expectation);
    
}
```

## [출처 및 참고]
* [https://www.baeldung.com/java-stream-operations-on-strings](https://www.baeldung.com/java-stream-operations-on-strings)
