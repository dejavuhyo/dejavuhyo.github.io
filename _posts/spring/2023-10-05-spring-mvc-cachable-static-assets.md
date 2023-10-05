---
title: Spring MVC 캐시 가능한 정적 자산
author: dejavuhyo
date: 2023-10-05 22:05:00 +0900
categories: [Framework, Spring]
tags: [spring-cache, cachable-static-assets, cache]
---

## 1. 정적 자산 캐싱
정적 자산을 캐시 가능하게 만들려면 해당 리소스 핸들러를 구성해야 한다.

다음은 이를 수행하는 방법에 대한 간단한 예이다. 응답의 `Cache-Control` 헤더를 `max-age=31536000`으로 설정 하면 브라우저가 1년 동안 캐시된 파일 버전을 사용하게 된다.

```java
@EnableWebMvc
public class MvcConfig implements WebMvcConfigurer {
    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/js/**") 
                .addResourceLocations("/js/") 
                .setCacheControl(CacheControl.maxAge(365, TimeUnit.DAYS));
    }
}
```

캐시 유효성에 대한 기간이 이렇게 긴 이유는 파일이 업데이트될 때까지 클라이언트가 파일의 캐시된 버전을 사용하기를 원하기 때문이며, Cache-Control 헤더에 대한 RFC에 따라 사용할 수 있는 최대 기간은 365일 이다.

따라서 클라이언트가 처음으로 `foo.js`를 요청하면 네트워크를 통해 상태 코드 200 OK와 함께 전체 파일(이 경우 37바이트)을 받게 된다. 응답에는 캐싱 동작을 제어하기 위한 다음 헤더가 있다.

```text
Cache-Control: max-age=31536000
```

이는 다음 응답의 결과로 브라우저에 만료 기간이 1년인 파일을 캐시하도록 지시한다.

![cache](/assets/img/2023-10-05-spring-mvc-cachable-static-assets/cache.png)

클라이언트가 동일한 파일을 두 번째로 요청하면 브라우저는 서버에 다른 요청을 하지 않는다. 대신 캐시에서 파일을 직접 제공하고 네트워크 왕복을 방지하여 페이지가 훨씬 빠르게 로드된다.

![cache-highlighted](/assets/img/2023-10-05-spring-mvc-cachable-static-assets/cache-highlighted.png)

Chrome 브라우저 사용자는 화면의 새로 고침 버튼을 누르거나 F5 키를 눌러 페이지를 새로 고치는 경우 Chrome에서 캐시를 사용하지 않으므로 테스트하는 동안 주의해야 한다. 캐싱 동작을 관찰하려면 주소 표시줄에서 Enter 키를 눌러야 한다.

### 1) 스프링 부트
Spring Boot에서 Cache-Control 헤더를 사용자 정의하기 위해 `spring.resources.cache.cachecontrol` 속성 네임스페이스 아래의 속성을 사용할 수 있다. 예를 들어, max-age를 1년으로 변경하려면 `application.properties`에 다음을 추가할 수 있다.

```property
spring.resources.cache.cachecontrol.max-age=365d
```

이는 Spring Boot가 제공하는 모든 정적 리소스에 적용된다. 따라서 요청의 하위 집합에 캐싱 전략을 적용하려면 일반 Spring MVC 접근 방식을 사용해야 한다.

max-age 외에도 유사한 구성 속성을 사용하여 no-store 또는 no-cache와 같은 다른 Cache-Control 매개 변수를 사용자 정의할 수도 있다.

## 2. 정적 자산 버전 관리
정적 자산을 제공하기 위해 캐시를 사용하면 페이지 로드 속도가 매우 빨라지지만 중요한 주의 사항이 있다. 파일을 업데이트하면 클라이언트는 파일이 최신인지 서버에 확인하지 않고 브라우저 캐시에서 파일을 제공하기 때문에 최신 버전의 파일을 가져오지 못한다.

파일이 업데이트될 때만 브라우저가 서버에서 파일을 가져오도록 하려면 다음을 수행해야 한다.

* 버전이 포함된 URL 아래에 파일을 제공한다. 예를 들어 `foo.js`는 `/js/foo-46944c7e3a9bd20cc30fdc085cae46f2.js` 아래에 제공되어야 한다.

* 새 URL로 파일 링크 업데이트를 한다.

* 파일이 업데이트될 때마다 URL의 버전 부분을 업데이트한다. 예를 들어, `foo.js`가 업데이트되면 이제 `/js/foo-a3d8d7780349a12d739799e9aa7d2623.js` 아래에 제공되어야 한다.

페이지에 다른 URL에 대한 링크가 있으므로 클라이언트는 업데이트될 때 서버에서 파일을 요청하므로 브라우저는 캐시를 사용하지 않는다. 파일이 업데이트되지 않으면 해당 버전(따라서 URL)은 변경되지 않으며 클라이언트는 해당 파일에 대한 캐시를 계속 사용한다.

일반적으로 우리는 이 모든 작업을 수동으로 수행해야 하지만 Spring은 각 파일에 대한 해시를 계산하고 이를 URL에 추가하는 것을 포함하여 기본적으로 이러한 작업을 지원한다. 이 모든 작업을 수행하도록 Spring 애플리케이션을 구성하는 방법을 확인한다.

### 1) 버전이 있는 URL로 제공
해당 URL에 업데이트된 버전 문자열이 포함된 파일을 제공하려면 경로에 VersionResourceResolver를 추가해야 한다.

