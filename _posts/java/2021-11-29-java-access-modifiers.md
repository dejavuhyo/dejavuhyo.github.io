---
title: Java 액세스 수정자
author: dejavuhyo
date: 2021-11-29 09:30:00 +0900
categories: [Language, Java]
tags: [java-access-modifiers, access-modifiers, 자바-액세스-수정자, 액세스-수정자]
---

## 1. 액세스 수정자
public, private, protected 및 default (키워드 없음)의 네 가지 액세스 수정자가 있다.

최상위 클래스는 공용 또는 기본 액세스 한정자를 사용할 수 있다는 점에 유한다.

## 2. Default
명시적으로 키워드를 사용하지 않으면 Java는 지정된 클래스, 메서드 또는 속성에 대한 기본 액세스를 설정 한다. 기본 액세스 수정자는 package-private라고도 하며, 이는 모든 멤버가 동일한 패키지 내에서 표시 되지만 다른 패키지에서는 액세스할 수 없음을 의미한다.

```java
package com.baeldung.accessmodifiers;

public class SuperPublic {
    static void defaultMethod() {
        ...
    }
}
```

defaultMethod()는 동일한 패키지의 다른 클래스에서 액세스할 수 있다.

```java
package com.baeldung.accessmodifiers;

public class Public {
    public Public() {
        SuperPublic.defaultMethod(); // Available in the same package.
    }
}
```

그러나 다른 패키지에서는 사용할 수 없다.

## 3. Public
클래스, 메서드 또는 속성에 public 키워드를 추가하면 전 세계에서 사용할 수 있게 된다. 즉, 모든 패키지의 다른 모든 클래스에서 사용할 수 있다. 다음은 가장 제한이 적은 액세스 수정자이다.

```java
package com.baeldung.accessmodifiers;

public class SuperPublic {
    public static void publicMethod() {
        ...
    }
}
```

publicMethod()는 다른 패키지에서 사용할 수 있다.

```java
package com.baeldung.accessmodifiers.another;

import com.baeldung.accessmodifiers.SuperPublic;

public class AnotherPublic {
    public AnotherPublic() {
        SuperPublic.publicMethod(); // Available everywhere. Let's note different package.
    }
}
```

## 4. Private
private 키워드가 있는 모든 메서드, 속성 또는 생성자는 동일한 클래스에서만 액세스할 수 있다. 이것은 가장 제한적인 액세스 수정자이며 캡슐화 개념의 핵심이다. 모든 데이터는 외부 세계에서 숨겨진다.

```java
package com.baeldung.accessmodifiers;

public class SuperPublic {
    static private void privateMethod() {
        ...
    }
    
     private void anotherPrivateMethod() {
         privateMethod(); // available in the same class only.
    }
}
```

## 5. Protected
공개 및 비공개 액세스 수준 사이에는 보호된 액세스 수정자가 있다.

protected 키워드로 메서드, 속성 또는 생성자를 선언하면 동일한 패키지(패키지 개인 액세스 수준에서와 같이)와 해당 클래스의 모든 하위 클래스에서 멤버에 액세스할 수 있다. 다른 패키지에 있더라도 다음과 같다.

```java
package com.baeldung.accessmodifiers;

public class SuperPublic {
    static protected void protectedMethod() {
        ...
    }
}
```

protectedMethod()는 하위 클래스에서 사용할 수 있다(패키지에 관계없이).

```java
package com.baeldung.accessmodifiers.another;

import com.baeldung.accessmodifiers.SuperPublic;

public class AnotherSubClass extends SuperPublic {
    public AnotherSubClass() {
        SuperPublic.protectedMethod(); // Available in subclass. Let's note different package.
    }
}
```

## 6. 비교
아래 표에는 사용 가능한 액세스 수정자가 요약되어 있다. 사용된 액세스 한정자와 상관없이 클래스는 항상 해당 멤버에 액세스할 수 있음을 알 수 있다.

| Modifier | Class | Package | Subclass | World |
|:---:|:---:|:---:|:---:|:---:|
| public | Y | Y | Y | Y |
| protected | Y | Y | Y | N |
| default | Y | Y | N | N |
| private | Y | N | N | N |

오용을 방지하기 위해 주어진 구성원에 대해 가능한 가장 제한적인 액세스 수준을 사용하는 것이 좋다. 합당한 이유가 없는한 항상 private access modifier를 사용해야 한다.

공개 액세스 수준은 구성원이 API의 일부인 경우에만 사용해야 한다.

## [출처 및 참고]
* <https://www.baeldung.com/java-access-modifiers>
