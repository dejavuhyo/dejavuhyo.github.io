---
title: Spring Boot Actuator
author: dejavuhyo
date: 2024-03-28 09:41:00 +0900
categories: [Framework, Spring]
tags: [spring-actuator, spring-boot-actuator, 스프링-액츄에이터, 스프링-부트-액츄에이터]
---

## 1. Actuator란
본질적으로 Actuator는 애플리케이션에 생산 준비 기능을 제공한다.

이러한 종속성을 사용하면 앱을 모니터링하고, 지표를 수집하고, 트래픽이나 데이터베이스 상태를 이해하는 것이 간단해진다.

이 라이브러리의 주요 이점은 실제로 이러한 기능을 직접 구현하지 않고도 프로덕션급 도구를 얻을 수 있다는 것이다.

액추에이터는 주로 실행 중인 애플리케이션에 대한 운영 정보 (상태, 메트릭, 정보, 덤프, 환경 등)를 노출한다. 이는 HTTP 엔드포인트 또는 JMX 빈을 사용하여 상호 작용할 수 있도록 한다.

이 종속성이 클래스 경로에 있으면 즉시 여러 엔드포인트를 사용할 수 있다. 대부분의 Spring 모듈과 마찬가지로 다양한 방법으로 쉽게 구성하거나 확장할 수 있다.

### 1) 시작하기
Spring Boot Actuator를 활성화하려면 패키지 관리자에 [spring-boot-actuator](https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-actuator) 종속성을 추가한다.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

버전은 Spring Boot BOM(Bill of Materials)에 지정되어 있으므로 Boot 버전에 관계없이 유효하다.

## 2. Spring Boot 2.x Actuator
2.x에서 액추에이터는 기본 의도를 유지하지만 모델을 단순화하고 기능을 확장하며 더 나은 기본값을 통합한다.

첫째, 이 버전은 기술에 구애받지 않는다. 또한 보안 모델을 애플리케이션 모델과 병합하여 단순화한다.

다양한 변경 사항 중 일부 변경 사항이 중단된다는 점을 명심하는 것이 중요하다. 여기에는 HTTP 요청 및 응답은 물론 Java API도 포함된다.

마지막으로 최신 버전은 이제 이전 읽기/쓰기 모델이 아닌 CRUD 모델을 지원한다.

### 1) 기술지원
두 번째 주요 버전에서 Actuator는 이제 기술에 구애받지 않지만 1.x에서는 MVC에 묶여 있으므로 Servlet API에 묶여 있다.

2.x에서 Actuator는 MVC에 의존하지 않고 해당 모델을 플러그형 및 확장형으로 정의한다.

따라서 이 새로운 모델을 통해 MVC와 WebFlux를 기본 웹 기술로 활용할 수 있다.

또한 올바른 어댑터를 구현하면 향후 기술을 추가할 수 있다.

마지막으로 JMX는 추가 코드 없이 엔드포인트를 노출하도록 계속 지원된다.

### 2) 중요한 변경 사항
이전 버전과 달리 Actuator는 대부분의 엔드포인트가 비활성화된 상태로 제공된다.

따라서 기본적으로 사용 가능한 두 가지는 `/health` 및 `/info` 이다.

모두 활성화하려면 `management.endpoints.web.exposure.include=*`를 설정할 수 있다. 또는 활성화해야 하는 엔드포인트를 나열할 수 있다.

또한 Actuator는 이제 일반 앱 보안 규칙과 보안 구성을 공유하여 보안 모델을 대폭 단순화한다.

따라서 프로젝트에서 Spring Security를 ​​사용하는 경우 Actuator 엔드포인트를 허용하도록 Actuator 보안 규칙을 조정해야 한다.

`/actuator/**`에 대한 항목을 추가하면 된다 .

```java
@Bean
public SecurityWebFilterChain securityWebFilterChain(
  ServerHttpSecurity http) {
    return http.authorizeExchange()
      .pathMatchers("/actuator/**").permitAll()
      .anyExchange().authenticated()
      .and().build();
}
```

