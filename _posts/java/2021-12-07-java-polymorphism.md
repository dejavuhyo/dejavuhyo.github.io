---
title: Java 다형성
author: dejavuhyo
date: 2021-12-07 10:00:00 +0900
categories: [Language, Java]
tags: [java-polymorphism, polymorphism, 자바-다형성, 다형성]
---

## 1. 다형성이란
모든 객체 지향 프로그래밍(OOP) 언어는 추상화, 캡슐화, 상속 및 다형성의 네 가지 기본 특성을 나타내야 한다.

정적 다형성은 컴파일 타임에 적용되는 반면 동적 다형성은 런타임에 구현 된다.

## 2. 정적 다형성
정적 다형성은 컴파일 시간에 해결되어 런타임 가상 테이블 조회를 없애는 다형성의 모방이다.

예를 들어 파일 관리자 앱의 TextFile 클래스에는 read() 메서드의 동일한 서명을 가진 세 가지 메서드가 있을 수 있다.

```java
public class TextFile extends GenericFile {
    //...

    public String read() {
        return this.getContent()
          .toString();
    }

    public String read(int limit) {
        return this.getContent()
          .toString()
          .substring(0, limit);
    }

    public String read(int start, int stop) {
        return this.getContent()
          .toString()
          .substring(start, stop);
    }
}
```

코드 컴파일 동안 컴파일러는 read 메서드의 모든 호출이 위에 정의된 세 가지 메서드 중 하나 이상에 해당 하는지 확인한다.

## 3. 동적 다형성
동적 다형성을 사용하면 JVM(Java Virtual Machine)이 하위 클래스가 상위 형식에 할당될 때 실행할 적절한 메소드의 감지를 처리한다. 이는 하위 클래스가 상위 클래스에 정의된 일부 또는 모든 메서드를 재정의할 수 있기 때문에 필요하다.

가상의 파일 관리자 앱에서 GenericFile 이라는 모든 파일의 상위 클래스를 정의해 본다.

```java
public class GenericFile {
    private String name;

    //...

    public String getFileInfo() {
        return "Generic File Impl";
    }
}
```

GenericFile을 확장 하지만 getFileInfo() 메서드를 재정의 하고 추가 정보를 추가 하는 ImageFile 클래스를 구현할 수도 있다.

```java
public class ImageFile extends GenericFile {
    private int height;
    private int width;

    //... getters and setters
    
    public String getFileInfo() {
        return "Image File Impl";
    }
}
```

ImageFile의 인스턴스를 만들고 GenericFile 클래스에 할당 하면 암시적 캐스트가 수행된다. 그러나 JVM은 ImageFile의 실제 형식에 대한 참조를 유지한다.

위의 구성은 메서드 재정의와 유사하다. 다음과 같이 getFileInfo() 메서드를 호출하여 이를 확인할 수 있다.

```java
public static void main(String[] args) {
    GenericFile genericFile = new ImageFile("SampleImageFile", 200, 100, 
      new BufferedImage(100, 200, BufferedImage.TYPE_INT_RGB)
      .toString()
      .getBytes(), "v1.0.0");
    logger.info("File Info: \n" + genericFile.getFileInfo());
}
```

예상대로 genericFile.getFileInfo()는 아래 출력에서​볼 수 있듯이 ImageFile 클래스의 getFileInfo() 메서드를 트리거한다.

```text
File Info: 
Image File Impl
```

## 4. 기타 다형성 특성
Java의 이러한 두 가지 주요 다형성 유형 외에도 Java 프로그래밍 언어에는 다형성을 나타내는 다른 특성이 있다.

### 1) 강제
다형성 강제는 유형 오류를 방지하기 위해 컴파일러에서 수행하는 암시적 유형 변환을 처리한다. 일반적인 예는 정수 및 문자열 연결에서 볼 수 있다.

```java
String str = “string” + 2;
```

### 2) 연산자 오버로딩
연산자 또는 메서드 오버로딩은 동일한 기호 또는 연산자가 컨텍스트에 따라 다른 의미(형태)를 갖는 다형성 특성을 나타낸다.

예를 들어 더하기 기호(+)는 수학적 덧셈과 문자열 연결에 사용할 수 있다. 두 경우 모두 컨텍스트(즉, 인수 유형)만 기호의 해석을 결정한다.

```java
String str = "2" + 2;
int sum = 2 + 2;
System.out.printf(" str = %s\n sum = %d\n", str, sum);
```

결과

```text
str = 22
sum = 4
```

### 3) 다형성 매개변수
매개변수 다형성을 사용하면 클래스의 매개변수 또는 메서드 이름을 다른 유형과 연결할 수 있다. 아래에 내용을 String으로 정의 하고 나중에 Integer로 정의하는 일반적인 예가 있다.

```java
public class TextFile extends GenericFile {
    private String content;
    
    public String setContentDelimiter() {
        int content = 100;
        this.content = this.content + content;
    }
}
```

다형성 매개변수의 선언은 매개 변수의 로컬 선언이 항상 같은 이름을 가진 다른 매개변수의 전역 선언을 무시하는 변수 숨김으로 알려진 문제로 이어질 수 있다는 점에 유의하는 것도 중요하다.

이 문제를 해결하려면 이 키워드와 같은 전역 참조를 사용 하여 로컬 컨텍스트 내의 전역 변수를 가리키는 것이 좋다.

