---
title: Java 9 압축 문자열
author: dejavuhyo
date: 2022-01-06 09:25:00 +0900
categories: [Language, Java]
tags: [java-9-compact-string, java-compact-string, compact-string, Java-9-압축-문자열, 압축-문자열, 문자열]
---

## 1. 압축 문자열
Java의 문자열은 내부적으로 String의 문자를 포함 하는 char[]로 표현된다. 그리고 Java는 내부적으로 UTF-16을 사용 하기 때문에 모든 문자는 2바이트로 구성 된다.

예를들어 문자열에 영어 단어가 포함된 경우 ASCII 문자는 단일 바이트를 사용하여 표시될 수 있으므로 선행 8비트는 모든 char에 대해 모두 0이다.

많은 문자를 나타내기 위해 16비트가 필요하지만 통계적으로 대부분은 8비트(LATIN-1 문자 표현)만 필요하다. 따라서 메모리 사용량과 성능을 향상시킬 수 있는 여지가 있다.

또한 중요한 것은 일반적으로 String이 JVM 힙 공간의 많은 부분을 차지한다는 것이다. 또한 JVM에 의해 저장되는 방식 때문에 대부분의 경우 문자열 인스턴스는 실제로 필요한 공간을 두 배로 차지할 수 있다.

JDK6에 도입된 압축 문자열 옵션과 최근 JDK9에 도입된 새로운 압축 문자열에 대해 알아본다. 이 두 가지 모두 JMV에서 문자열의 메모리 소비를 최적화하기 위해 설계되었다.

## 2. Compressed String - Java 6
JDK 6 update 21 성능 릴리스에는 새로운 VM 옵션이 도입되었다.

```text
-XX:+UseCompressedStrings
```

이 옵션이 활성화되면 문자열은 char[] 대신 byte[]로 저장되므로 많은 메모리가 절약된다. 그러나 이 옵션은 주로 의도하지 않은 성능 결과를 초래했기 때문에 JDK 7에서 결국 제거되었다.

## 3. Compact String - Java 9
자바 9은 컴팩트 스트링의 개념을 되살렸다.

즉, 문자열을 만들 때마다 문자열의 모든 문자가 바이트 LATIN-1 표현을 사용하여 표현될 수 있다면 바이트 배열이 내부적으로 사용되어 하나의 문자에 대해 1바이트가 주어진다.

다른 경우에는, 어떤 문자가 그것을 표현하기 위해 8비트 이상을 요구한다면, 모든 문자는 각각 UTF-16 표현을 사용하여 2바이트로 저장된다.

따라서 기본적으로 가능할 때마다 각 문자에 대해 single 바이트만 사용한다.

문제는 모든 String 작업이 어떻게 작동하느냐 하는 것이다. LATIN-1과 UTF-16 표현을 구별을 해결하기 위해 스트링의 내부 구현에 또 다른 변화가 있다. 이 정보를 보존하는 final field coder가 있다.

### 1) Java 9의 문자열 구현
지금까지 문자열은 char[]로 저장되었다.

```java
private final char[] value;
```

이제부터 byte[]가 된다.

```java
private final byte[] value;
```

변수 coder

```java
private final byte coder;
```

coder가 될 수 있다.

```java
static final byte LATIN1 = 0;
static final byte UTF16 = 1;
```

대부분의 문자열 작업은 이제 coder를 확인하고 특정 구현으로 전달한다.

```java
public int indexOf(int ch, int fromIndex) {
    return isLatin1() 
      ? StringLatin1.indexOf(value, ch, fromIndex) 
      : StringUTF16.indexOf(value, ch, fromIndex);
}  

private boolean isLatin1() {
    return COMPACT_STRINGS && coder == LATIN1;
}
```

JVM에 필요한 모든 정보가 준비되고 사용 가능하므로 CompactString VM 옵션은 기본적으로 활성화된다. 비활성화하려면 다음을 사용할 수 있다.

```text
+XX:-CompactStrings
```

