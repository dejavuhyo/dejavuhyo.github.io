---
title: Java ArrayList
author: dejavuhyo
date: 2022-08-19 09:30:00 +0900
categories: [Language, Java]
tags: [java-arraylist, java-list, arraylist, list, list-add, list-remove, list-clear, map-keyset, 자바-arraylist, 자바-list, arraylist-선언, arraylist-값-추가, arraylist-값-삭제, arraylist-값-출력]
---

## 1. ArrayList
ArrayList는 List 인터페이스를 상속받은 클래스로 크기가 가변적으로 변하는 선형리스트이다. 일반적인 배열과 같은 순차리스트이며 인덱스로 내부의 객체를 관리한다는점등이 유사하지만, 한번 생성되면 크기가 변하지 않는 배열과는 달리 ArrayList는 객체들이 추가되어 저장 용량(capacity)을 초과한다면 자동으로 부족한 크기만큼 저장 용량(capacity)이 늘어난다는 특징을 가지고 있다.

![java-arraylist](/assets/img/2022-08-19-java-arraylist/java-arraylist.png)

## 2. ArrayList 선언
ArrayList선언시 `ArrayList list = new ArrayList()`로 선언 후 내부에 임의의 값을 넣고 사용할 수도 있지만 이렇게 사용할 경우 값을 뽑아내기 위해서는 캐스팅(Casting) 연산이 필요하고 잘못된 타입으로 캐스팅을 한 경우에는 에러가 발생하기 때문에 위와 같은 방식은 추천하지 않는다.

ArrayList를 사용할 시에는 ArrayList에 타입을 명시해주는 것이 좋다. JDK 5.0 이후부터 자료형의 안정성을 위해 제너릭스(Generics)라는 개념이 도입되었다.

`ArrayLIst<String> list = new ArrayList<String>();` 이라고 되어있다면 String 객체들만 add 될 수 있고 다른 타입의 객체는 사용이 불가능하다.

```java
ArrayList list = new ArrayList(); // 타입 미설정 Object로 선언
ArrayList<Student> members = new ArrayList<Student>(); // 타입설정 Student 객체만 사용가능
ArrayList<Integer> num = new ArrayList<Integer>(); // 타입설정 int 타입만 사용가능
ArrayList<Integer> num2 = new ArrayList<>(); // new에서 타입 파라미터 생략가능
ArrayList<Integer> num3 = new ArrayList<Integer>(10); // 초기 용량(capacity)지정
ArrayList<Integer> list2 = new ArrayList<Integer>(Arrays.asList(1, 2, 3)); // 생성시 값 추가
```

> 제네릭스는 선언할 수 있는 타입이 객체 타입이다. int는 기본자료형이기 때문에 들어갈 수 없으므로 int를 객체화시킨 wrapper 클래스를 사용해야 한다.

## 3. ArrayList 값 추가
ArrayList에 값을 추가하려면 ArrayList의 `add(index, value)` 메소드를 사용한다. index를 생략하면 ArrayList 맨 뒤에 데이터가 추가되며 index 중간에 값을 추가하면 해당 인덱스부터 마지막 인덱스까지 모두 1씩 뒤로 밀려난다.

![arraylist-add](/assets/img/2022-08-19-java-arraylist/arraylist-add.png)

이 경우 데이터가 늘어나면 늘어날수록 성능에 악영향이 미치기에 중간에 데이터를 insert를 해야 할 경우가 많다면 ArrayList보다는 LinkedList를 활용한다.

```java
ArrayList<Integer> list = new ArrayList<Integer>();
list.add(3); // 값 추가
list.add(null); // null 값도 add 가능
list.add(1, 10); // index 1에 10 삽입
```

```java
ArrayList<Student> members = new ArrayList<Student>();
Student student = new Student(name, age);
members.add(student);
members.add(new Student("홍길동", 15));
```

## 4. ArrayList 값 삭제
ArrayList에 값을 제거하려면 ArrayList의 `remove(index)` 메소드를 사용한다. `remove()` 함수를 사용하여 특정 인덱스의 객체를 제거하면 바로 뒤 인덱스부터 마지막 인덱스까지 모두 앞으로 1씩 당겨진다. 모든 값을 제거하려면 `clear()` 메소드를 사용한다.

```java
ArrayList<Integer> list = new ArrayList<Integer>(Arrays.asList(1, 2, 3));
list.remove(1); //index 1 제거
list.clear(); //모든 값 제거
```

## 5. ArrayList 크기 구하기
ArrayList의 크기를 구하려면 ArrayList의 `size()` 메소드를 사용한다.

```java
ArrayList<Integer> list = new ArrayList<Integer>(Arrays.asList(1, 2, 3));
System.out.println(list.size()); // list 크기 : 3
```

## 6. ArrayList 값 출력
ArrayList의 `get(index)` 메소드를 사용하면 ArrayList의 원하는 index의 값이 리턴된다. 전체 출력은 for 문을 통해서 출력하고 Iterator를 사용해서 출력을 할 수도 있다.

```java
ArrayList<Integer> list = new ArrayList<Integer>(Arrays.asList(1, 2, 3));

System.out.println(list.get(0)); // 0번째 index 출력

for (Integer i : list) { // for문을 통한 전체출력
    System.out.println(i);
}

Iterator iter = list.iterator(); // Iterator 선언
while (iter.hasNext()) { // 다음값이 있는지 체크
    System.out.println(iter.next()); // 값 출력
}
```

## 7. ArrayList 값 검색
ArrayList에서 찾고자 하는 값을 검색하려면 ArrayList의 `contains(value)` 메소드를 사용한다. 값이 있으면 true가 리턴되고 값이 없으면 false가 리턴된다.

값이 있는 index를 찾으려면 `indexOf(value)` 메소드를 사용하고 값이 없다면 `-1`을 리턴한다.

```java
ArrayList<Integer> list = new ArrayList<Integer>(Arrays.asList(1, 2, 3));
System.out.println(list.contains(1)); // list에 1이 있는지 검색 : true
System.out.println(list.indexOf(1)); // 1이 있는 index반환 없으면 -1
```

## [출처 및 참고]
* <https://coding-factory.tistory.com/551>