새로운 [Actuator 공식 문서](https://docs.spring.io/spring-boot/docs/2.0.x/actuator-api/html/)에서 자세한 내용을 확인할 수 있다.

또한 모든 Actuator 엔드포인트는 이제 기본적으로 `/actuator` 경로 아래에 배치된다.

이전 버전과 마찬가지로 새로운 속성인 `Management.endpoints.web.base-path`를 사용하여 이 경로를 조정할 수 있다.

### 3) 사전 정의된 Endpoints
사용 가능한 엔드포인트 중 일부를 살펴본다. 대부분은 이미 1.x에서 사용 가능했다.

또한 일부 엔드포인트가 추가되고 일부는 제거되었으며 일부는 재구성되었다.

* `/auditevents`는 사용자 로그인/로그아웃과 같은 보안 감사 관련 이벤트를 나열한다. 또한 다른 필드 중에서 주체나 유형을 기준으로 필터링할 수도 있다.

* `/beans`는 BeanFactory에서 사용 가능한 모든 빈을 반환한다. `/auditevents`와 달리 필터링을 지원하지 않는다.

* `/conditions` (이전에는 `/autoconfig`로 알려짐)는 자동 구성과 관련된 조건 보고서를 작성한다.

* `/configprops`를 사용하면 모든 `@ConfigurationProperties` Bean을 가져올 수 있다.

* `/env`는 현재 환경 속성을 반환한다. 또한 단일 속성을 검색할 수도 있다.

* `/flyway`는 Flyway 데이터베이스 마이그레이션에 대한 세부 정보를 제공한다.

* `/health`는 애플리케이션의 상태를 요약한다.

* `/heapdump`는 애플리케이션에서 사용하는 JVM에서 힙 덤프를 빌드하고 반환한다.

* `/info`는 일반 정보를 반환한다. 사용자 정의 데이터, 빌드 정보 또는 최신 커밋에 대한 세부 정보일 수 있다.

* `/liquibase`는 `/flyway`와 유사하게 동작 하지만 Liquibase용이다.

* `/logfile`은 일반 애플리케이션 로그를 반환한다.

* `/loggers`를 사용하면 애플리케이션의 로깅 수준을 쿼리하고 수정할 수 있다.

* `/metrics`는 애플리케이션의 측정항목을 자세히 설명한다. 여기에는 일반 측정항목과 사용자 지정 측정항목이 포함될 수 있다.

* `/prometheus`는 이전 측정항목과 유사하지만 Prometheus 서버에서 작동하도록 형식이 지정된 측정항목을 반환한다.

* `/scheduledtasks`는 애플리케이션 내의 모든 예약된 작업에 대한 세부 정보를 제공한다.

* `/sessions`는 Spring 세션을 사용하는 경우 HTTP 세션을 나열한다.

* `/shutdown`은 응용 프로그램의 정상적인 종료를 수행한다.

* `/threaddump`는 기본 JVM의 스레드 정보를 덤프한다.

### 4) Actuator Endpoints를 위한 Hypermedia
Spring Boot는 사용 가능한 모든 Actuator 엔드포인트에 대한 링크를 반환하는 검색 엔드포인트를 추가한다. 이렇게 하면 액츄에이터 엔드포인트와 해당 URL을 쉽게 찾을 수 있다.

기본적으로 이 검색 엔드포인트는 `/actuator` 엔드포인트를 통해 액세스할 수 있다.

따라서 이 URL로 GET 요청을 보내면 다양한 엔드포인트에 대한 액추에이터 링크가 반환된다.

```json
{
  "_links": {
    "self": {
      "href": "http://localhost:8080/actuator",
      "templated": false
    },
    "features-arg0": {
      "href": "http://localhost:8080/actuator/features/{arg0}",
      "templated": true
    },
    "features": {
      "href": "http://localhost:8080/actuator/features",
      "templated": false
    },
    "beans": {
      "href": "http://localhost:8080/actuator/beans",
      "templated": false
    },
    "caches-cache": {
      "href": "http://localhost:8080/actuator/caches/{cache}",
      "templated": true
    },
    // truncated
}
```

위에 표시된 대로 `/actuator` 엔드포인트는 `_links` 필드 아래에 사용 가능한 모든 액추에이터 엔드포인트를 보고한다.

또한 사용자 정의 관리 기본 경로를 구성하는 경우 해당 기본 경로를 검색 URL로 사용해야 한다.

예를 들어 `management.endpoints.web.base-path`를 `/mgmt`로 설정한 경우 링크 목록을 보려면 `/mgmt` 엔드포인트를 요청해야 한다.

관리 기본 경로가 `/`로 설정되면 다른 매핑과의 충돌 가능성을 방지하기 위해 검색 엔드포인트가 비활성화된다.

### 5) Health Indicators
이전 버전과 마찬가지로 사용자 지정 표시기를 쉽게 추가할 수 있다. 다른 API와는 달리 사용자 지정 상태 엔드포인트를 생성하기 위한 추상화는 변경되지 않는다. 그러나 반응형 상태 확인을 구현하기 위해 새로운 인터페이스인 ReactiveHealthIndicator가 추가되었다.

간단한 맞춤형 반응형 상태 확인을 살펴본다.

```java
@Component
public class DownstreamServiceHealthIndicator implements ReactiveHealthIndicator {

    @Override
    public Mono<Health> health() {
        return checkDownstreamServiceHealth().onErrorResume(
          ex -> Mono.just(new Health.Builder().down(ex).build())
        );
    }

    private Mono<Health> checkDownstreamServiceHealth() {
        // we could use WebClient to check health reactively
        return Mono.just(new Health.Builder().up().build());
    }
}
```

상태 지표의 편리한 기능은 이를 계층 구조의 일부로 집계할 수 있다는 것이다.

따라서 이전 예에 따라 모든 다운스트림 서비스를 다운스트림 서비스 범주로 그룹화할 수 있다. 이 카테고리는 모든 중첩된 서비스에 접근할 수 있는 한 정상이다.

더 자세한 내용은 [Health Indicators](https://www.baeldung.com/spring-boot-health-indicators)를 확인한다.

### 6) Health Groups
Spring Boot 2.2부터는 상태 표시기를 그룹으로 구성하고 모든 그룹 구성원에 동일한 구성을 적용할 수 있다.

예를 들어, 이것을 `application.properties`에 추가하여 custom 이라는 상태 그룹을 생성할 수 있다.

```properties
management.endpoint.health.group.custom.status.http-mapping.up=207
```

이런 방식으로 사용자 정의 그룹에는 diskSpace 및 ping 상태 표시기가 포함된다.

이제 `/actuator/health` 엔드포인트를 호출하면 JSON 응답에서 새 상태 그룹에 대해 알려준다.

```json
{"status":"UP","groups":["custom"]}
```

건강 그룹을 사용하면 몇 가지 건강 지표의 집계 결과를 볼 수 있다.

이 경우 `/actuator/health/custom`에 요청을 보내면 다음이 수행된다.

```json
{"status":"UP"}
```

`application.properties`를 통해 더 많은 세부 정보를 표시하도록 그룹을 구성할 수 있다.

```properties
management.endpoint.health.group.custom.show-components=always
management.endpoint.health.group.custom.show-details=always
```

이제 `/actuator/health/custom`에 동일한 요청을 보내면 자세한 내용을 볼 수 있다.

```json
{
  "status": "UP",
  "components": {
    "diskSpace": {
      "status": "UP",
      "details": {
        "total": 499963170816,
        "free": 91300069376,
        "threshold": 10485760
      }
    },
    "ping": {
      "status": "UP"
    }
  }
}
```

승인된 사용자에게만 다음 세부정보를 표시할 수도 있다.

```properties
management.endpoint.health.group.custom.show-components=when_authorized
management.endpoint.health.group.custom.show-details=when_authorized
```

사용자 정의 상태 매핑을 가질 수도 있다.

예를 들어 HTTP 200 OK 응답 대신 207 상태 코드를 반환할 수 있다.

```properties
management.endpoint.health.group.custom.status.http-mapping.up=207
```

사용자 지정 그룹 상태가 `UP`인 경우 Spring Boot에 207 HTTP 상태 코드를 반환하도록 지시한다.

### 7) Spring Boot 2 메트릭
Spring Boot 2.0에서는 내부 메트릭이 Micrometer 지원으로 대체되었으므로 획기적인 변화를 기대할 수 있다. 애플리케이션이 GaugeService 또는 CounterService와 같은 메트릭 서비스를 사용하고 있었다면 더 이상 사용할 수 없다.

대신 [Micrometer](https://www.baeldung.com/micrometer)와 직접 상호 작용해야 한다. Spring Boot 2.0에서는 자동으로 구성된 MeterRegistry 유형의 Bean을 얻게 된다.

또한 Micrometer는 이제 Actuator 종속성의 일부이므로 Actuator 종속성이 클래스 경로에 있는 한 계속 진행하는 것이 좋다.

또한 `/metrics` 엔드포인트에서 완전히 새로운 응답을 받게 된다.

```json
{
  "names": [
    "jvm.gc.pause",
    "jvm.buffer.memory.used",
    "jvm.memory.used",
    "jvm.buffer.count",
    // ...
  ]
}
```

보시다시피 1.x에서 얻은 실제 측정항목은 없다.

특정 측정항목의 실제 값을 얻으려면 이제 원하는 측정항목(예: `/actuator/metrics/jvm.gc.pause`)으로 이동하여 자세한 응답을 얻을 수 있다.

```json
{
  "name": "jvm.gc.pause",
  "measurements": [
    {
      "statistic": "Count",
      "value": 3.0
    },
    {
      "statistic": "TotalTime",
      "value": 7.9E7
    },
    {
      "statistic": "Max",
      "value": 7.9E7
    }
  ],
  "availableTags": [
    {
      "tag": "cause",
      "values": [
        "Metadata GC Threshold",
        "Allocation Failure"
      ]
    },
    {
      "tag": "action",
      "values": [
        "end of minor GC",
        "end of major GC"
      ]
    }
  ]
}
```

이제 다양한 값과 일부 관련 메타데이터를 포함하여 측정항목이 훨씬 더 철저해졌다.

### 8) /info Endpoint 사용자 정의
`/info` 엔드포인트는 변경되지 않고 그대로 유지된다. 이전과 마찬가지로 각각의 Maven 또는 Gradle 종속성을 사용하여 git 세부정보를 추가할 수 있다.

```xml
<dependency>
    <groupId>pl.project13.maven</groupId>
    <artifactId>git-commit-id-plugin</artifactId>
</dependency>
```

마찬가지로 Maven 또는 Gradle 플러그인을 사용하여 이름, 그룹, 버전을 포함한 빌드 정보를 포함할 수도 있다.

```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <executions>
        <execution>
            <goals>
                <goal>build-info</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

### 9) 사용자 정의 Endpoint 생성
이전에 지적했듯이 사용자 지정 엔드포인트를 만들 수 있다. 그러나 Spring Boot 2는 새로운 기술에 구애받지 않는 패러다임을 지원하기 위해 이를 달성하는 방법을 재설계했다.

애플리케이션에서 기능 플래그를 쿼리, 활성화 및 비활성화하는 Actuator 엔드포인트를 만든다.

```java
@Component
@Endpoint(id = "features")
public class FeaturesEndpoint {

    private Map<String, Feature> features = new ConcurrentHashMap<>();

    @ReadOperation
    public Map<String, Feature> features() {
        return features;
    }

    @ReadOperation
    public Feature feature(@Selector String name) {
        return features.get(name);
    }

    @WriteOperation
    public void configureFeature(@Selector String name, Feature feature) {
        features.put(name, feature);
    }

    @DeleteOperation
    public void deleteFeature(@Selector String name) {
        features.remove(name);
    }

    public static class Feature {
        private Boolean enabled;

        // [...] getters and setters 
    }

}
```

엔드포인트를 얻으려면 빈이 필요하다. 이 예에서는 이를 위해 `@Component`를 사용하고 있다. 또한 이 빈을 `@Endpoint`로 장식해야 한다.

엔드포인트의 경로는 `@Endpoint`의 id 매개변수에 의해 결정된다. 요청을 `/actuator/features`로 라우팅한다.

준비가 되면 다음을 사용하여 작업 정의를 시작할 수 있다.

* `@ReadOperation`: HTTP GET에 매핑된다.

* `@WriteOperation`: HTTP POST에 매핑된다.

* `@DeleteOperation`: HTTP DELETE에 매핑된다.

애플리케이션의 이전 엔드포인트를 사용하여 애플리케이션을 실행하면 Spring Boot가 이를 등록한다.

이를 확인하는 빠른 방법은 로그를 확인하는 것이다.

```text
[...].WebFluxEndpointHandlerMapping: Mapped "{[/actuator/features/{name}],
  methods=[GET],
  produces=[application/vnd.spring-boot.actuator.v2+json || application/json]}"
[...].WebFluxEndpointHandlerMapping : Mapped "{[/actuator/features],
  methods=[GET],
  produces=[application/vnd.spring-boot.actuator.v2+json || application/json]}"
[...].WebFluxEndpointHandlerMapping : Mapped "{[/actuator/features/{name}],
  methods=[POST],
  consumes=[application/vnd.spring-boot.actuator.v2+json || application/json]}"
