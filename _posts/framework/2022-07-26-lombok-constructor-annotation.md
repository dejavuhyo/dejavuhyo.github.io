---
title: Lombok 생성자 관련 어노테이션
author: dejavuhyo
date: 2022-07-26 09:30:00 +0900
categories: [Application, Framework]
tags: [lombok-constructor-annotation, constructor-annotation, lombok, noargsconstructor, requiredargsconstructor, allargsconstructor, 롬복-생성자, 롬복-생성자-어노테이션, 롬복]
---

## 1. Lombok 생성자
Lombok이란 Java의 라이브러리로 반복되는 메소드를 Annotation을 사용해서 자동으로 작성해주는 라이브러리이다.

귀찮은 과정을 줄여주고 반복되는 코드 작성을 대신 해준다는 점에서 많은 개발자가 선호하는 라이브러리이지만, 호불호가 갈리는 라이브러리이기도 하므로 프로젝트에 도입하는 경우 주의해야 한다.

또한, 잘 모르고 사용하면 에러가 발생할 수 있음에 주의해야 한다.

```java
@NoArgsConstructor
@RequiredArgsConstructor
@AllArgsConstructor
public class Board {
    @NotNull
    private Long idx;
    private String content;
}
```

## 2. @NoArgsConstructor
파라미터가 없는 기본 생성자를 생성한다. 초기값 세팅이 필요한 final 변수가 있을 경우 컴파일 에러가 발생함으로 주의한다. `@NoArgsConstructor(force=true)`를 사용하면 null, 0 등 기본 값으로 초기화 된다.

```java
Board board = new Board();
```

## 3. @RequiredArgsConstructor
final 변수, `@NotNull`인 변수처럼 필수적인 정보를 세팅하는 생성자를 만든다.

```java
Board board = new Board(2L);
```

## 4. @AllArgsConstructor
모든 필드 값을 파라미터로 받는 생성자를 만든다.

```java
Board board = new Board(2L, "content value");
```

## [출처 및 참고]
* [https://goyunji.tistory.com/98](https://goyunji.tistory.com/98)
* [https://athena7.tistory.com/entry/Lombok-NoArgsConstructor-AllArgsConstructor-RequiredArgsConstructor](https://athena7.tistory.com/entry/Lombok-NoArgsConstructor-AllArgsConstructor-RequiredArgsConstructor)
