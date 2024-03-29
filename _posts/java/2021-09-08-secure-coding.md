---
title: 시큐어 코딩
author: dejavuhyo
date: 2021-09-08 06:00:00 +0900
categories: [Language, Java]
tags: [secure-coding, secure-coding-guide, 시큐어-코딩, 시큐어-코딩-가이드]
---

## 1. 시큐어 코딩이란
소프트웨어 개발 보안 또는 시큐어 코딩(Secure Coding)이란 안전한 소프트웨어 개발을 위해 소스 코드 등에 존재할 수 있는 잠재적인 보안 취약점을 제거하고, 보안을 고려하여 기능을 설계 및 구현하는 등 소프트웨어 개발 과정에서 지켜야 할 일련의 보안 활동을 말한다.

인터넷 홈페이지나 소프트웨어 개발 시 보안 취약점을 악용한 해킹 등 내외부 공격으로부터 시스템을 안전하게 방어할 수 있도록 코딩하는 것이 여기에 해당한다.

미국은 2002년 연방정보보안관리법(FISMA)을 제정해 시큐어 코딩을 의무화했고, 마이크로소프트는 윈도우 비스타를 개발할 때 시큐어 코딩을 도입했다.

대한민국의 경우 2012년 12월부터 SW 개발 단계부터 보안 취약점을 제거하는 'SW 개발 보안 의무제'가 시행되었다. 이에 따라 공공기관의 정보화 사업 중 40억원 이상의 발주는 시큐어 코딩을 의무화했고, 2014년 12월 31일까지는 20억원 미만의 사업에도 의무적으로 적용된다.

## 2. 시큐어 코딩 가이드
시큐어 코딩은 개발단계에서 적용되기 때문에 개발자의 코딩 작업이 핵심 대상이 된다. 그러나 개발자로서 취약점을 모두 고려하는 프로그래밍이란 어려운 일이다. 따라서 어떠한 규칙에 따라 코딩을 하면 되는지에 대한 기준이 있으면 좋을 것이다. 그리고 실제로 국내에서는 2012년 12월부터 행정안전부에 의해 시큐어 코딩에 대한 법규가 제정, 시행되어 그 기준을 제시하고 있다.

### 1) 입력데이터 검증 및 표현
프로그램 입력값에 대한 검증 누락 또는 부적절한 검증, 데이터의 잘못된 형식지정으로 인해 발생할 수 있는 보안 약점이다.

### 2) 보안 기능
보안 기능(인증, 접근제어, 기밀성, 암호화, 권한 관리 등)을 적절하지 않게 구현 시 발생할 수 있는 보안 약점이다.

### 3) 시간 및 상태
동시 또는 거의 동시 수행을 지원하는 병렬 시스템, 하나 이상의 프로세스가 동작하는 환경에서 시간 및 상태를 부적절하게 관리하여 발생할 수 있는 보안 약점이다.

### 4) 에러 처리
에러를 처리하지 않거나, 불충분하게 처리하여 에러 정보에 중요정보(시스템 등)가 포함될 때 발생할 수 있는 보안 약점이다.

### 5) 코드 오류
타입변환 오류, 자원(메모리 등)의 부적절한 반환 등과 같이 개발자가 범할 수 있는 코딩오류로 인해 유발되는 보안 약점이다.

### 6) 캡슐화
중요한 데이터 또는 기능성을 불충분하게 캡슐화하였을 때, 인가되지 않는 사용자에게 데이터 누출이 가능해지는 보안 약점이다.

### 7) API 오용
의도된 사용에 반하는 방법으로 API를 사용하거나, 보안에 취약한 API를 사용하여 발생할 수 있는 보안 약점이다.

## 3. 보안약점과 보안취약점

### 1) CWE(Common Weakness Enumeration)
보안약점(Weakness): 보안취약점의 근본 원인이 되는 SW 허점, 결점, 오류 등

* [약점에 대한 정보 제공](https://cwe.mitre.org)

### 2) CVE(Common Vulnerabilities and Exposures)
보안취약점(Vulnerability): 해킹 등 실제 보안사고에 이용되는 SW 보안약점

* [보안 취약점에 대한 정보 제공](https://cve.mitre.org)

## [출처 및 참고]
* [https://ko.wikipedia.org/wiki/%EC%86%8C%ED%94%84%ED%8A%B8%EC%9B%A8%EC%96%B4_%EA%B0%9C%EB%B0%9C_%EB%B3%B4%EC%95%88](https://ko.wikipedia.org/wiki/%EC%86%8C%ED%94%84%ED%8A%B8%EC%9B%A8%EC%96%B4_%EA%B0%9C%EB%B0%9C_%EB%B3%B4%EC%95%88)
* [https://www.devkuma.com/books/pages/1165](https://www.devkuma.com/books/pages/1165)
* [https://www.kisa.or.kr/public/laws/laws3_View.jsp?cPage=6&mode=view&p_No=259&b_No=259&d_No=55&ST=T&SV=](https://www.kisa.or.kr/public/laws/laws3_View.jsp?cPage=6&mode=view&p_No=259&b_No=259&d_No=55&ST=T&SV=)