[...].WebFluxEndpointHandlerMapping : Mapped "{[/actuator/features/{name}],
  methods=[DELETE]}"[...]
```

이전 로그에서 WebFlux가 새 엔드포인트를 어떻게 노출하는지 확인할 수 있다. MVC로 전환하면 코드를 변경하지 않고도 해당 기술을 위임할 수 있다.

또한 이 새로운 접근 방식에 대해 염두에 두어야 할 몇 가지 중요한 고려 사항이 있다.

* MVC에는 종속성이 없다.

* 이전에 메서드(sensitive, enabled...)로 존재했던 모든 메타데이터는 더 이상 존재하지 않는다. 그러나 `@Endpoint(id = "features", enableByDefault = false)`를 사용하여 엔드포인트를 활성화하거나 비활성화할 수 있다.

* 1.x와 달리 더 이상 주어진 인터페이스를 확장할 필요가 없다.

* 이전 읽기/쓰기 모델과 달리 이제 `@DeleteOperation`을 사용하여 DELETE 작업을 정의할 수 있다.

### 10) 기존 Endpoints 확장
애플리케이션의 프로덕션 인스턴스가 SNAPSHOT 버전이 아닌지 확인하고 싶다고 가정한다.

이 정보를 반환하는 Actuator 엔드포인트의 HTTP 상태 코드(예: `/info`)를 변경하여 이를 수행하기로 결정했다. 앱이 SNAPSHOT인 경우 다른 HTTP 상태 코드를 받게 된다.

`@EndpointExtension` 주석이나 보다 구체적인 특수화인 `@EndpointWebExtension` 또는 `@EndpointJmxExtension`을 사용하여 사전 정의된 엔드포인트의 동작을 쉽게 확장할 수 있다.

```java
@Component
@EndpointWebExtension(endpoint = InfoEndpoint.class)
public class InfoWebEndpointExtension {

