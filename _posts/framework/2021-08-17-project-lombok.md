---
title: Project Lombok
author: dejavuhyo
date: 2021-08-17 06:00:00 +0900
categories: [Application, Framework]
tags: [lombok, project-lombok, 롬복]
---

## 1. Lombok이란

![project-lombok](/assets/img/2021-08-17-project-lombok/project-lombok.png)

롬복(Lombok)은 자바에서 Model(DTO, VO, Domain) Object를 만들 때, 멤버필드(프로퍼티)에 대한 Getter, Setter, ToString과 멤버필드에 주입하는 생성자를 만드는 코드 등 불필요하게 반복적으로 만드는 코드를 어노테이션을 통해 줄여 주는 라이브러리, 프로젝트이다.

## 2. 사용

### 1) @Getter, @Setter
필드에 대한 getter, setter를 자동으로 생성해주는 Annotation이다. 예를 들어 필드의 이름이 name이라면 getName()과 setName()을 추가해준다.

```java
// Code
class Person{
    @Getter
    @Setter
    private String name;
}

// Compiled
class Person {
    private String name;

    Person() {
    }

    public String getName() {
        return this.name;
    }

    public void setName(final String name) {
        this.name = name;
    }
}
```

자동으로 생성되는 getter와 setter의 경우 기본은 public이며 AccessLevel을 명시한 경우 PUBLIC, PROTECTED, PACKAGE, PRIVATE 등으로도 생성할 수 있다.

```java
// Code
class Person{
    @Getter(AccessLevel.PRIVATE)
    @Setter(AccessLevel.PROTECTED)
    private String name;
}
```

또한 @Getter ,@Setter를 클래스에 명시할 수도 있다. 이 경우 모든 non-static 필드에 대해 getter와 setter를 추가해준다.

```java
@Getter
@Setter
class Person{
    private String name;
    private int age;
}
```

이름이 같고 매개변수의 수가 같은 메소드가 이미 존재한다면 메소드가 생성되지 않는다.

예를 들어 getName(String... names)가 이미 존재한다면 getName() 메소드는 기술적으로 가능하더라도 생성되지 않는다. 이는 메소드 사용의 혼동을 방지하기 위해서이다. 메소드가 생성되지는 않지만 이에 대해 경고 메시지로 알려준다.

또한 열거형 변수에 @Getter는 사용할 수 있지만 @Setter는 사용할 수 없다.

### 2) @NonNull
메소드나 생성자의 매개변수에 @NonNull을 사용하면 lombok이 null check를 해준다.

```java
// code
class Person{
    private String name;
    private int age;

    public Person(@NonNull String name, int age) {
        this.name = name;
        this.age = age;
    }
}
// build
class Person {
    private String name;
    private int age;

    public Person(@NonNull String name, int age) {
        if (name == null) {
            throw new NullPointerException("name is marked non-null but is null");
        } else {
            this.name = name;
            this.age = age;
        }
    }
}
```

### 3) @ToString
@ToString이 붙은 클래스는 lombok이 toString() 메소드를 생성해준다. 기본적으로는 클래스 이름과 각 필드에 대한 값을 ','으로 구분해서 출력해준다.

```java
// code
@ToString
class Person{
    private String name;
    private int age;
}
// build
// 필요없는 부분은 생략
class Person {
    private String name;
    private int age;
    public String toString() {
        return "Person(name=" + this.name + ", age=" + this.age + ")";
    }
}
```

includeFieldNames를 설정하면 각 필드의 이름과 함께 값을 확인할 수 있다. true가 기본값이다.

```java
// code
@ToString(includeFieldNames = false)
class Person{
    private String name;
    private int age;
}
// build
class Person {
    private String name;
    private int age;
    public String toString() {
        return "Person(" + this.name + ", " + this.age + ")";
    }
}
```

기본적으로 모든 non-static 필드는 toString()에 포함되지만 원한다면 몇몇 필드는 @ToString.Exclude를 사용해서 제외할 수 있다. 아니면 @ToString(onlyExplicitlyIncluded = true)를 사용해서 명시적으로 @ToString.Include가 붙은 필드만 포함할 수도 있다.

```java
// code
@ToString()
class Person{
    @ToString.Exclude
    public static String type = "human";
    private String name;
}
// build
class Person {
    public static String type = "human";
    private String name;
    public String toString() {
        return "Person(name=" + this.name + ")";
    }
}
```

callSuper를 설정하면 슈퍼 클래스의 toString 반환값을 포함할 수도 있다.

다른 메소드의 출력을 toString에 포함할 수도 있다. 다만 매개변수가 없는 인스턴스 메소드(non-static)만 포함할 수 있다. @ToString.Include를 사용하면 된다.

```java
// code
@ToString()
class Person{
    private String name;
    @ToString.Include
    public String greet(){
        return "Hello ";
    }
}
// build
class Person {
    private String name;
    public String greet() {
        return "Hello ";
    }
    public String toString() {
        String var10000 = this.name;
        return "Person(name=" + var10000 + ", greet=" + this.greet() + ")";
    }
}
```

