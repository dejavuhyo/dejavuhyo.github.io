---
title: Visual Studio Code에서 Spring Boot 사용 설정
author: dejavuhyo
date: 2025-08-06 09:55:00 +0900
categories: [DevOps, IDE]
tags: [vscode-spring-boot, vscode-spring-boot-setting, vscode-스프링-부트, vscode-스프링-부트-설정]
---

## 1. VSCode에서 Spring Boot 사용
Visual Studio Code는 Spring Boot 애플리케이션 개발자를 위한 이상적인 경량 개발 환경이며, 다음을 포함하여 여러 가지 유용한 VSCode 확장 기능이 있다.

* [Spring Boot Tools](https://marketplace.visualstudio.com/items?itemName=vmware.vscode-spring-boot)

* [Spring Initializr](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-spring-initializr)

* [Spring Boot Dashboard](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-spring-boot-dashboard)

위의 모든 확장 기능이 포함된 [Spring Boot Extension Pack](https://marketplace.visualstudio.com/items?itemName=vmware.vscode-boot-dev-pack)을 설치하는 것이 좋다.


## 2. 필수 조건
Visual Studio Code에서 Spring Boot 애플리케이션을 개발하려면 다음을 설치해야 한다.

* [Java Development Kit (JDK)](https://adoptium.net/)

* [Extension Pack for Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)

* [Spring Boot Extension Pack](https://marketplace.visualstudio.com/items?itemName=vmware.vscode-boot-dev-pack)

Java Spring Boot 개발을 시작하는데 도움이 되도록 유용한 확장 기능, 설정 및 Java Spring Boot 코드 조각이 포함된 [Java Spring profile template](https://code.visualstudio.com/docs/configure/profiles#_java-spring-profile-template)을 사용할 수 있다.

## 3. 프로젝트 생성
Spring Initializr 확장 프로그램을 사용하면 종속성을 검색하고 새로운 Spring Boot 프로젝트를 생성할 수 있습니다.

설치하려면 VSCode를 실행하고 확장 프로그램 뷰(`Ctrl + Shift + X`)에서 `vscode-spring-initializr`을 검색한다.

확장 프로그램을 설치한 후 명령 팔레트(`Ctrl + Shift + P`)를 열고 Spring Initializr을 입력하여 Maven이나 Gradle 프로젝트를 생성한 다음 마법사를 따른다.

<iframe src="https://code.visualstudio.com/assets/docs/java/java-spring-boot/spring-initializr.mp4" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## 4. 프로젝트 편집
Spring Initializr 확장 기능을 사용하면 새 Spring Boot 프로젝트를 생성한 후 종속성을 추가할 수 있다.

`pom.xml` 파일로 이동하여 마우스 오른쪽 버튼을 클릭하고 '스타터 추가...'를 선택한다. 드롭다운 메뉴에 이미 추가된 종속성이 `√`로 시작하는 항목으로 표시된다. 프로젝트에 추가할 다른 종속성을 검색하거나 기존 종속성을 클릭하여 제거할 수 있다.

<iframe src="https://code.visualstudio.com/assets/docs/java/java-spring-boot/spring-initializr-add-starters.mp4" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## 5. 애플리케이션 개발
[Spring Boot Tools](https://marketplace.visualstudio.com/items?itemName=vmware.vscode-spring-boot) 확장 기능은 Spring Boot `application.properties`, `application.yml` 및 `.java` 파일 작업을 위한 풍부한 언어 지원을 제공한다.

이 확장 기능은 다음과 같은 기능을 지원한다.

* 작업 공간에서 Spring 요소로 빠르게 이동

* Spring 관련 구성 요소에 대한 스마트 코드 완성

* 실행 중인 Spring 앱에 빠르게 액세스

* 라이브 애플리케이션 정보

* 코드 템플릿

`.properties` 및 `.yml` 파일에도 유사한 코드 완성 및 유효성 검사 기능이 제공된다.

이러한 기능의 사용 방법을 알아보려면 [자세한 사용 가이드](https://github.com/spring-projects/spring-tools/tree/main/vscode-extensions/vscode-spring-boot#usage)를 참조한다.

아래는 라이브 애플리케이션 정보를 보여주는 예이다.

<iframe src="https://code.visualstudio.com/assets/docs/java/java-spring-boot/spring-live-info.mp4" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## 6. 애플리케이션 실행
`F5`를 사용하여 애플리케이션을 실행하는 것 외에도 [Spring Boot Dashboard](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-spring-boot-dashboard) 확장 기능이 있다. 이를 통해 작업 공간에서 사용 가능한 모든 Spring Boot 프로젝트를 보고 관리할 수 있을 뿐만 아니라 프로젝트를 빠르게 시작, 중지 또는 디버깅할 수 있다.

<iframe src="https://code.visualstudio.com/assets/docs/java/java-spring-boot/spring-dashboard.mp4" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## [출처 및 참고]
* [https://code.visualstudio.com/docs/java/java-spring-boot](https://code.visualstudio.com/docs/java/java-spring-boot)
