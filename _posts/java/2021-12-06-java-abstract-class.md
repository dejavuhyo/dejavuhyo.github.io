---
title: Java Abstract 클래스
author: dejavuhyo
date: 2021-12-06 09:00:00 +0900
categories: [Language, Java]
tags: [java-abstract-class, abstract-class, 자바-추상-클래스, 추상-클래스, 서브-클래스]
---

## 1. 추상 클래스의 핵심 개념
추상 클래스를 언제 사용해야 하는지 알아보기 전에 가장 관련성이 높은 특성을 살펴본다.

* class 키워드 앞에 abstract 수정자를 사용하여 추상 클래스를 정의한다.

* 추상 클래스는 하위 클래스화할 수 있지만 인스턴스화할 수는 없다.

* 클래스가 하나 이상의 추상 메서드를 정의하는 경우 클래스 자체를 추상으로 선언해야 한다.

* 추상 클래스는 추상 메서드와 구체적인 메서드를 모두 선언할 수 있다.

* 추상 클래스에서 파생된 하위 클래스는 모든 기본 클래스의 추상 메서드를 구현하거나 추상 자체여야 한다.

이러한 개념을 더 잘 이해하기 위한 간단한 예이다.

기본 추상 클래스가 보드 게임의 추상 API를 정의하도록 한다.

```java
public abstract class BoardGame {

    //... field declarations, constructors

    public abstract void play();

    //... concrete methods
}
```

그런 다음 play 메서드를 구현하는 하위 클래스를 만들 수 있다.

```java
public class Checkers extends BoardGame {

    public void play() {
        //... implementation
    }
}
```

## 2. 추상 클래스를 사용하는 경우
인터페이스 및 구체적인 클래스 보다 추상 클래스를 선호해야 하는 몇 가지 일반적인 시나리오이다.

* 여러 관련 하위 클래스가 공유하는 몇 가지 공통 기능을 한 곳(코드 재사용)에 캡슐화하려고 한다.

* 하위 클래스가 쉽게 확장하고 개선할 수 있는 API를 부분적으로 정의해야 한다.

* 하위 클래스는 보호된 액세스 수정자가 있는 하나 이상의 공통 메서드 또는 필드를 상속해야 한다.

이러한 모든 시나리오는 Open/Closed 원칙에 대한 완전한 상속 기반 준수의 좋은 예이다 .

게다가 추상 클래스를 사용하면 기본 유형과 하위 유형을 암시적으로 다루기 때문에 다형성도 활용하고 있다.

코드 재사용은 클래스 계층 구조 내에서 "is-a" 관계가 유지되는 한 추상 클래스를 사용해야 하는 매우 강력한 이유이다.

그리고 Java 8은 기본 메서드를 사용하여 또 다른 주름을 추가한다. 이 메서드는 때때로 추상 클래스를 완전히 만들어야 하는 자리를 대신할 수 있다.

## 3. 파일 판독기의 샘플 계층
추상 클래스가 테이블에 가져오는 기능을 더 명확하게 이해하기 위해 다른 예를이다.

### 1) 기본 추상 클래스 정의
여러 유형의 파일 판독기를 갖고 싶다면 파일 읽기에 공통적인 것을 캡슐화하는 추상 클래스를 만들 수 있다.

```java
public abstract class BaseFileReader {
    
    protected Path filePath;
    
    protected BaseFileReader(Path filePath) {
        this.filePath = filePath;
    }
    
    public Path getFilePath() {
        return filePath;
    }
    
    public List<String> readFile() throws IOException {
        return Files.lines(filePath)
          .map(this::mapFileLine).collect(Collectors.toList());
    }
    
    protected abstract String mapFileLine(String line);
}
```

필요한 경우 하위 클래스가 액세스할 수 있도록 filePath를 보호하도록 만들었다. 더 중요한 것은 파일 내용에서 실제로 한 줄의 텍스트를 구문 분석하는 방법을 미해결 상태로 두었다.

구체적인 클래스에는 파일 경로를 저장하거나 파일을 탐색하는 특별한 방법이 없지만 각 줄을 변환하는 특별한 방법이 있다.

처음에는 BaseFileReader가 불필요해 보일 수 있다. 그러나 이것은 깨끗하고 쉽게 확장할 수 있는 디자인의 기초이다. 이를 통해 고유한 비즈니스 논리에 집중할 수 있는 다양한 버전의 파일 판독기를 쉽게 구현할 수 있다.

### 2) 서브클래스 정의
파일의 내용을 소문자로 변환하는 예이다.

```java
public class LowercaseFileReader extends BaseFileReader {

    public LowercaseFileReader(Path filePath) {
        super(filePath);
    }

    @Override
    public String mapFileLine(String line) {
        return line.toLowerCase();
    }   
}
```

파일의 내용을 대문자로 변환하는 예이다.

```java
public class UppercaseFileReader extends BaseFileReader {

    public UppercaseFileReader(Path filePath) {
        super(filePath);
    }

    @Override
    public String mapFileLine(String line) {
        return line.toUpperCase();
    }
}
```

각 하위 클래스는 파일 읽기의 다른 측면을 지정할 필요 없이 고유한 동작에 집중할 수 있다.

### 3) 서브클래스 사용
추상 클래스에서 상속받은 클래스를 사용하는 것은 다른 구체적인 클래스와 다르지 않다.

```java
@Test
public void givenLowercaseFileReaderInstance_whenCalledreadFile_thenCorrect() throws Exception {
    URL location = getClass().getClassLoader().getResource("files/test.txt")
    Path path = Paths.get(location.toURI());
    BaseFileReader lowercaseFileReader = new LowercaseFileReader(path);
        
    assertThat(lowercaseFileReader.readFile()).isInstanceOf(List.class);
}
```

대상 파일은 src/main/resources/files 폴더 아래에 있다. 따라서 예제 파일의 경로를 가져오기 위해 응용 프로그램 클래스 로더를 사용했다.

## [출처 및 참고]
* <https://www.baeldung.com/java-abstract-class>
