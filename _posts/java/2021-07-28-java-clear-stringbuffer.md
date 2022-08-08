---
title: Java StringBuffer 초기화 방법
author: dejavuhyo
date: 2021-07-28 06:00:00 +0900
categories: [Language, Java]
tags: [java-stringbuffer-clear, stringbuffer-clear, 자바-stringbuffer-초기화, stringbuffer-초기화, stringbuffer-비우기]
---

## 1. 초기화 방법

### 1) delete()

* ClearStringBuffer.java

```java
public class ClearStringBuffer {
    public static void main(String[] args) {
        // create a string buffer
        StringBuffer str = new StringBuffer();

        // add string to string buffer
        str.append("Java");
        str.append(" is");
        str.append(" popular.");
        System.out.println("StringBuffer: " + str);

        // clear the string
        // using delete()
        str.delete(0, str.length());
        System.out.println("Updated StringBuffer: " + str);
    }
}
```

* 결과

```text
StringBuffer: Java is popular.
Updated StringBuffer: 
```

위의 예에서는 StringBuffer 클래스의 delete() 메서드를 사용하여 문자열 버퍼를 지웠다. 여기서 delete() 메서드는 지정된 인덱스 번호 내의 모든 문자를 제거한다.

> __참고:__ delete() 메서드는 이전 데이터를 메모리에 저장하지 않고 삭제하므로 garbage collected 되지 않는다. 가비지 컬렉션(Garbage Collection)을 호출하지 않기 때문에 속도가 중요한 경우 효율적이다.

### 2) setLength()

* ClearStringBuffer.java

```java
public class ClearStringBuffer {
    public static void main(String[] args) {
        // create a string buffer
        StringBuffer str = new StringBuffer();

        // add string to string buffer
        str.append("Java");
        str.append(" is");
        str.append(" awesome.");
        System.out.println("StringBuffer: " + str);

        // clear the string
        // using setLength()
        str.setLength(0);
        System.out.println("Updated StringBuffer: " + str);
    }
}
```

* 결과

```text
StringBuffer: Java is awesome.
Updated StringBuffer
```

여기서 setLength() 메서드는 StringBuffer에 있는 문자 시퀀스를 새 문자 시퀀스로 변경한다. 그리고 새 문자 시퀀스의 길이를 0으로 설정한다.

따라서 이전 문자 시퀀스는 *garbage collected* 된다.

> __참고:__ setLength() 메서드는 문자열 버퍼에 있는 문자 시퀀스를 완전히 무시한다. 그러나 delete() 메서드는 문자 시퀀스에 액세스하여 삭제한다. 따라서 setLength()가 delete()보다 빠르다.

### 3) Creating a new object

* ClearStringBuffer.java

```java
public class ClearStringBuffer {
    public static void main(String[] args) {
        // create a string buffer
        StringBuffer str = new StringBuffer();

        // add string to string buffer
        str.append("Java");
        str.append(" is");
        str.append(" awesome.");
        System.out.println("StringBuffer: " + str);

        // clear the string
        // using new
        // here new object is created and assigned to str
        str = new StringBuffer();
        System.out.println("Updated StringBuffer: " + str);
    }
}
```

* 결과

```text
StringBuffer: Java is awesome.
Updated StringBuffer: 
```

여기에서 new StringBuffer()는 새 문자열 버퍼 개체를 만들고 이전 변수를 새 개체에 할당한다. 이 경우 이전 개체가 있다. 그러나 액세스할 수 없음으로 *garbage collected* 된다.

따라서 매번 이전 문자열 버퍼를 지우는 대신 새 문자열 버퍼가 생성된다. 따라서 성능 면에서 효율성이 떨어진다.

## [출처 및 참고]
* <https://www.programiz.com/java-programming/examples/clear-stringbuffer>
* <https://needjarvis.tistory.com/674>
