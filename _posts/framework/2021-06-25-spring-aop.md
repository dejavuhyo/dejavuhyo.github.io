---
title: Spring AOP
author: dejavuhyo
date: 2021-06-25 06:00:00 +0900
categories: [Application, Framework]
tags: [spring-aop, aop, aspect-oriented-programming, 관점-지향-프로그래밍]
---

## 1. 관점 지향 프로그래밍
관점 지향 프로그래밍(aspect-oriented programming, AOP)은 횡단 관심사(cross-cutting concern)의 분리를 허용함으로써 모듈성을 증가시키는 것이 목적인 프로그래밍 패러다임이다.

코드 그 자체를 수정하지 않는 대신 기존의 코드에 추가 동작(어드바이스)을 추가함으로써 수행하며, "함수의 이름이 'set'으로 시작하면 모든 함수 호출을 기록한다"와 같이 어느 코드가 포인트컷(pointcut) 사양을 통해 수정되는지를 따로 지정한다.

이를 통해 기능의 코드 핵심부를 어수선하게 채우지 않고도 비즈니스 로직에 핵심적이지 않은 동작들을 프로그램에 추가할 수 있게 한다. 관점 지향 프로그래밍은 관점 지향 소프트웨어 개발의 토대를 형성한다.

AOP에서 각 관점을 기준으로 로직을 모듈화한다는 것은 코드들을 부분적으로 나누어서 모듈화하겠다는 의미다. 이때, 소스 코드상에서 다른 부분에 계속 반복해서 쓰는 코드들을 발견할 수 있는데 이것을 흩어진 관심사 (Crosscutting Concerns)라 부른다.

![aop](/assets/img/2021-06-25-spring-aop/aop.png)

흩어진 관심사를 Aspect로 모듈화하고 핵심적인 비즈니스 로직에서 분리하여 재사용하겠다는 것이 AOP의 목적이다.

## 2. 주요 개념

### 1) Aspect
여러 곳에서 쓰이는 코드(공통 부분)를 모듈화한 것

### 2) Target
Aspect가 적용되는 곳

### 3) Advice
Aspect에서 실질적인 기능에 대한 구현체

### 4) Joint point
Advice가 Target에 적용되는 시점(ex: 메소드 진입, 생성자 호출, 필드)

### 5) Point cut
Joint point의 상세 스펙을 정의한 것

## 3. 구현

### 1) 의존성 추가

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

### 2) 인터페이스 정의

```java
public interface EventService {
    public void created();
    public void operation();
    public void deleted();
}
```

```java
@Component
public class SimpleServiceEvent implements EventService {

    @Override
    public void created() {
        long begin = System.currentTimeMillis();
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("created");
        System.out.println(System.currentTimeMillis() - begin);
    }

    @Override
    public void operation() {
        System.out.println(System.currentTimeMillis() - begin);
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("operation");
        System.out.println(System.currentTimeMillis() - begin);
    }

    @Override
    public void deleted() {
        System.out.println("deleted");
    }
}
```

### 3) 클래스 활용

```java
@Component
public class AppRuner implements ApplicationRunner {

    @Autowired
    EventService eventService;
    
    @Override
        public void run(ApplicationArguments args) throws Exception {
        eventService.created();
        eventService.operation();
        eventService.deleted();
    }
}
```

* 결과

```text
created
1011
operation
2004
deleted
```

## 4. AOP를 이용한 개선

### 1) Execution Expression
수행 시간을 재는 다음 코드가 여러 곳에서 사용되고 있다. Aspect로 묶어서 관리될 필요가 있다.

* Aspect 클래스정의

```java
// Aspect 정의
@Component
@Aspect
public class PerfAspect {

    // Advice 정의 (Around 사용)
    // Point Cut 표현식
    // (com.example.demo 아래에 있는 모든 클래스 중 EventService 안에 들어있는 모든 메소드에 적용)
    @Around("execution(* com.example..*.EventService.*(..))")
    public Object logPerf(ProceedingJoinPoint pjp) throws Throwable {
        long begin = System.currentTimeMillis();
        Object retVal = pjp.proceed();
        System.out.println(System.currentTimeMillis() - begin);
        return retVal;
    }
}
```

* @Aspect 로 Aspect 클래스임을 정의한다.

* Advice를 정의하고, Point cut 시점은 @Around 형태로 정의한다.
  - @Around, @Before, @After가 있음

* execution...으로 시작하는 부분은 execution expression이라고 한다. Point cut을 설정하는 부분이다.

AOP를 적용했으므로, 적용될 클래스에서 Aspect로 대체된 부분은 삭제한다.

```java
@Component
public class SimpleServiceEvent implements EventService {

    @Override
    public void created() {
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("created");
    }
    
    @Override
    public void operation() {
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("operation");
    }
    
    @Override
    public void deleted() {
        System.out.println("deleted");
    }
}
```

* 결과

```text
created
1011
operation
2004
deleted
0
```

### 2) Annotation 기반
created()와 operation()에만 Apsect를 적용하고 싶은데 delete()에도 적용이 되었다.

* Annotation 생성

```java
@Documented
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.CLASS)
public @interface PerfLogging {
}
```

* Aspect 클래스 수정

```java
@Component
@Aspect
public class PerfAspect {

    @Around("@annotation(PerfLogging)")
        public Object logPerf(ProceedingJoinPoint pjp) throws Throwable {
        long begin = System.currentTimeMillis();
        Object retVal = pjp.proceed();
        System.out.println(System.currentTimeMillis() - begin);
        return retVal;
    }
}
```

execution expression을 @annotation(PerfLogging)으로 대체하였다. 적용될 클래스 메소드에 @PerfLogging을 붙여준다.

```java
@Component
public class SimpleServiceEvent implements EventService {

    @PerfLogging
    @Override
    public void created() {
        ...
    }
    
    @PerfLogging
    @Override
    public void operation() {
        ...
    }
    
    @Override
    public void deleted() {
        ...
    }
}
```

@PerfLogging을 붙인 메소드만 Aspect가 붙는 것을 확인할 수 있다.

* 결과

```text
created
1011
operation
2004
deleted
```

### 3) 특정 bean 기반
simpleServiceEvent 내 모든 public 메소드에 적용하는 방법이다.

```java
// Aspect 정의
@Component
@Aspect
public class PerfAspect {

    // 빈이 가지고있는 모든 퍼블릭 메쏘드
    @Around("bean(simpleServiceEvent)")
    public Object logPerf(ProceedingJoinPoint pjp) throws Throwable {
        long begin = System.currentTimeMillis();
        Object retVal = pjp.proceed();
        System.out.println(System.currentTimeMillis() - begin);
        return retVal;
    }
}
```

* 결과

```text
created
1011
operation
2004
deleted
0
```

## [출처 및 참고]
* <https://ko.wikipedia.org/wiki/%EA%B4%80%EC%A0%90_%EC%A7%80%ED%96%A5_%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D>
* <https://dailyheumsi.tistory.com/202>
* <https://engkimbs.tistory.com/746>