또한 @ToString.Include(name = "custom name")를 사용해서 이름을 바꾸거나 @ToString.Include(rank = -1)를 사용해서 출력 순서를 바꾸는 것도 가능하다. 필드의 기본 rank는 0이다. 높은 값을 가질수록 먼저 출력되며 rank가 같은 경우 소스 파일에 등장하는 순서대로 출력된다.

```java
// code
@ToString()
class Person{
    @ToString.Include(rank=-1, name="Important Field!!!!!")
    private String other;
    private String school;
    private String name;
    @ToString.Include(rank=2)
    private int age;
}
// build
class Person {
    private String other;
    private String school;
    private String name;
    private int age;

    public String toString() {
        return "Person(age=" + this.age + ", school=" + this.school + ", name=" + this.name + 
        ", Important Field!!!!!=" + this.other + ")";
    }
}
```

### 4) @EqualsAndHashCode
@EqualsAndHashCode를 사용하면 lombok이 equals(Object other)와 hashCode()를 만들어준다. 기본적으로 모든 non-static, non-transient 필드를 사용하지만 @EqualsAndHashCode.Include와 @EqualsAndHashCode.Exclude를 사용해서 명시적으로 선택할 수도 있다.

@ToString처럼 @EqualsAndHashCode(onlyExplicitlyIncluded = true)를 사용하는 것도 가능하다.

### 5) @NoArgsConstructor
매개변수가 없는 생성자를 생성한다. 만약 불가능 하다면(final 필드 떄문에) 컴파일 에러가 난다. 만약 @NoArgsConstructor(force = true)를 사용하면 컴파일 에러를 발생시키는 대신 모든 final 필드는 기본값(0, false, null)로 초기화된다.

### 6) @RequiredArgsConstructor
초기화되지 않은 모든 final 필드, @NonUll 필드에 대한 생성자를 생성해준다. @NonNull 필드의 경우 null check 구문 또한 생성해준다. 생성자 파라미터의 순서는 필드가 작성된 순서와 같다.

### 7) @AllArgsConstructor
모든 필드에 대한 생성자를 만들어준다. 마찬가지로 @NonNull 필드에 대한 null check 구문을 생성해준다.

### 8) @Data
모든 필드에 대해 @ToString, @EqualsAndHashCode, @Getter를, 모든 non-final 필드에 대해 @Setter를 설정하고 @RequiredArgsConstructor를 설정해주는 단축 Annotation이다.

### 9) @Value
@Data의 불변 클래스 버전이다. 모든 필드를 private / final로 만들고 setter는 생성되지 않는다. 클래스 또한 final로 만든다.

@Data처럼 toString(), equals(), hashCode()를 자동으로 생성해주고 각 필드에 대한 getter와 생성자 또한 만들어 준다.

### 10) @Builder
빌더를 자동으로 작성해준다. 클래스에 작성하면 모든 필드에 대한 빌더를 만들어준다. 원하는 필드에 대해서만 빌더를 작성하고 싶은 경우 생성자를 작성하고 그 위에 @Builder를 붙여주면 된다.

```java
// code
@Builder
class Person{
    private String name;
    private int age;
}
// build
class Person {
    private String name;
    private int age;

    Person(final String name, final int age) {
        this.name = name;
        this.age = age;
    }

    public static Person.PersonBuilder builder() {
        return new Person.PersonBuilder();
    }

    public static class PersonBuilder {
        private String name;
        private int age;

        PersonBuilder() {
        }

        public Person.PersonBuilder name(final String name) {
            this.name = name;
            return this;
        }

        public Person.PersonBuilder age(final int age) {
            this.age = age;
            return this;
        }

        public Person build() {
            return new Person(this.name, this.age);
        }

        public String toString() {
            return "Person.PersonBuilder(name=" + this.name + ", age=" + this.age + ")";
        }
    }
}

// 아래처럼 사용
Person person = Person.builder()
    .name("name")
    .age(1)
    .build();
```

### 11) @CleanUp
안전하게 close()를 호출해준다.

```java
// code
class Person {
    public static void main(String[] args) throws IOException {
        File file;
        @Cleanup InputStream in = new FileInputStream(args[0]);
        byte[] b = new byte[10000];
        while (in.read(b) != -1) {
            System.out.println("Read~");
        }
    }
}
// build
class Person {
    public static void main(String[] args) throws IOException {
        FileInputStream in = new FileInputStream(args[0]);
        try {
            byte[] b = new byte[10000];

            while(in.read(b) != -1) {
                System.out.println("Read~");
            }
        } finally {
            if (Collections.singletonList(in).get(0) != null) {
                in.close();
            }
        }
    }
}
```

## [출처 및 참고]
* <https://korecm.github.io/Lombok%EC%9D%B4%EB%9E%80>
* <https://projectlombok.org/features/all>