```java
@Override
public void addResourceHandlers(ResourceHandlerRegistry registry) {
    registry.addResourceHandler("/js/**")
            .addResourceLocations("/js/")
            .setCacheControl(CacheControl.maxAge(365, TimeUnit.DAYS))
            .resourceChain(false)
            .addResolver(new VersionResourceResolver().addContentVersionStrategy("/**"));
}
```

여기서는 콘텐츠 버전 전략을 사용한다. `/js` 폴더의 각 파일은 해당 콘텐츠에서 계산된 버전이 있는 URL 아래에 제공된다. 이것을 지문 채취라고 한다. 예를 들어 `foo.js`는 이제 URL `/js/foo-46944c7e3a9bd20cc30fdc085cae46f2.js` 아래에 제공된다.

이 구성을 사용하면 클라이언트가 `http://localhost:8080/js/46944c7e3a9bd20cc30fdc085cae46f2.js`에 대한 요청을 할 때,

```shell
curl -i http://localhost:8080/js/foo-46944c7e3a9bd20cc30fdc085cae46f2.js
```

서버는 Cache-Control 헤더로 응답하여 클라이언트 브라우저에 1년 동안 파일을 캐시하도록 지시한다.

```text
HTTP/1.1 200 OK
Server: Apache-Coyote/1.1
Last-Modified: Tue, 09 Aug 2016 06:43:26 GMT
Cache-Control: max-age=31536000
```

### 2) 스프링 부트
Spring Boot에서 동일한 콘텐츠 기반 버전 관리를 활성화하려면 `spring.resources.chain.strategy.content` 속성 네임스페이스 에서 몇 가지 구성을 사용하면 된다. 예를 들어 다음 구성을 추가하면 이전과 동일한 결과를 얻을 수 있다.

```property
spring.resources.chain.strategy.content.enabled=true
spring.resources.chain.strategy.content.paths=/**
```

Java 구성과 유사하게 이를 통해 `/**` 경로 패턴과 일치하는 모든 자산에 대한 콘텐츠 기반 버전 관리가 가능해진다.

### 3) 새 URL로 링크 업데이트
URL에 버전을 삽입하기 전에 간단한 스크립트 태그를 사용하여 `foo.js`를 가져올 수 있다.

```javascript
<script type="text/javascript" src="/js/foo.js">
```

이제 동일한 파일을 버전이 포함된 URL로 제공하므로 이를 페이지에 반영해야 한다.

```javascript
<script type="text/javascript" src="<em>/js/foo-46944c7e3a9bd20cc30fdc085cae46f2.js</em>">
```

그 긴 경로를 모두 처리하는 것은 지루해진다. 이 문제에 대해 Spring이 제공하는 더 나은 솔루션이 있다. 버전이 지정된 링크의 URL을 다시 작성하기 위해 ResourceUrlEncodingFilter 및 JSTL의 url 태그를 사용할 수 있다.

ResourceURLEncodingFilter는 평소와 같이 `web.xml`에 등록할 수 있다.

```xml
<filter>
    <filter-name>resourceUrlEncodingFilter</filter-name>
    <filter-class>
        org.springframework.web.servlet.resource.ResourceUrlEncodingFilter
    </filter-class>
</filter>
<filter-mapping>
    <filter-name>resourceUrlEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

url 태그 를 사용하려면 먼저 JSP 페이지에서 JSTL 코어 태그 라이브러리를 가져와야 한다.

```jsp
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
```

그런 다음 url 태그를 사용하여 다음과 같이 `foo.js`를 가져올 수 있다.

```jsp
<script type="text/javascript" src="<c:url value="/js/foo.js" />">
```

이 JSP 페이지가 렌더링되면 파일의 URL이 해당 버전을 포함하도록 올바르게 다시 작성된다.

```jsp
<script type="text/javascript" src="/js/foo-46944c7e3a9bd20cc30fdc085cae46f2.js">
```

### 4) 업데이트 버전 URL의 일부
파일이 업데이트될 때마다 해당 버전이 다시 계산되고 파일은 새 버전이 포함된 URL 아래에 제공된다. 이를 위해 추가 작업을 수행할 필요가 없으며 VersionResourceResolver가 이를 처리한다.

## 3. CSS 링크 수정
CSS 파일은 `@import` 지시문을 사용하여 다른 CSS 파일을 가져올 수 있다. 예를 들어 `myCss.css` 파일은 `another.css` 파일을 가져온다.

```css
@import "another.css";
```

브라우저가 `another.css` 파일을 요청하지만 해당 파일은 `another-9556ab93ae179f87b178cfad96a6ab72.css`와 같은 버전이 지정된 경로에서 제공되기 때문에 일반적으로 버전이 지정된 정적 자산에 문제가 발생한다.

이 문제를 해결하고 올바른 경로를 요청하려면 리소스 핸들러 구성에 CssLinkResourceTransformer를 도입해야 한다.

```java
@Override
public void addResourceHandlers(ResourceHandlerRegistry registry) {
    registry.addResourceHandler("/resources/**")
            .addResourceLocations("/resources/", "classpath:/other-resources/")
            .setCacheControl(CacheControl.maxAge(365, TimeUnit.DAYS))
            .resourceChain(false)
            .addResolver(new VersionResourceResolver().addContentVersionStrategy("/**"))
            .addTransformer(new CssLinkResourceTransformer());
}
```

이는 `myCss.css`의 내용을 수정 하고 import 문을 다음으로 바꾼다.

```css
@import "another-9556ab93ae179f87b178cfad96a6ab72.css";
```

## [출처 및 참고]
* [https://www.baeldung.com/cachable-static-assets-with-spring-mvc#4-update-version-part-of-the-url](https://www.baeldung.com/cachable-static-assets-with-spring-mvc#4-update-version-part-of-the-url)
