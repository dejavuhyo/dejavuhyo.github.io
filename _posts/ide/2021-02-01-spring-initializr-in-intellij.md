--- 
title: IntelliJ에서 Spring Initializr
author: Hyosik
date: 2021-02-01 16:30:00 +0900
categories: [Tools, IDE]
tags: [intellij-spring-initializr, spring-initializr, intellij, intellij-springboot, spring-quickstart, 인텔리제이-스프링부트]
---

## 1. Spring Initializr
SpringBoot 프로젝트 구조를 만드는 웹 애플리케이션이다. 기본적인 프로젝트 구조와 코드를 빌드하는 데 필요한 maven이나 gradle 빌드 명세를 만들어준다. Spring Initializr는 웹 기반 인터페이스, Spring Tool Suite(STS), IntelliJ IDE, SpringBoot CLI로 사용할 수 있다.

* [Spring Quickstart Guide](https://spring.io/quickstart)

* [웹 기반 Spring Initializr](https://start.spring.io/)

![img001](/assets/img/2021-02-01-spring-initializr-in-intellij/img001.png)

## 2. IntelliJ에서 Spring Initializr

### 1) New Project
Spring Initializr를 선택하고 Project SDK를 설정

![img002](/assets/img/2021-02-01-spring-initializr-in-intellij/img002.png)

### 2) Project Metadata
프로젝트의 기본적인 정보를 설정

![img003](/assets/img/2021-02-01-spring-initializr-in-intellij/img003.png)

### 3) Dependencies 추가
프로젝트에서 필요한 의존성을 추가

![img004](/assets/img/2021-02-01-spring-initializr-in-intellij/img004.png)

### 4) 저장 경로 지정
프로젝트 저장 경로 지정

![img005](/assets/img/2021-02-01-spring-initializr-in-intellij/img005.png)

## [출처 및 참고]
* <https://spring.io/quickstart>
* <https://start.spring.io/>
* <https://jongmin92.github.io/2018/02/04/Spring/springboot-start/>
