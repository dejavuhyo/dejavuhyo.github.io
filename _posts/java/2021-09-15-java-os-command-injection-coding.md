---
title: Java 운영체제 명령어 삽입 코딩 기법
author: dejavuhyo
date: 2021-09-15 06:00:00 +0900
categories: [Language, Java]
tags: [os-command-injection, 운영체제-명령어-삽입]
---

## 1. 정의
운영체제 명령어 삽입(Improper Neutralization of Special Elements Used in an OS Command, OS Command Injection)은 적절한 검증 절차를 수행하지 않은 사용자 입력값이 운영체제 명령어의 일부 또는 전부로 구성되어 실행되는 경우, 의도하지 않은 시스템 명령어가 실행되어 부적절하게 권한이 변경되거나 시스템 동작 및 운영에 악영향을 미칠 수 있다.

일반적으로 명령어 라인의 인수나 스트림 입력 등 외부 입력을 사용하여 시스템 명령어를 생성하는 프로그램이 많이 있다. 하지만 이러면 외부 입력 문자열은 신뢰할 수 없어서 적절한 처리를 해주지 않으면, 공격자가 원하는 명령어 실행이 가능하게 된다.

![os-command-injection](/assets/img/2021-09-15-os-command-injection/os-command-injection.png)

## 2. 안전한 코딩 기법

* 웹 인터페이스를 통해 서버 내부로 시스템 명령어를 전달시키지 않도록 애플리케이션을 구성하고 외부에서 전달되는 값을 그대로 시스템 내부 명령어로 사용하지 않는다.

* 외부 입력에 따라 명령어를 생성하거나 선택이 필요한 경우에는 명령어 생성에 필요한 값들을 미리 지정해 놓고 외부 입력에 따라 선택하여 사용한다.

## 3. 예제
다음의 예제는 cmd.exe 명령어를 사용하여 rmanDB.bat 배치 명령어를 수행하며, 외부에서 전달되는 dir_type 값이 manDB.bat의 인자값으로서 명령어 스트링의 생성에 사용된다.

만약, 외부의 공격자가 의도하지 않은 문자열을 전달할 시, dir_type이 의도했던 인자값이 아닐 경우, 비정상적인 업무를 수행할 수 있다.

* 안전하지 않은 코드의 예

```java
props.load(in);
String version = props.getProperty("dir_type");
String cmd = new String("cmd.exe /K \"rmanDB.bat \"");
Runtime.getRuntime().exec(cmd + " c:\\prog_cmd\\" + version);
```

다음의 예제와 같이 미리 정의된 인자값의 배열을 만들어 놓고, 외부의 입력에 따라 적절한 인자값을 선택하도록 하여, 외부의 부적절한 입력이 명령어로 사용될 가능성을 배제하여야 한다.

* 안전한 코드의 예

```java
props.load(in);
String version[] = {"1.0", "1.1"};
int versionSelection = Integer.parseInt(props.getProperty("version"));
String cmd = new String("cmd.exe /K \"rmanDB.bat \"");
String vs = "";

if (versionSelection == 0)
    vs = version[0];
else if (versionSelection == 1)
    vs = version[1];
else
    vs = version[1];

Runtime.getRuntime().exec(cmd + " c:\\prog_cmd\\" + vs);
```

## [출처 및 참고]
* [https://www.kisa.or.kr/public/laws/laws3_View.jsp?cPage=6&mode=view&p_No=259&b_No=259&d_No=55&ST=T&SV=](https://www.kisa.or.kr/public/laws/laws3_View.jsp?cPage=6&mode=view&p_No=259&b_No=259&d_No=55&ST=T&SV=)