    private InfoEndpoint delegate;

    // standard constructor

    @ReadOperation
    public WebEndpointResponse<Map> info() {
        Map<String, Object> info = this.delegate.info();
        Integer status = getStatus(info);
        return new WebEndpointResponse<>(info, status);
    }

    private Integer getStatus(Map<String, Object> info) {
        // return 5xx if this is a snapshot
        return 200;
    }
}
```

### 11) 모든 Endpoints 활성화
HTTP를 사용하여 액츄에이터 엔드포인트에 액세스하려면 이를 활성화하고 노출해야 합니다.

기본적으로 `/shutdown`을 제외한 모든 엔드포인트가 활성화된다. 기본적으로 `/health` 및 `/info` 엔드포인트만 노출된다.

모든 엔드포인트를 노출하려면 다음 구성을 추가해야 한다.

```properties
management.endpoints.web.exposure.include=*
```

특정 엔드포인트(예: `/shutdown`)를 명시적으로 활성화하려면 다음을 사용한다.

```properties
management.endpoint.shutdown.enabled=true
```

하나(예: `/loggers`)를 제외하고 활성화된 모든 엔드포인트를 노출하려면 다음을 사용한다.

```properties
management.endpoints.web.exposure.include=*
management.endpoints.web.exposure.exclude=loggers
```

## 3. Spring Boot 1.x Actuator
1.x에서 액츄에이터는 읽기/쓰기 모델을 따른다. 즉, 액츄에이터에서 읽거나 쓸 수 있다는 의미이다.

예를 들어 측정항목이나 애플리케이션 상태를 검색할 수 있다. 또는 앱을 정상적으로 종료하거나 로깅 구성을 변경할 수 있다.

Actuator가 작동하려면 Spring MVC가 HTTP를 통해 엔드포인트를 노출해야 한다. 다른 기술은 지원되지 않는다.

### 1) Endpoints
1.x에서는 Actuator가 자체 보안 모델을 제공합니다. 이는 Spring Security 구성을 활용하지만 애플리케이션의 나머지 부분과 독립적으로 구성되어야 합니다.

또한 대부분의 엔드포인트는 민감하다. 즉, 완전히 공개되지 않거나 대부분의 정보가 생략된다. 반면 소수는 그렇지 않다(예: `/info`).

다음은 Boot가 기본적으로 제공하는 가장 일반적인 엔드포인트 중 일부이다.

* `/health`는 애플리케이션 상태 정보를 표시한다(인증되지 않은 연결을 통해 액세스할 경우 간단한 상태 또는 인증될 경우 전체 메시지 세부 정보). 기본적으로 민감하지 않다.

* `/info`는 임의의 애플리케이션 정보를 표시한다. 기본적으로 민감하지 않다.

* `/metrics`는 현재 애플리케이션에 대한 메트릭 정보를 표시한다. 기본적으로 민감하다.

* `/trace`는 추적 정보(기본적으로 마지막 몇 개의 HTTP 요청)를 표시한다.

[공식 문서](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)에서 기존 엔드포인트의 전체 목록을 찾을 수 있다.

### 2) 기존 Endpoints 구성
`endpoints.[endpoint name].[property to customize]` 형식을 사용하여 속성으로 각 엔드포인트를 사용자 정의할 수 있다.

다음 세 가지 속성을 사용할 수 있다.

* id: HTTP를 통해 이 엔드포인트에 액세스할 때 사용되는 ID이다.

* enabled: true이면 액세스할 수 있다. 그렇지 않으면 그렇지 않다.

* sensitive: true인 경우 HTTP를 통해 중요한 정보를 표시하려면 인증이 필요하다.

예를 들어 다음 속성을 추가하면 `/beans` 엔드포인트가 사용자 지정된다.

```properties
endpoints.beans.id=springbeans
endpoints.beans.sensitive=false
endpoints.beans.enabled=true
```

### 3) /health Endpoint
`/health` 엔드포인트는 실행 중인 애플리케이션의 상태를 확인하는데 사용된다.

일반적으로 실행 중인 인스턴스가 DB 연결 문제, 디스크 공간 부족 등 다른 이유로 인해 다운되거나 비정상이 되는 경우 경고하기 위해 소프트웨어를 모니터링하여 실행된다.

기본적으로 권한이 없는 사용자는 HTTP를 통해 액세스할 때만 상태 정보를 볼 수 있다.

```json
{
    "status" : "UP"
}
```

이 상태 정보는 애플리케이션 컨텍스트에 구성된 HealthIndicator 인터페이스를 구현하는 모든 Bean에서 수집된다.

HealthIndicator에서 반환된 일부 정보는 본질적으로 민감하지만 디스크 공간, 메시징 브로커 연결, 사용자 지정 검사 등과 같은 더 자세한 정보를 노출하도록 `endpoints.health.sensitive=false`를 구성할 수 있다.

이는 1.5.0 이하의 Spring Boot 버전에서만 작동한다. 1.5.0 이상 버전의 경우 무단 액세스에 대해 `management.security.enabled=false`를 설정하여 보안을 비활성화해야 한다.

또한 애플리케이션과 관련된 모든 유형의 사용자 정의 상태 데이터를 수집하고 `/health` 엔드포인트를 통해 자동으로 노출할 수 있는 자체 사용자 정의 상태 표시기를 구현할 수도 있다.

```java
@Component("myHealthCheck")
public class HealthCheck implements HealthIndicator {
 
