---
title: Spring Boot 3 및 Spring Framework 6.0
author: dejavuhyo
date: 2024-04-01 10:07:00 +0900
categories: [Framework, Spring]
tags: [spring-boot-3, spring-framework-6, spring-boot, spring-framework]
---

## 1. Java 17
이전에는 이미 Java 17에 대한 지원이 있었지만 이제 이 LTS 버전이 기본 지원을 받는다.

LTS 버전 11에서 마이그레이션할 때 Java 개발자는 새로운 언어 기능의 이점을 누릴 수 있다. Java [17](https://www.baeldung.com/java-17-new-features), [16](https://www.baeldung.com/java-16-new-features), [15](https://www.baeldung.com/java-15-new), [14](https://www.baeldung.com/java-14-new-features), [13](https://www.baeldung.com/java-13-new-features) 및 [12](https://www.baeldung.com/java-12-new-features)에 대한 별도의 문서에서 추가 세부정보를 찾을 수 있다.

### 1) Records
Java 레코드([JEP 395](https://openjdk.java.net/jeps/395), [Java 14 Record 키워드](https://www.baeldung.com/java-record-keyword) 참조)는 데이터 캐리어 클래스, 즉 단순히 데이터를 포함하고 모듈 간에 데이터를 운반하는 것을 목표로 하는 클래스 POJO(Plain Old Java Objects) 및 DTO(Data Transfer Objects)라고도 한다.

불변의 DTO를 쉽게 만들 수 있습니다.

```java
public record Person (String name, String address) {}
```

현재 인스턴스가 JSON 역직렬화(Jackson)에서 생성되고 컨트롤러의 메서드에 매개변수로 입력되는 경우와 같이 생성자 인수에 대한 유효성 검사 제약 조건이 지원되지 않기 때문에 이를 Bean 유효성 검사와 결합할 때 주의해야 한다.

### 2) Text Blocks
[JEP 378](https://openjdk.org/jeps/378)을 사용하면 이제 줄바꿈에 문자열을 연결할 필요없이 여러 줄의 텍스트 블록을 생성할 수 있다.

```java
String textBlock = """
Hello, this is a
multi-line
text block.
""";
```

### 3) Switch 표현식
Java 12에는 모든 표현식과 마찬가지로 단일 값을 평가하고 문에서 사용할 수 있는 스위치 표현식([JEP 361](https://openjdk.org/jeps/361))이 도입되었다. 중첩된 if–else-operators(`?:`)를 결합하는 대신 이제 switch–case-construct를 사용할 수 있다.

```java
DayOfWeek day = DayOfWeek.FRIDAY;
int numOfLetters = switch (day) {
    case MONDAY, FRIDAY, SUNDAY -> 6;
    case TUESDAY                -> 7;
    case THURSDAY, SATURDAY     -> 8;
    case WEDNESDAY              -> 9;
};
```

### 4) Pattern Matching
패턴 일치는 [Project Amber](https://openjdk.org/projects/amber/)에서 정교화되었으며 Java 언어로 향하는 길을 찾았다. Java 언어에서는 평가 인스턴스를 위한 코드를 단순화하는데 도움이 될 수 있다.

instanceof와 함께 직접 사용할 수 있다.

```java
if (obj instanceof String s) {
    System.out.println(s.toLowerCase());
}
```

switch–case문 내에서도 사용할 수 있다.

```java
static double getDoubleUsingSwitch(Object o) {
    return switch (o) {
        case Integer i -> i.doubleValue();
        case Float f -> f.doubleValue();
        case String s -> Double.parseDouble(s);
        default -> 0d;
    };
}
```

### 5) Sealed 클래스 및 인터페이스
Sealed 클래스는 허용된 하위 클래스를 지정하여 상속을 제한할 수 있다.

```java
public abstract sealed class Pet permits Dog, Cat {}
```

자세한 내용은 Java의 [Sealed Classes and Interfaces](https://www.baeldung.com/java-sealed-classes-interfaces)에서 확인할 수 있다.

## 2. Jakarta EE 9
가장 중요한 변경 사항은 Java EE에서 Jakarta EE9로의 점프일 수 있다. 여기서 패키지 네임스페이스는 javax.* 에서 jakarta.* 로 변경되었다. 결과적으로 Java EE의 클래스를 직접 사용할 때마다 코드의 모든 가져오기를 조정해야 한다.

예를 들어 Spring MVC 컨트롤러 내에서 HttpServletRequest 객체에 액세스할 때 다음을 교체해야 한다.

```java
import javax.servlet.http.HttpServletRequest;
```

```java
import jakarta.servlet.http.HttpServletRequest;
```

물론 Servlet API의 유형을 자주 사용할 필요는 없지만 Bean 유효성 검사와 JPA를 사용하면 피할 수 없다.

Java/Jakarta EE에 의존하는 외부 라이브러리를 사용할 때도 이 점을 알아야 한다(예: Hibernate Validator 7+, Tomcat 10+ 및 Jetty 11+에서 사용해야 함).

## 3. 추가 종속성
Spring Framework 6 및 Spring Boot 3에는 다음과 같은 최소 버전이 필요하다.

* Kotlin 1.7+

* Lombok 1.18.22+ (JDK17 support)

* Gradle 7.3+

## 4. Big Points
두 가지 중요한 주제인 Native Executables와 Observability이 특히 주목을 받았다. 가장 중요한 의미는 다음과 같다.

* Spring Framework는 핵심 추상화를 도입한다.

* 포트폴리오 프로젝트는 지속적으로 그들과 통합된다.

* Spring Boot는 자동 구성을 제공한다.

### 1) Native Executables
기본 실행 파일을 빌드하고 GraalVM에 배포하는 것이 더 높은 우선순위를 갖는다. 따라서 [Spring Native](https://www.baeldung.com/spring-native-intro) 이니셔티브는 [Spring](https://spring.io/blog/2022/03/22/initial-aot-support-in-spring-framework-6-0-0-m3)으로 이동하고 있다.

AOT 생성의 경우 별도의 플러그인을 포함할 필요가 없으며 spring-boot-maven-plugin의 [새로운 목표](https://docs.spring.io/spring-boot/docs/3.0.0-M3/maven-plugin/reference/htmlsingle/#aot)를 사용할 수 있다.

```text
mvn spring-boot:aot-generate
```

[Native Hints](https://docs.spring.io/spring-boot/docs/current/reference/html/native-image.html#native-hints)도 Spring 코어의 일부가 될 것입니다. 이에 대한 테스트 인프라는 [Milestone 5 (v6.0.0-M5)](https://github.com/spring-projects/spring-framework/issues/27981)에서 사용할 수 있다.

### 2) Observability
Spring 6에는 Micrometer 및 Micrometer Tracing(이전의 Spring Cloud Sleuth)을 기반으로 하는 새로운 이니셔티브인 Spring Observability가 도입되었다. 목표는 Micrometer를 사용하여 애플리케이션 메트릭을 효율적으로 기록하고 [OpenZipkin](https://zipkin.io/) 또는 [OpenTelemetry](https://opentelemetry.io/)와 같은 공급자를 통해 추적을 구현하는 것이다.

Spring Boot 3에는 이들 모두에 대한 자동 구성이 있으며 Spring 프로젝트는 새로운 Observation API를 사용하여 자체적으로 계측 작업을 진행하고 있다.

이에 대한 자세한 내용은 [여기에서](https://www.baeldung.com/spring-boot-3-observability) 확인할 수 있다.

## 5. Spring Web MVC의 작은 변화
가장 중요한 새로운 기능 중 하나는 [RFC7807](https://github.com/spring-projects/spring-framework/issues/27052) (Problem Details Standard)에 대한 지원이다. 이제 [Zalando Problem](https://github.com/zalando/problem)과 같은 별도의 라이브러리를 포함할 필요가 없다.

또 다른 작은 변화는 [HttpMethod]가(https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/http/HttpMethod.html) 더 이상 열거형이 아니라 확장된 HTTP 메서드(예: WebDAV에서 정의한 메서드)에 대한 인스턴스를 생성할 수 있는 클래스라는 것이다.

```java
HttpMethod lock = HttpMethod.valueOf("LOCK");
```

Commons FileUpload(멀티파트 파일 업로드를 위해 [StandardServletMultipartResolver](https://www.logicbig.com/tutorials/spring-framework/spring-web-mvc/file-upload-servlet-resolver.html)를  사용해야 함), Tiles 및 FreeMarker JSP 지원(대신 [FreeMarker template views](https://www.baeldung.com/freemarker-in-spring-mvc-tutorial)를 사용해야 함)과 같은 일부 오래된 서블릿 기반 통합이 삭제되었다.


## 6. 프로젝트 마이그레이션
프로젝트 마이그레이션에 대한 몇 가지 힌트가 있다. 권장되는 단계는 다음과 같다.

* [Spring Boot 2.7](https://spring.io/blog/2022/05/19/spring-boot-2-7-0-available-now)로 마이그레이션 (Spring Boot 3이 출시되면 Spring Boot 2.7 기반 마이그레이션 가이드가 제공될 예정)

* 더 이상 사용되지 않는 코드 사용 및 [레거시 구성 파일 처리](https://spring.io/blog/2020/08/14/config-file-processing-in-spring-boot-2-4)를 확인한다. 새로운 주요 릴리스에서는 제거될 예정이다.

* 자바 17로 마이그레이션

* Jakarta EE 9 호환 릴리스가 있는지 타사 프로젝트를 확인한다.

* Spring Boot 3은 아직 출시되지 않았으므로 [현재 마일스톤](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-3.0.0-M4-Release-Notes)을 사용해 마이그레이션을 테스트 할 수 있다.

## [출처 및 참고]
* [https://www.baeldung.com/spring-boot-3-spring-6-new](https://www.baeldung.com/spring-boot-3-spring-6-new)