### 4) 다형성 하위 유형
다형성 하위 유형을 사용하면 편리하게 여러 하위 유형을 유형에 할당하고 유형에 대한 모든 호출이 하위 유형에서 사용 가능한 정의를 트리거할 것으로 예상할 수 있다.

예를 들어 GenericFile 컬렉션이 있고 각각에 대해 getInfo() 메서드를 호출하는 경우 컬렉션의 각 항목이 파생된 하위 유형에 따라 출력이 다를 것으로 예상할 수 있다.

```java
GenericFile [] files = {new ImageFile("SampleImageFile", 200, 100, 
  new BufferedImage(100, 200, BufferedImage.TYPE_INT_RGB).toString() 
  .getBytes(), "v1.0.0"), new TextFile("SampleTextFile", 
  "This is a sample text content", "v1.0.0")};
 
for (int i = 0; i < files.length; i++) {
    files[i].getInfo();
}
```

하위 유형 다형성은 업캐스팅과 후기 바인딩의 조합으로 가능하다. 업캐스팅에는 상위 유형에서 하위 유형으로 상속 계층을 캐스팅하는 작업이 포함된다.

```java
ImageFile imageFile = new ImageFile();
GenericFile file = imageFile;
```

위의 결과로 발생하는 결과는 ImageFile 관련 메서드가 새로운 업캐스트 GenericFile에서 호출될 수 없다는 것 이다. 그러나 하위 유형의 메소드는 상위 유형에 정의된 유사한 메소드보다 우선이다.

상위 유형으로 업캐스팅할 때 하위 유형별 메서드를 호출할 수 없는 문제를 해결하기 위해 상위 유형에서 하위 유형으로 상속을 다운캐스팅할 수 있다. 이것은 다음과 같이 수행된다.

```java
ImageFile imageFile = (ImageFile) file;
```

후기 바인딩 전략은 컴파일러가 업캐스팅 후 트리거할 메서드를 결정하는 데 도움이 된다. 위의 예에서 ```imageFile#getInfo``` 대 ```file#getInfo```의 경우 컴파일러는 ImageFile의 getInfo 메서드에 대한 참조를 유지한다.

## 5. 다형성의 문제
제대로 확인하지 않으면 런타임 오류로 이어질 수 있는 다형성의 몇 가지 모호성을 살펴본다.

### 1) 다운캐스팅 중 유형 식별
이전에 업캐스트를 수행한 후 일부 하위 유형별 메서드에 대한 액세스 권한을 상실했다. 다운캐스트로 이 문제를 해결할 수 있었지만 실제 유형 검사를 보장하지는 않는다.

예를 들어 업캐스트 및 후속 다운캐스트를 수행하는 경우이다.

```java
GenericFile file = new GenericFile();
ImageFile imageFile = (ImageFile) file;
System.out.println(imageFile.getHeight());
```

컴파일러는 클래스가 실제로 ImageFile이 아닌 GenericFile임에도 불구하고 GenericFile을 ImageFile로 다운캐스트할 수 있음을 알 수 있다.

결과적으로 imageFile 클래스에서 getHeight() 메서드를 호출하려고 하면 ClassCastException이 발생한다. GenericFile은 getHeight() 메서드를 정의하지 않기 때문이다.

```java
Exception in thread "main" java.lang.ClassCastException:
GenericFile cannot be cast to ImageFile
```

이 문제를 해결하기 위해 JVM은 RTTI(Run-Time Type Information) 검사를 수행한다. 다음 과 같이 instanceof 키워드를 사용하여 명시적 유형 식별을 시도할 수도 있다.

```java
ImageFile imageFile;
if (file instanceof ImageFile) {
    imageFile = file;
}
```

위의 내용은 런타임 시 ClassCastException 예외를 방지하는 데 도움이 된다. 사용할 수 있는 또 다른 옵션은 try 및 catch 블록 내에서 캐스트를 래핑 하고 ClassCastException을 잡는 것 이다.

RTI 검사는 유형이 올바른지 효과적으로 확인하는 데 필요한 시간과 리소스로 인해 비용이 많이 든다는 점에 유의해야 한다. 또한 키워드 인스턴스를 자주 사용하는 것은 거의 항상 잘못된 설계를 의미한다.

### 2) 취약한 기본 클래스 문제
기본 클래스에 대한 겉보기에 안전한 수정으로 인해 파생 클래스가 오작동할 수 있는 경우 기본 또는 수퍼클래스는 취약한 것으로 간주된다.

GenericFile 이라는 슈퍼클래스와 그 서브클래스 TextFile의 선언을 생각해 본다.

```java
public class GenericFile {
    private String content;

    void writeContent(String content) {
        this.content = content;
    }
    void toString(String str) {
        str.toString();
    }
}
```

```java
public class TextFile extends GenericFile {
    @Override
    void writeContent(String content) {
        toString(content);
    }
}
```

GenericFile 클래스를 수정할 때이다.

```java
public class GenericFile {
    //...

    void toString(String str) {
        writeContent(str);
    }
}
```

위의 수정 사항으로 인해 TextFile이 writeContent() 메서드에서 무한 재귀로 남게 되어 결국 스택 오버플로가 발생한다.

취약한 기본 클래스 문제를 해결 하기 위해 final 키워드를 사용하여 하위 클래스가 writeContent() 메서드를 재정의하는 것을 방지 할 수 있다. 적절한 문서도 도움이 될 수 있다. 그리고 마지막으로 중요한 것은 구성이 일반적으로 상속보다 우선되어야 한다는 것이다.

## [출처 및 참고]
* <https://www.baeldung.com/java-polymorphism>