    @Override
    public Health health() {
        int errorCode = check(); // perform some specific health check
        if (errorCode != 0) {
            return Health.down()
              .withDetail("Error Code", errorCode).build();
        }
        return Health.up().build();
    }
    
    public int check() {
    	// Our logic to check health
    	return 0;
    }
}
```

출력은 다음과 같다.

```json
{
    "status" : "DOWN",
    "myHealthCheck" : {
        "status" : "DOWN",
        "Error Code" : 1
     },
     "diskSpace" : {
         "status" : "UP",
         "free" : 209047318528,
         "threshold" : 10485760
     }
}
```

### 4) /info Endpoint
`/info` 엔드포인트에 표시된 데이터를 사용자 정의할 수도 있다.

```properties
info.app.name=Spring Sample Application
info.app.description=This is my first spring boot application
info.app.version=1.0.0
```

샘플 출력은 다음과 같다.

```json
{
    "app" : {
        "version" : "1.0.0",
        "description" : "This is my first spring boot application",
        "name" : "Spring Sample Application"
    }
}
```

### 5) /metrics Endpoint
메트릭 엔드포인트는 애플리케이션 수준 메트릭뿐만 아니라 OS 및 JVM에 대한 정보도 게시한다. 활성화되면 일부 HTTP 메트릭과 함께 메모리, 힙, 프로세서, 스레드, 로드된 클래스, 언로드된 클래스, 스레드 풀과 같은 정보를 얻을 수 있다.

이 엔드포인트의 출력은 기본적으로 다음과 같다.

```json
{
    "mem" : 193024,
    "mem.free" : 87693,
    "processors" : 4,
    "instance.uptime" : 305027,
    "uptime" : 307077,
    "systemload.average" : 0.11,
    "heap.committed" : 193024,
    "heap.init" : 124928,
    "heap.used" : 105330,
    "heap" : 1764352,
    "threads.peak" : 22,
    "threads.daemon" : 19,
    "threads" : 22,
    "classes" : 5819,
    "classes.loaded" : 5819,
    "classes.unloaded" : 0,
    "gc.ps_scavenge.count" : 7,
    "gc.ps_scavenge.time" : 54,
    "gc.ps_marksweep.count" : 1,
    "gc.ps_marksweep.time" : 44,
    "httpsessions.max" : -1,
    "httpsessions.active" : 0,
    "counter.status.200.root" : 1,
    "gauge.response.root" : 37.0
}
```

사용자 정의 지표를 수집하기 위해 게이지(데이터의 단일 값 스냅샷) 및 카운터(예: 지표 증가/감소)를 지원한다.

`/metrics` 엔드포인트에 자체 사용자 정의 메트릭을 구현해 본다.

성공 및 실패한 로그인 시도를 기록하도록 로그인 흐름을 사용자 정의한다.

```java
@Service
public class LoginServiceImpl {

