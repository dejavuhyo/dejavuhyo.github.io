---
title: Java Packages
author: dejavuhyo
date: 2021-11-26 09:30:00 +0900
categories: [Language, Java]
tags: [java-packages, packages, 자바-packages, 자바-패키지]
---

## 1. 자바 패키지
Java에서는 패키지를 사용하여 관련 클래스, 인터페이스 및 하위 패키지를 그룹화한다.

이 작업의 주요 이점은 다음과 같다.

* 관련 유형을 더 쉽게 찾을 수 있도록 한다. 패키지에는 일반적으로 논리적으로 관련된 유형이 포함된다.

* 이름 충돌 방지: 패키지가 클래스를 고유하게 식별하는 데 도움이 됩니다. 예를 들어 com.baeldung.Application 클래스와 com.example.Application 클래스를 사용할 수 있다.

* 액세스 제어: 패키지와 액세스 수정자를 결합하여 유형에 대한 가시성과 액세스를 제어할 수 있다.

## 2. 패키지 생성
패키지를 생성하려면 파일의 첫 번째 코드 줄에 패키지 문을 추가 하여 패키지문을 사용해야 한다.

com.baeldung.packages라는 이름의 패키지에 유형을 배치해 보겠다.

```java
package com.baeldung.packages;
```

각각의 새로운 유형을 패키지에 넣는 것이 좋다. 유형을 정의하고 패키지에 넣지 않으면 기본 또는 이름 없는 패키지로 이동 한다. 기본 패키지를 사용하면 몇 가지 단점이 있다.

* 패키지 구조를 갖는 이점을 상실하고 하위 패키지를 가질 수 없다.

* 다른 패키지에서 기본 패키지의 유형을 가져올 수 없다.

* 보호된 액세스 범위 및 패키지 전용 액세스 범위는 의미가 없다.

Java 언어 사양에서 알 수 있듯이 이름이 지정되지 않은 패키지는 주로 소규모 또는 임시 응용 프로그램을 개발할 때 또는 개발을 막 시작할 때 편의를 위해 Java SE 플랫폼에서 제공한다.

따라서 실제 응용 프로그램에서 이름이 지정되지 않거나 기본 패키지를 사용하지 않아야 한다.

### 1) 명명 규칙
같은 이름의 패키지를 피하기 위해 몇 가지 명명 규칙을 따른다.

* 패키지 이름을 모두 소문자로 정의한다.

* 패키지 이름은 마침표로 구분된다.

* 이름은 또한 이름을 만든 회사 또는 조직에 의해 결정된다.

조직을 기반으로 패키지 이름을 결정하기 위해 일반적으로 회사 URL을 역순으로 시작한다. 그 후 명명 규칙은 회사에서 정의하며 부서 이름과 프로젝트 이름을 포함할 수 있다.

예를 들어, www.baeldung.com 에서 패키지를 만들려면 그것을 반대로 한다.

```text
com.baeldung
```

그런 다음 com.baeldung.packages 또는 com.baeldung.packages.domain과 같은 하위 패키지를 추가로 정의할 수 있다.

### 2) 디렉토리 구조
Java의 패키지는 디렉토리 구조에 해당한다.

각 패키지와 하위 패키지에는 자체 디렉토리가 있다. 따라서 com.baeldung.packages 패키지의 경우 com → baeldung → packages의 디렉토리 구조를 가져야 한다.

대부분의 IDE는 패키지 이름을 기반으로 이 디렉터리 구조를 만드는데 도움이 되므로 직접 만들 필요가 없다.

## 3. 패키지 멤버 사용
domain 이라는 서브패키지에 TodoItem 클래스를 정의하는 것으로 시작한다.

```java
package com.baeldung.packages.domain;

public class TodoItem {
    private Long id;
    private String description;
    
    // standard getters and setters
}
```

### 1) Imports
다른 패키지의 클래스에서 TodoItem 클래스를 사용 하려면 가져와야 한다. 가져온 후에는 이름으로 액세스할 수 있다.

패키지에서 단일 유형을 가져오거나 별표를 사용하여 패키지의 모든 유형을 가져올 수 있다.

전체 도메인 하위 패키지를 가져온다.

```java
import com.baeldung.packages.domain.*;
```

TodoItem 클래스만 가져오 온다.

```java
import com.baeldung.packages.domain.TodoItem;
```

JDK 및 기타 Java 라이브러리도 자체 패키지와 함께 제공된다. 같은 방식으로 프로젝트에서 사용하려는 기존 클래스를 가져올 수 있다.

예를 들어 Java 핵심 List 인터페이스와 ArrayList 클래스를 가져온다.

```java
import java.util.ArrayList;
import java.util.List;
```

그런 다음 단순히 이름을 사용하여 애플리케이션에서 이러한 유형을 사용할 수 있다.

```java
public class TodoList {
    private List<TodoItem> todoItems;

    public void addTodoItem(TodoItem todoItem) {
        if (todoItems == null) {
            todoItems = new ArrayList<TodoItem>();
        }
        todoItems.add(todoItem);
    }
}
```

Java 코어 클래스와 함께 새 클래스를 사용하여 ToDoItems 목록을 만들었다.

### 2) 정규화된 이름
때로는 다른 패키지에서 같은 이름을 가진 두 개의 클래스를 사용할 수 있다. 예를 들어 java.sql.Date와 java.util.Date를 모두 사용할 수 있다. 명명 충돌이 발생하면 클래스 중 하나 이상에 대해 정규화된 클래스 이름을 사용해야 한다.

완전한 이름으로 TodoItem을 사용한다.

```java
public class TodoList {
    private List<com.baeldung.packages.domain.TodoItem> todoItems;

    public void addTodoItem(com.baeldung.packages.domain.TodoItem todoItem) {
        if (todoItems == null) {
            todoItems = new ArrayList<com.baeldung.packages.domain.TodoItem>();
        }todoItems.add(todoItem);
    }

    // standard getters and setters
}
```

## 4. javac 컴파일
패키지된 클래스를 컴파일할 때 디렉터리 구조를 기억해야 한다. 소스 폴더에서 시작하여 javac에게 파일을 찾을 위치를 알려야 한다.

TodoList 클래스가 의존 하기 때문에 먼저 TodoItem 클래스를 컴파일해야 한다.

명령줄이나 터미널을 열고 소스 디렉토리로 이동하여 시작한다.

이제 com.baeldung.packages.domain.TodoItem 클래스를 컴파일한다.

```shell
$ javac com/baeldung/packages/domain/TodoItem.java
```

클래스가 정상적으로 컴파일되면 오류 메시지가 표시되지 않고 TodoItem.class 파일이 com/beldung/packages/domain 디렉토리에 나타납니다.

다른 패키지의 유형을 참조하는 유형의 경우 -classpath 플래그를 사용 하여 다른 컴파일된 클래스를 찾을 위치를 javac 명령에 알려야 한다.

이제 TodoItem 클래스가 컴파일 되었으므로 TodoList 및 TodoApp 클래스를 컴파일할 수 있다.

```shell
$ javac -classpath . com/baeldung/packages/*.java
```

오류 메시지가 표시되지 않아야 하며 com/baeldung/packages 디렉토리에서 두 개의 클래스 파일을 찾아야 한다.

TodoApp 클래스의 정규화된 이름을 사용하여 애플리케이션을 실행한다.

```shell
> java com.baeldung.packages.TodoApp
```

출력은 다음과 같다.

![packages](/assets/img/2021-11-26-java-packages/packages.png)

## [출처 및 참고]
* <https://www.baeldung.com/java-packages>
