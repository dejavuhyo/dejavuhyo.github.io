---
title: Spring MVC 사용자 정의 오류 페이지
author: dejavuhyo
date: 2023-08-31 09:20:00 +0900
categories: [Framework, Spring]
tags: [spring-data-binder, data-binder, 스프링-데이터-바인더, 데이터-바인더]
---

## 1. 개별 객체를 요청 매개변수에 바인딩
간단한 유형을 바인딩한다. `Converter<S, T>` 인터페이스의 사용자 정의 구현을 제공해야 한다. 여기서 S는 변환할 원본 유형이고 T는 변환할 유형이다.

```java
@Component
public class StringToLocalDateTimeConverter
  implements Converter<String, LocalDateTime> {

    @Override
    public LocalDateTime convert(String source) {
        return LocalDateTime.parse(
          source, DateTimeFormatter.ISO_LOCAL_DATE_TIME);
    }
}
```

컨트롤러에서 다음 구문을 사용할 수 있다.

```java
@GetMapping("/findbydate/{date}")
public GenericEntity findByDate(@PathVariable("date") LocalDateTime date) {
    return ...;
}
```

### 1) 열거형을 요청 매개변수로 사용
enum을 RequestParameter로 사용하는 방법이다.

간단한 열거형 모드가 있다.

```java
public enum Modes {
    ALPHA, BETA;
}
```

다음과 같이 문자열 열거형 변환기를 구축한다.

```java
public class StringToEnumConverter implements Converter<String, Modes> {

    @Override
    public Modes convert(String from) {
        return Modes.valueOf(from);
    }
}
```

Converter를 등록해야 한다.

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addFormatters(FormatterRegistry registry) {
        registry.addConverter(new StringToEnumConverter());
    }
}
```

Enum을 RequestParameter로 사용할 수 있다.

```java
@GetMapping
public ResponseEntity<Object> getStringToMode(@RequestParam("mode") Modes mode) {
    // ...
}
```

또는 PathVariable로 사용할 수 있다.

```java
@GetMapping("/entity/findbymode/{mode}")
public GenericEntity findByEnum(@PathVariable("mode") Modes mode) {
    // ...
}
```
## 2. 객체 계층 바인딩
때로는 개체 계층의 전체 트리를 변환해야 하며 개별 변환기 집합보다는 중앙 집중식 바인딩을 사용하는 것이 합리적이다.

예에서는 기본 클래스인 AbstractEntity를 사용한다.

```java
public abstract class AbstractEntity {
    long id;
    public AbstractEntity(long id){
        this.id = id;
    }
}
```

하위 클래스 Foo 및 Bar는 다음과 같다.

```java
public class Foo extends AbstractEntity {
    private String name;
    
    // standard constructors, getters, setters
}
```

```java
public class Bar extends AbstractEntity {
    private int value;
    
    // standard constructors, getters, setters
}
```

`ConverterFactory<S, R>`을 구현할 수 있다. 여기서 S는 변환할 원본 유형이고 R은 변환할 수 있는 클래스 범위를 정의하는 기본 유형이다.

```java
public class StringToAbstractEntityConverterFactory 
  implements ConverterFactory<String, AbstractEntity>{

    @Override
    public <T extends AbstractEntity> Converter<String, T> getConverter(Class<T> targetClass) {
        return new StringToAbstractEntityConverter<>(targetClass);
    }

    private static class StringToAbstractEntityConverter<T extends AbstractEntity>
      implements Converter<String, T> {

        private Class<T> targetClass;

        public StringToAbstractEntityConverter(Class<T> targetClass) {
            this.targetClass = targetClass;
        }

        @Override
        public T convert(String source) {
            long id = Long.parseLong(source);
            if(this.targetClass == Foo.class) {
                return (T) new Foo(id);
            }
            else if(this.targetClass == Bar.class) {
                return (T) new Bar(id);
            } else {
                return null;
            }
        }
    }
}
```

구현해야 하는 유일한 메서드는 필요한 유형에 대한 변환기를 반환하는 `getConverter()`이다. 그러면 변환 프로세스가 이 변환기에 위임된다.

그런 다음 ConverterFactory를 등록해야 한다.

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addFormatters(FormatterRegistry registry) {
        registry.addConverterFactory(new StringToAbstractEntityConverterFactory());
    }
}
```

컨트롤러에서 원하는 대로 사용할 수 있다.

```java
@RestController
@RequestMapping("/string-to-abstract")
public class AbstractEntityController {

    @GetMapping("/foo/{foo}")
    public ResponseEntity<Object> getStringToFoo(@PathVariable Foo foo) {
        return ResponseEntity.ok(foo);
    }
    
    @GetMapping("/bar/{bar}")
    public ResponseEntity<Object> getStringToBar(@PathVariable Bar bar) {
        return ResponseEntity.ok(bar);
    }
}
```

## 3. 도메인 개체 바인딩
데이터를 개체에 바인딩하려는 경우가 있지만 이는 간접적인 방식(예: Session, Header 또는 Cookie 변수)으로 제공되거나 데이터 소스에 저장되는 경우도 있다. 이러한 경우에는 다른 솔루션을 사용해야 한다.

### 1) 사용자 정의 인수 분석기
매개변수에 대한 주석을 정의한다.

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.PARAMETER)
public @interface Version {
}
```

사용자 정의 HandlerMethodArgumentResolver를 구현한다.

```java
public class HeaderVersionArgumentResolver
  implements HandlerMethodArgumentResolver {

    @Override
    public boolean supportsParameter(MethodParameter methodParameter) {
        return methodParameter.getParameterAnnotation(Version.class) != null;
    }

    @Override
    public Object resolveArgument(
      MethodParameter methodParameter, 
      ModelAndViewContainer modelAndViewContainer, 
      NativeWebRequest nativeWebRequest, 
      WebDataBinderFactory webDataBinderFactory) throws Exception {
 
        HttpServletRequest request 
          = (HttpServletRequest) nativeWebRequest.getNativeRequest();

        return request.getHeader("Version");
    }
}
```

Spring에게 검색 위치를 알려준다.

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

    //...

    @Override
    public void addArgumentResolvers(
      List<HandlerMethodArgumentResolver> argumentResolvers) {
        argumentResolvers.add(new HeaderVersionArgumentResolver());
    }
}
```

이제 컨트롤러에서 사용할 수 있다.

```java
@GetMapping("/entity/{id}")
public ResponseEntity findByVersion(
  @PathVariable Long id, @Version String version) {
    return ...;
}
```

HandlerMethodArgumentResolver의 `resolveArgument()` 메소드는 객체를 반환한다. 즉, String 뿐만 아니라 모든 객체를 반환할 수 있다.

## [출처 및 참고]
* [https://www.baeldung.com/spring-mvc-custom-data-binder](https://www.baeldung.com/spring-mvc-custom-data-binder)
