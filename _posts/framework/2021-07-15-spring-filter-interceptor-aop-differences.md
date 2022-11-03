---
title: Spring Filter, Interceptor, AOP 차이
author: dejavuhyo
date: 2021-07-15 03:00:00 +0900
categories: [Application, Framework]
tags: [spring-filter, spring-interceptor, spring-aop, filter, interceptor, aop, filter-interceptor-aop, 스프링-필터, 스프링-인터셉터, 스프링-aop, 필터, 인터셉터, 필터-인터셉터-aop]
---

## 1. Filter, Interceptor, AOP의 흐름

![flow](/assets/img/2021-07-15-spring-filter-interceptor-aop-differences/flow.png)

* Filter와 Interceptor는 Servlet 단위에서 실행된다. 반면 AOP는 메소드 앞에서 Proxy 패턴으로 실행된다.

* 필터를 웹 컨테이너 내에 생성한 후 초기화 시 init()이 호출된다. 그리고 doFilter가 호출된다.

* 컨트롤러에 들어가기 전에 preHandler()가 실행된다.

* 컨트롤러에 나와 postHandler(), afterCompletion(), doFilter() 순으로 실행된다.

* 컨트롤러의 메소드 처리가 끝나 return 되고 화면에 띄워주는 처리가 되기 직전에 preHandler()가 호출된다.

* 서블릿 종료 시 Filter.destroy()가 실행된다.

> **실행순서:** Filter → Interceptor → AOP → Interceptor → Filter

## 2. 개념

### 1) Filter
Filter(필터)는 요청과 응답을 거른 뒤 정제하는 역할을 한다.

서블릿 필터는 DispatcherServlet 이전에 실행이 되는데 필터가 동작하도록 지정된 자원의 앞단에서 요청내용을 변경하거나, 여러 가지 체크를 수행할 수 있다.

또한 자원의 처리가 끝난 후 응답 내용에 대해서도 변경하는 처리를 할 수가 있다.

보통 web.xml에 등록하고, 일반적으로 인코딩 변환 처리, XSS 방어 등의 요청에 대한 처리로 사용된다.

* **init():** 필터 인스턴스 초기화

* **doFilter():** 전/후 처리

* **destroy():** 필터 인스턴스 종료

### 2) Interceptor
Interceptor(인터셉터)는 요청에 대한 작업 전, 후로 가로챈다.

Filter는 스프링 컨텍스트 외부에 존재하여 스프링과 무관한 자원에 대해 동작한다.

그러나 인터셉터는 스프링의 DistpatcherServlet이 컨트롤러를 호출하기 전, 후로 끼어들기 때문에 스프링 컨텍스트(Context, 영역) 내부에서 Controller(Handler)에 관한 요청과 응답에 대해 처리한다.

스프링의 모든 빈 객체에 접근할 수 있다. 인터셉터는 여러 개를 사용할 수 있고 로그인 체크, 권한 체크, 프로그램 실행 시간 계산작업 로그 확인 등의 업무처리에 사용한다.

* **preHandler():** 컨트롤러 메소드가 실행되기 전

* **postHanler():** 컨트롤러 메소드 실행직 후 view 페이지 렌더링 되기 전

* **afterCompletion():** view 페이지가 렌더링 되고 난 후

### 3) AOP
AOP는 OOP를 보완하기 위해 나온 개념이다. 객체 지향의 프로그래밍을 했을 때 중복을 줄일 수 없는 부분을 줄이기 위해 종단면(관점)에서 바라보고 처리한다.

주로 '로깅', '트랜잭션', '에러 처리' 등 비즈니스단의 메소드에서 조금 더 세밀하게 조정할 때 사용한다.

Interceptor나 Filter와는 달리 메소드 전후의 지점에 자유롭게 설정이 가능하다. Interceptor와 Filter는 주소로 대상을 구분해서 걸러내야 하는 반면, AOP는 주소, 파라미터, 애노테이션 등 다양한 방법으로 대상을 지정할 수 있다.

AOP의 Advice와 HandlerInterceptor의 가장 큰 차이는 파라미터의 차이다. Advice의 경우 JoinPoint나 ProceedingJoinPoint 등을 활용해서 호출한다. 반면 HandlerInterceptor는 Filter와 유사하게 HttpServletRequest, HttpServletResponse를 파라미터로 사용한다.

* **@Before:** 대상 메소드의 수행 전

* **@After:** 대상 메소드의 수행 후

* **@After-returning:** 대상 메소드의 정상적인 수행 후

* **@After-throwing:** 예외발생 후

* **@Around:** 대상 메소드의 수행 전, 후

## 3. 차이점

### 1) 적용 시점이 다름

* Filter → Interceptor → AOP

### 2) 적용 방식이 다름

* Filter: web.xml

* Interceptor: servlet-context.xml

### 3) 실행 위치가 다름

* Interceptor, Filter: Servlet 단위에서 실행

* AOP: 메소드 앞에 Proxy 패턴의 형태로 실행

## [출처 및 참고]
* <https://justforchangesake.wordpress.com/2014/05/07/spring-mvc-request-life-cycle/>
* <https://twofootdog.github.io/Spring-%ED%95%84%ED%84%B0(Filter),-%EC%9D%B8%ED%84%B0%EC%85%89%ED%84%B0(Interceptor),-AOP-%EC%B0%A8%EC%9D%B4%EC%A0%90/>
* <https://goddaehee.tistory.com/154>
