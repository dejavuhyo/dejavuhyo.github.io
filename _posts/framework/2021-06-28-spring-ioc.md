---
title: Spring IoC
author: dejavuhyo
date: 2021-06-28 06:00:00 +0900
categories: [Application, Framework]
tags: [inversion-of-control, ioc, spring-ioc, 제어의-역전, spring-제어의-역전]
---

## 1. IoC란
IoC(Inversion of Control) '제어의 역전'이라는 의미로 객체의 생성, 생명주기의 관리까지 모든 객체에 대한 제어권이 바뀌었다는 것을 의미한다. 객체의 의존성을 역전 시켜 객체 간의 결합도를 줄이고 유연한 코드를 작성할 수 있게 하여 가독성 및 코드 중복, 유지 보수를 편하게 할 수 있게 한다.

## 2. IoC Container
모든 작업을 사용하는 쪽에서 제어하게 되면서 IoC 컨테이너에서 제어하게 되는데, 기본적으로 컨테이너는 객체를 생성하고 객체 간의 의존성을 이어주는 역할을 한다.

### 1) BeanFactory
BeanFactory 인터페이스는 IOC 컨테이너의 기능을 정의하고 있는 인터페이스이며, Bean의 생성 및 의존성 주입, 생명주기(life cycle) 관리 등의 기능을 제공한다. Bean이란 IOC 컨테이너에 의해 생성되고 관리되는 객체를 의미한다.

### 2) ApplicationContext
BeanFactory 인터페이스를 상속받는 ApplicationContext는 BeanFactory가 제공하는 기능 외에 AOP, 메세지 처리, 이벤트 처리 등의 기능을 제공한다.

모든 ApplicationContext 구현체는 BeanFactory의 기능을 모두 제공하므로, 특별한 경우를 제외하고는 ApplicationContext를 사용하는 것이 바람직하다.

## 3. IoC 사용 목적
IoC를 사용하는 목적은 지금까지의 클래스호출 방식의 변화를 살펴보면 더 쉽게 이해할 수 있다.

### 1) 클래스 호출 방식
클래스내에 선언과 구현이 같이 있기 때문에 다양한 형태로 변화가 불가능하다.

![class](/assets/img/2021-06-28-spring-ioc/class.png)

### 2) 인터페이스 호출 방식
클래스를 인터페이스와 인터페이스를 상속받아 구현하는 클래스로 분리했다. 구현 클래스 교체가 용이하여 다양한 변화가 가능하다. 그러나 구현 클래스 교체 시 호출 클래스 코드에서 수정이 필요하다. (부분적으로 종속적)

![interface](/assets/img/2021-06-28-spring-ioc/interface.png)

### 3) 팩토리 호출 방식
팩토리 방식은 팩토리가 구현 클래스를 생성하기 때문에 호출 클래스는 팩토리를 호출하는 코드로 충분하다. 구현 클래스 변경 시 팩토리만 수정하면 되기 때문에 호출 클래스에는 영향을 미치지 않는다. 그러나 호출 클래스에서 팩토리를 호출하는 코드가 들어가야 하는 것 또한 팩토리에 의존함을 의미한다.

![factory](/assets/img/2021-06-28-spring-ioc/factory.png)

### 4) IoC
팩토리 패턴의 장점을 더해 어떠한 것에도 의존하지 않는 형태이다. 실행 시점에 클래스 간의 관계가 형성된다. 즉, 의존성이 삽입된다는 의미로 IoC를 DI라는 표현으로 사용한다.

![ioc](/assets/img/2021-06-28-spring-ioc/ioc.png)

## 4. IoC 용어

### 1) bean
스프링에서 제어권을 가지고 직접 만들어 관계를 부여하는 오브젝트

Java Bean, EJB의 Bean과 비슷한 오브젝트 단위의 애플리케이션 컴포넌트이다. 그러나 스프링을 사용하는 애플리케이션에서 만들어지는 모든 오브젝트가 빈은 아니다. 스프링의 빈은 스프링 컨테이너가 생성하고 관계설정, 사용을 제어해주는 오브젝트를 말한다.

### 2) bean factory
스프링의 IoC를 담당하는 핵심 컨테이너

Bean을 등록, 생성, 조회, 반환, 관리한다. 보통 bean factory를 바로 사용하지 않고 이를 확장한 application context를 이용한다. BeanFactory는 bean factory가 구현하는 interface이다. (getBean()등의 메서드가 정의되어 있다.)

### 3) application context
bean factory를 확장한 IoC 컨테이너

Bean의 등록, 생성, 조회, 반환, 관리 기능은 bean factory와 같지만, 추가로 spring의 각종 부가 서비스를 제공한다. ApplicationContext는 application context가 구현해야 하는 interface이며, BeanFactory를 상속한다.

### 4) configuration metadata
application context 혹은 bean factory가 IoC를 적용하기 위해 사용하는 메타정보

스프링의 설정 정보는 컨테이너에 어떤 기능을 세팅하거나 조정하는 경우에도 사용하지만 주로 bean을 생성, 구성하는 용도로 사용한다.

### 5) container (ioC container)
IoC 방식으로 bean을 관리한다는 의미에서 bean factory나 application context를 가리킴

application context는 그 자체로 ApplicationContext 인터페이스를 구현한 오브젝트를 말하기도 하는데, 하나의 애플리케이션에 보통 여러 개의 ApplicationContext 객체가 만들어진다. 이를 통칭해서 spring container라고 부를 수 있다.

## [출처 및 참고]
* <https://jongmin92.github.io/2018/02/11/Spring/spring-ioc-di/>
* <https://gangnam-americano.tistory.com/60>
