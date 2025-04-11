---
title: 직렬화(Serialization)와 역직렬화(Deserialization)
author: dejavuhyo
date: 2025-04-11 17:00:00 +0900
categories: [Language, Java]
tags: [serialization, deserialization, 직렬화, 역직렬화]
---

## 1. 직렬화
직렬화(Serialization)는 데이터 구조나 객체 상태를 저장하거나 전송할 수 있는 포맷(예: JSON, XML)으로 변환하는 과정이다. 이렇게 하면 메모리에 있는 객체를 파일 시스템, 데이터베이스, 또는 네트워크를 통해 전송 후에도 재구성할 수 있다.

즉, 객체들의 데이터를 연속적인 데이터(스트림)로 변형하여 전송 가능한 형태로 만드는 것이다.

## 2. 역직렬화
역직렬화(Deserialization)는 직렬화의 반대 과정으로, 데이터를 원래의 객체나 데이터 구조로 복구하는 과정을 의미한다. 역직렬화를 통해 저장소나 네트워크를 통해 받은 데이터를 응용 프로그램에서 다시 사용할 수 있는 실제 객체로 변환할 수 있다.

즉, 직렬화된 데이터를 다시 객체의 형태로 만드는 것이다.

## 3. 직렬화를 사용하는 이유
자바 원시타입 `bytem short`, `int`, `long`, `float`, `double`, `boolean`, `char`가 있다. 이를 제외한 객체들은 모두 주소값을 참조하는 참조타입이다.

원시타입은 Stack영역에 값을 갖고있어 직접 접근이 가능하지만, 참조타입은 Heap에 존재하고 메모리 주소를 참조하고 있어 프로그램이 종료되거나 Garbage Collector에 의해 Heap 영역에서 데이터가 제거된다면 데이터도 함께 사라진다. 이 주소값을 외부에 전송했다고 하더라도 실제 메모리 주소에는 객체가 존재하지 않을 수 있다.

따라서 참조타입의 객체들은 원시값 형식으로 데이터를 변환하는 과정을 거쳐 전달해야 한다.

## 4. 예제

### 1) Serializable 인터페이스

```java
public class Person implements Serializable {

    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

}
```

### 2) 직렬화

```java
public static void main(String[] args) {

        Person person = new Person("김OO", 20);

        byte[] serialized;

        try {
            ByteArrayOutputStream baos = new ByteArrayOutputStream();
            ObjectOutputStream oos = new ObjectOutputStream(baos);
            oos.writeObject(person);

            // serialized -> 직렬화된 Person 객체
            serialized = baos.toByteArray();
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
        // 바이트 배열로 생성된 직렬화 데이터를 base64로 변환
        System.out.println(Base64.getEncoder().encodeToString(serialized));
        
        // 출력 결과
        // rO0ABXNyAB/sp4HroKztmZTsmYDsl63sp4HroKztmZQuUGVyc29uc1NTeAAdcbkCAAJJAANhZ2VMAARuYW1ldAASTGphdmEvbGFuZy9TdHJpbmc7eHAAAAAUdAAF6rmAT08=

    }
```

### 3) 역직렬화
역직렬화의 조건은 자바 직렬화 대상 객체가 동일한 serialVersionID를 가지고 있어야한다.

```java
    public static void main(String[] args) {

        byte[] serialized = { -84, -19, 0, 5, 115, 114, 0, 31, -20, -89, ... };

        try {
            ByteArrayInputStream bais = new ByteArrayInputStream(serialized);
            ObjectInputStream ois = new ObjectInputStream(bais);
            
            // 역직렬화된 Person 객체
            Person newObject = (Person) ois.readObject();
            System.out.println("newObject = " + newObject);
            // 출력
            // newObject = Person { name = '김OO', age = 20 }
        } catch (IOException | ClassNotFoundException e) {
            throw new RuntimeException(e);
        }

    }
```

* 특정 필드의 값을 직렬화하고 싶지 않으면 `transient`를 붙인다.

```java
public class Person implements Serializable {

    private transient String name;
    private int age;

}

// 역직렬화 결과
// Person { name = null, age = 20 }
```

## 5. 오류 사항

### 1) 객체의 멤버변수가 추가되었을 경우
`InvalidClassException` 예외가 발생한다. serialVersionUID가 일치하지 않는다는 뜻이다.

멤버변수의 추가로인해 `serialVersionUID`가 새로운 값을 가지면서 이전 `serialVersionUID` 값과 일치하지 않아 생긴 예외이다.

```text
Caused by: java.io.InvalidClassException: Person; local class incompatible: stream classdesc serialVersionUID = 8310077512291807673, local class serialVersionUID = 2986034573208750977
```

`serialVersionUID`를 명시하면 멤버 변수를 추가하여도 에러가 발생하지 않는다. 기존 멤버변수를 삭제하여도 에러가 발생하지 않는다.

```java
public class Person implements Serializable {

    @Serial
    private static final long serialVersionUID = 1L;
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return String.format("Person { name = '%s', age = %d }", name, age);
    }
}
```

### 2) 객체의 멤버변수의 타입이 변경되었을 경우
age 타입을 `int`에서 `long`으로 변경하면 `InvalidClassException`이 발생한다.

```java
public class Person implements Serializable {

    @Serial
    private static final long serialVersionUID = 1L;
    private String name;
    private long age;

    public Person(String name, long age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return String.format("Person { name = '%s', age = %d }", name, age);
    }
}

// java.io.InvalidClassException: Person; incompatible types for field age
```

## [출처 및 참고]
* [https://tmd8633.tistory.com/28](https://tmd8633.tistory.com/28)
* [https://inkyu-yoon.github.io/docs/Language/Java/Serialization](https://inkyu-yoon.github.io/docs/Language/Java/Serialization)
* [https://happydhkim.tistory.com/entry/%EC%A7%81%EB%A0%AC%ED%99%94Serialization%EC%99%80-%EC%97%AD%EC%A7%81%EB%A0%AC%ED%99%94Deserialization#google_vignette](https://happydhkim.tistory.com/entry/%EC%A7%81%EB%A0%AC%ED%99%94Serialization%EC%99%80-%EC%97%AD%EC%A7%81%EB%A0%AC%ED%99%94Deserialization#google_vignette)