    private final CounterService counterService;
    
    public LoginServiceImpl(CounterService counterService) {
        this.counterService = counterService;
    }
	
    public boolean login(String userName, char[] password) {
        boolean success;
        if (userName.equals("admin") && "secret".toCharArray().equals(password)) {
            counterService.increment("counter.login.success");
            success = true;
        }
        else {
            counterService.increment("counter.login.failure");
            success = false;
        }
        return success;
    }
}
```

출력은 다음과 같다.

```json
{
    ...
    "counter.login.success" : 105,
    "counter.login.failure" : 12,
    ...
}
```

로그인 시도 및 기타 보안 관련 이벤트는 Actuator에서 감사 이벤트로 바로 사용할 수 있다.

### 6) 새 Endpoint 생성
Spring Boot에서 제공하는 기존 엔드포인트를 사용하는 것 외에도 완전히 새로운 엔드포인트를 생성할 수도 있다.

먼저, 새로운 엔드포인트가 `Endpoint<T>` 인터페이스를 구현하도록 해야 한다.

```java
@Component
public class CustomEndpoint implements Endpoint<List<String>> {
    
    @Override
    public String getId() {
        return "customEndpoint";
    }

    @Override
    public boolean isEnabled() {
        return true;
    }

    @Override
    public boolean isSensitive() {
        return true;
    }

