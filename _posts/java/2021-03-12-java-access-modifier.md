---
title: Java 접근 제어자
author: dejavuhyo
date: 2021-03-12 06:00:00 +0900
categories: [Language, Java]
tags: [access-modifier, java-access-modifier, private, default, protected, public, 접근-제어자, 자바-접근-제어자]
---

## 1. 접근 제어자
자바에는 다음과 같은 접근 제어자가 있다.

* private

* default

* protected

* public

private → default → protected → public 순으로 보다 많은 접근을 허용한다.

## 2. private
접근 제어자가 private으로 설정되었다면 private 이 붙은 변수, 메소드는 해당 클래스에서만 접근이 가능하다.

```java
public class AccessModifier {
    private String secret;
    private String getSecret() {
        return this.secret;
    }
}
```

secret 변수와 getSecret 메소드는 오직 AccessModifier 클래스에서만 접근이 가능하고 다른 클래스에서는 접근이 불가능하다.

## 3. default
접근 제어자를 별도로 설정하지 않는다면 접근 제어자가 없는 변수, 메소드는 default 접근 제어자가 되어 해당 패키지 내에서만 접근이 가능하다.

```java
package jump2java.house;

public class HouseKim {
    String lastname = "kim";
}
```

```java
package jump2java.house;

public class HousePark {
    String lastname = "park";

    public static void main(String[] args) {
        HouseKim kim = new HouseKim();
        System.out.println(kim.lastname);
    }
}
```

HouseKim과 HousePark의 패키지는 jump2java.house로 동일하다. HouseKim의 lastname 변수는 접근 제어자가 default이므로 HousePark 클래스에서 main 메소드에서 사용한 것과 같이 kim.lastname 으로 HouseKim의 lastname 변수에 접근이 가능하다.

## 4. protected
접근 제어자가 protected로 설정되었다면 protected가 붙은 변수, 메소드는 동일 패키지내의 클래스 또는 해당 클래스를 상속받은 외부 패키지의 클래스에서 접근이 가능하다.

```java
package jump2java.house;

public class HousePark {
    protected String lastname = "park";
}
```

```java
package jump2java.house.person;

import house.HousePark;

public class EungYongPark extends HousePark {
    public static void main(String[] args) {
        EungYongPark eyp = new EungYongPark();
        System.out.println(eyp.lastname);
    }
}
```

HousePark클래스를 상속받은 EungYongPark이라는 클래스의 패키지는 jump2java.house.person으로 HousePark의 패키지인 jump2java.house와 다르지만 HousePark의 lastname 변수가 protected로 설정되었기 때문에 eyp.lastname과 같은 접근이 가능하다.

만약 lastname의 접근 제어자가 protected가 아닌 default 접근 제어자였다면 eyp.lastname 문장은 컴파일 에러를 유발할 것이다.

## 5. public
접근 제어자가 public으로 설정되었다면 public 접근 제어자가 붙은 변수, 메소드는 어떤 클래스에서라도 접근이 가능하다.

```java
package jump2java.house;

public class HousePark {
    protected String lastname = "park";
    public String info = "this is public message.";
}
```

HousePark의 info 변수는 public 접근 제어자가 붙어 있음으로 어떤 클래스에서든지 접근이 가능하다.

> 클래스 내의 클래스를 inner 클래스라고 부르는데 이러한 inner 클래스에도 역시 접근 제어자를 붙여서 접근을 제어할 수 있다.

접근 제어자를 모두 public으로 설정해도 프로그램은 잘 동작할 것이다. 하지만 접근 제어자를 이용하면 프로그래머의 코딩 실수를 방지할 수 있고 기타 위험요소를 제거할 수 있는 등의 많은 장점이 있다.

## 출처 및 참고
* <https://wikidocs.net/232>
