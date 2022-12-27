---
title: Spring에서 properties 사용시 UnsatisfiedDependencyException 해결 방법
author: dejavuhyo
date: 2021-07-26 06:00:00 +0900
categories: [Application, Framework]
tags: [unsatisfied-dependency-exception, bean-creation-exception, spring-properties, properties-exception, 스프링-properties-에러]
---

## 1. Exception 발생
DB 연동을 위해 DataSource를 정보를 properties 파일에 정의해두고, `@Value` 어노테이션을 통해 값을 주입하였다.

프로그램 실행 중 UnsatisfiedDependencyException과 BeanCreationException이 발생하였다. BeanCreationException은 스프링 Bean 객체 생성 중에 예외가 발생하여 Bean 생성이 되지 못하는 예외이다.

* java 설정

```java
@Component
public class Monitoring {

    @Value("${code}")
    private String code;

    @Value("${price}")
    private String price;

    @Value("${email}")
    private String email;

    public void run() {
        ...
    }
}
```

* properties 설정

```properties
code=005930
price=80,000
```

properties에 email에 해당하는 값이 없어 email에 값이 주입되지 않고 예외가 발생하였다.

## 2. 해결 방법

### 1) properties에 값 추가

* properties

```properties
code=005930
price=80,000
email=email@email.com
```

### 2) 기본값 설정
properties 파일에 값이 입력되지 않았을때 기본으로 주입할 값을 설정해둘 수 있다.

null과 @null의 차이점은 문자열로 인식하느냐 null 값으로 인식하느냐의 차이이다. null로 설정하면 "null" 문자열이 기본값으로 주입되며, @null로 설정하면 null 값이 주입된다. 또한 기본으로 설정해두고 싶은 email을 입력하여도 된다.

* java

```java
@Value("${email:@null}")
private String email;
```

## [출처 및 참고]
* <https://dololak.tistory.com/545>