### 2) coder의 작동 방식
Java 9 String 클래스 구현에서 길이는 다음과 같이 계산된다.

```java
public int length() {
    return value.length >> coder;
}
```

문자열이 LATIN-1만 포함하면 코더의 값은 0이 되므로 문자열의 길이는 바이트 배열의 길이와 같다.

다른 경우, 문자열이 UTF-16 표현에 있으면 코더의 값은 1이 되며, 따라서 길이는 실제 바이트 배열의 절반 크기가 된다.

Compact String에 대한 모든 변경사항은 String 클래스의 내부 구현에 있으며 String을 사용하는 개발자에게 완전히 투명하다.

## 4. Compact Strings vs. Compressed Strings
JDK 6 압축 문자열의 경우 직면한 주요 문제는 문자열 생성자가 인수로 char[]만 허용 한다는 것이다. 이 외에도 많은 문자열 작업은 바이트 배열이 아닌 char[] 표현에 의존했다. 이로 인해 많은 압축을 풀어야 했고, 이는 성능에 영향을 미쳤다.

반면 Compact String의 경우 추가 필드 "coder"를 유지하는 것도 오버헤드를 증가시킬 수 있다. 코더 비용을 줄이고 byte를 char로 압축 해제 하기 위해 (UTF-16 표현의 경우) 일부 메서드가 내장 되어 있고 JIT 컴파일러에서 생성된 ASM 코드도 개선되었다.

이 변경으로 인해 직관적이지 않은 결과가 나타났다. LATIN-1 indexOf(String)는 내장 메서드를 호출하지만 indexOf(char)는 호출 하지 않는다. UTF-16의 경우 이 두 메서드 모두 내장 메서드를 호출한다. 이 문제는 LATIN-1 문자열에만 영향을 미치며 향후 릴리스에서 수정될 예정이다.

따라서 Compact Strings은 성능면에서 Compressed Strings 보다 낫다.

Compact Strings를 사용하여 얼마나 많은 메모리가 절약되는지 알아보기 위해 다양한 Java 애플리케이션 힙 덤프를 분석했다. 결과는 특정 응용 프로그램에 크게 의존했지만 전반적인 개선은 거의 항상 상당했습니다.

### 1) 성능의 차이
Compact Strings 활성화 및 비활성화 간의 성능 차이에 대한 매우 간단한 예이다.

```java
long startTime = System.currentTimeMillis();
 
List strings = IntStream.rangeClosed(1, 10_000_000)
  .mapToObj(Integer::toString) 
  .collect(toList());
 
long totalTime = System.currentTimeMillis() - startTime;
System.out.println(
  "Generated " + strings.size() + " strings in " + totalTime + " ms.");

startTime = System.currentTimeMillis();
 
String appended = (String) strings.stream()
  .limit(100_000)
  .reduce("", (l, r) -> l.toString() + r.toString());
 
totalTime = System.currentTimeMillis() - startTime;
System.out.println("Created string of length " + appended.length() 
  + " in " + totalTime + " ms.");
```

여기에서 1000만 개의 String을 생성한 다음 순진한 방식으로 추가한다. 이 코드를 실행하면(Compact Strings는 기본적으로 활성화됨) 다음과 같은 결과를 얻는다.

```text
Generated 10000000 strings in 854 ms.
Created string of length 488895 in 5130 ms.
```

마찬가지로, -XX:-CompactStrings 옵션을 사용하여 Compact Strings를 비활성화하여 실행 하면 출력은 다음과 같다.

```text
Generated 10000000 strings in 936 ms.
Created string of length 488895 in 9727 ms.
```

분명히 이것은 표면 수준 테스트이며 대표성이 높지 않다. 이 특정 시나리오에서 성능을 향상시키기 위해 새로운 옵션이 무엇을 할 수 있는지에 대한 스냅샷일 뿐이다.

## [출처 및 참고]
* <https://www.baeldung.com/java-9-compact-string>