    @Override
    public List<String> invoke() {
        // Custom logic to build the output
        List<String> messages = new ArrayList<String>();
        messages.add("This is message 1");
        messages.add("This is message 2");
        return messages;
    }
}
```

이 새로운 엔드포인트에 액세스하기 위해 해당 ID를 사용하여 매핑한다. 즉, `/customEndpoint`를 눌러 이를 실행할 수 있다.

```json
[ "This is message 1", "This is message 2" ]
```

### 7) 추가 사용자 정의
보안을 위해 비표준 포트를 통해 액추에이터 엔드포인트를 노출하도록 선택할 수 있다. `management.port` 속성을 사용하여 이를 쉽게 구성할 수 있다.

또한 이미 언급했듯이 1.x에서 Actuator는 Spring Security를 ​​기반으로 하지만 나머지 애플리케이션과는 독립적인 자체 보안 모델을 구성한다.

따라서 네트워크를 통해 엔드포인트에 액세스할 수 있는 위치를 제한하기 위해 `management.address` 속성을 변경할 수 있다.

```properties
#port used to expose actuator
management.port=8081 

#CIDR allowed to hit actuator
management.address=127.0.0.1 

#Whether security should be enabled or disabled altogether
management.security.enabled=false
```

게다가 `/info`를 제외한 모든 내장 엔드포인트는 기본적으로 민감하다.

애플리케이션이 Spring Security를 ​​사용하는 경우 `application.properties` 파일에 기본 보안 속성(사용자 이름, 비밀번호 및 역할)을 정의하여 이러한 엔드포인트를 보호할 수 있다.

```properties
security.user.name=admin
security.user.password=secret
management.security.role=SUPERUSER
```

## [출처 및 참고]
* [https://www.baeldung.com/spring-boot-actuators](https://www.baeldung.com/spring-boot-actuators)
