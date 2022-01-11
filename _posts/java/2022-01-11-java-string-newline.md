---
title: Java 문자열에 개행 문자 추가
author: dejavuhyo
date: 2022-01-11 09:40:00 +0900
categories: [Language, Java]
tags: [java-string-newline, java-newline, string-newline, newline, 자바-개행-문자, 개행-문자, 개행, 줄바꿈]
---

## 1. 문자열에 개행 문자 추가
운영 체제에는 새 줄의 시작을 나타내는 특수 문자가 있다. 예를 들어, 리눅스에서 새로운 줄은 라인 피드라고도 불리는 ```\n```으로 표시된다. Windows에서 새 라인은 Carriage Return and Line Feed 또는 CRLF라고도 하는 ```\r\n```을 사용하여 표시된다.

Java에서 새 줄을 추가하는 것은 문자열 끝에 ```\n```, ```\r``` 또는 ```\r \n```을 포함하는 것만큼 간단하다.

### 1) CRLF 줄바꿈 사용
이 예에서는 두 줄의 텍스트를 사용하여 단락을 작성하려고 합니다. 구체적으로, 우리는 2번째 라인이 1번째 라인 다음에 새로운 라인으로 나타나기를 원한다.

Unix/Linux/New Mac 기반 OS의 경우 ```\n```을 사용할 수 있다.

```java
String line1 = "Humpty Dumpty sat on a wall.";
String line2 = "Humpty Dumpty had a great fall.";
String rhyme = line1 + "\n" + line2;
```

Windows 기반 OS를 사용하는 경우 ```\r\n```을 사용할 수 있다.

```java
rhyme = line1 + "\r\n" + line2;
```

이전 Mac 기반 OS의 경우 ```\r```을 사용할 수 있다.

```java
rhyme = line1 + "\r" + line2;
```

새 줄을 추가하는 세 가지 방법을 시연했지만 불행히도 플랫폼에 따라 다르다.

### 2) 플랫폼 독립 줄 구분 기호 사용
코드가 플랫폼에 독립적이기를 원할 때 시스템 정의 상수를 사용할 수 있다.

예를 들어, 줄 구분 기호를 제공하기 위해 System.lineSeparator()를 사용한다.

```java
rhyme = line1 + System.lineSeparator() + line2;
```

또는 System.getProperty("line.separator")를 사용할 수도 있다.

```java
rhyme = line1 + System.getProperty("line.separator") + line2;
```

### 3) 플랫폼 독립 개행 문자 사용
줄 구분자는 플랫폼 독립성을 제공하지만 문자열을 연결해야 한다.

System.out.printf나 String 같은 것을 사용하고 있다면, 플랫폼 독립적인 개행 문자 ```%n```을 문자열 내에서 직접 사용할 수 있다.

```java
rhyme = "Humpty Dumpty sat on a wall.%nHumpty Dumpty had a great fall.";
```

이것은 문자열 내에 System.lineSeparator()를 포함하는 것과 동일 하지만 문자열을 여러 부분으로 나눌 필요가 없다.

## 2. HTML 페이지에 개행 문자 추가
HTML 페이지의 일부인 문자열을 생성한다고 가정한다. 이 경우 HTML 구분 태그 ```<br>```를 추가할 수 있다.

유니코드 문자 ```& #13;```(Carriage Return) 및 ```& #10;```(Line Feed)를 사용할 수도 있다. 이러한 캐릭터는 작동하지만 모든 플랫폼에서 예상한 대로 정확하게 작동하지 않는다. 대신 줄 바꿈에 ```<br>```을 사용하는 것이 좋다.

또한 일부 HTML 요소에서 ```\n```을 사용 하여 줄을 구분할 수 있다.

전반적으로 이들은 HTML에서 줄을 바꾸는 세 가지 방법이다. 사용하는 HTML 태그에 따라 어떤 것을 사용할지 결정할 수 있다.

### 1) HTML 구분 태그
HTML break 태그 <br>를 사용 하여 줄 바꿈을 할 수 있다.

```html
rhyme = line1 + "<br>" + line2;
```

줄 바꿈을 위한 ```<br>``` 태그는 ```<body>```, ```<p>```, ```<pre>``` 등과 같은 거의 모든 HTML 요소에서 동작한다. 그러나 ```<textarea>``` 태그에서는 작동하지 않는다.

### 2) 줄 바꿈 문자
텍스트가 ```<pre>``` 또는 ```<textarea>``` 태그로 둘러싸인 경우 ```\n```을 사용하여 줄을 바꿀 수 있다.

```text
rhyme = line1 + "\n" + line2;
```

### 3) 유니코드 문자
마지막으로 유니코드 문자 ```& #13;```(캐리리지 리턴) 및 ```& #10;```(라인 피드)를 사용하여 줄을 바꿀 수 있다. 예를 들어, ```<textare>``` 태그에서는 다음 중 하나를 사용할 수 있다.

```text
rhyme = line1 + "
" + line2;
rhyme = line1 + "
" + line2;
```

```<pre>``` 태그의 경우 아래 두 줄이 모두 작동한다.

```text
rhyme = line1 + "
" + line2;
rhyme = line1 + "

" + line2;
```

### 4) \n 과 \r 의 차이점
```\r``` 및 ```\n```은 각각 ASCII 값 13(CR) 및 10(LF)으로 표시되는 문자이다. 그들은 모두 두 줄 사이 간격을 나타내지만, 운영 체제에서는 다르게 사용한다.

Windows에서 일련의 두 문자를 사용하여 새 줄을 시작한다. ```CR``` 바로 다음에 ```LF```가 온다. 반대로 Unix 계열 시스템에서는 ```LF```만 사용된다.

Java 응용 프로그램을 작성할 때 응용 프로그램이 실행되는 운영 체제에 따라 다르게 동작하기 때문에 사용하는 줄 바꿈 문자에 주의해야 한다.

가장 안전하고 상호 호환되는 옵션은 System.lineSeparator()를 사용하는 것이다. 이렇게 하면 운영 체제를 고려할 필요가 없다.

## [출처 및 참고]
* <https://www.baeldung.com/java-string-newline>
