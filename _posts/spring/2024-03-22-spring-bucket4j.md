---
title: Spring Bucket4j API 속도 제한
author: dejavuhyo
date: 2024-03-22 15:10:00 +0900
categories: [Framework, Spring]
tags: [spring-bucket4j, bucket4j, api-rate-limit, api-속도제한]
---

## 1. API 속도 제한
속도 제한은 [API에 대한 액세스를 제한](https://cloud.google.com/architecture/infra-reliability-guide/traffic-load?hl=ko) 하는 전략이다. 클라이언트가 특정 시간 내에 수행할 수 있는 API 호출 수를 제한한다. 이는 의도하지 않거나 악의적인 남용으로부터 API를 보호하는데 도움이 된다.

속도 제한은 IP 주소를 추적하거나 API 키 또는 액세스 토큰과 같은 보다 비즈니스에 특정한 방식으로 API에 적용되는 경우가 많다. API 개발자로서 클라이언트가 한도에 도달하면 몇 가지 옵션이 있다.

* 남은 기간이 경과할 때까지 요청 대기한다.

* 요청을 즉시 허용하지만 이 요청에 대해 추가 비용을 청구한다.

* 요청 거부(HTTP 429 Too Many Requests)한다.

## 2. Bucket4j 속도 제한 라이브러리

### 1) Bucket4j란
Bucket4j는 [token-bucket](https://en.wikipedia.org/wiki/Token_bucket) 알고리즘을 기반으로 하는 Java 속도 제한 라이브러리이다. Bucket4j는 독립형 JVM 애플리케이션이나 클러스터 환경에서 사용할 수 있는 스레드로부터 안전한 라이브러리이다. 또한 JCache(JSR107) 사양을 통해 인메모리 또는 분산 캐싱을 지원한다.

### 2) Token-bucket 알고리즘
API 속도 제한의 맥락에서 알고리즘을 직관적으로 살펴본다.

보유할 수 있는 토큰 수로 용량이 정의되는 버킷이 있다고 가정한다. 소비자가 API 엔드포인트에 액세스하려고 할 때마다 버킷에서 토큰을 가져와야 한다. 사용 가능한 경우 버킷에서 토큰을 제거하고 요청을 수락한다. 반대로 버킷에 토큰이 없으면 요청을 거부한다.

요청이 토큰을 소비하므로 버킷 용량을 초과하지 않도록 일정 비율로 토큰을 보충한다.

분당 100개의 요청으로 비율 제한이 있는 API를 생각해 본다. 용량이 100이고 리필 속도가 분당 토큰 100개인 버킷을 생성할 수 있다.

특정 분에 사용 가능한 토큰보다 적은 70개의 요청을 받으면 버킷 용량을 최대화하기 위해 다음 분 시작 시 토큰 30개만 추가한다. 반면에 40초 안에 모든 토큰을 소진한다면 버킷이 다시 채워지기까지 20초를 기다려야 한다.

## 3. Bucket4j 시작하기

### 1) 메이븐 구성
`pom.xml`에 [bucket4j](https://mvnrepository.com/artifact/com.github.vladimir-bukhtoyarov/bucket4j-core) 종속성을 추가한다.

```xml
<dependency>
    <groupId>com.bucket4j</groupId>
    <artifactId>bucket4j-core</artifactId>
    <version>8.1.0</version>
</dependency>
```

### 2) Terminology
Bucket4j를 사용하는 방법을 살펴보기 전에 핵심 클래스 중 일부에 대해 간략하게 설명하고 토큰 버킷 알고리즘의 공식 모델에서 다양한 요소를 나타내는 방법이다.

Bucket 인터페이스는 최대 용량을 가진 토큰 버킷을 나타낸다. 토큰 소비를 위해 tryConsume 및 tryConsumeAndReturnRemaining과 같은 메서드를 제공한다. 이러한 메서드는 요청이 제한을 준수하고 토큰이 소비된 경우 소비 결과를 true로 반환한다.

대역폭 클래스는 버킷의 한도를 정의하므로 버킷의 주요 구성 요소이다. 대역폭을 사용하여 버킷 용량과 리필 속도를 구성한다.

Refill 클래스는 토큰이 버킷에 추가되는 고정 비율을 정의하는데 사용된다. 특정 기간에 추가될 토큰 수로 비율을 구성할 수 있다. 예를 들어 초당 10개의 버킷 또는 5분당 200개의 토큰 등이다.

Bucket의 tryConsumeAndReturnRemaining 메서드는 ConsumptionProbe를 반환한다. ConsumptionProbe에는 소비 결과와 함께 남은 토큰이나 요청된 토큰을 버킷에서 다시 사용할 수 있을 때까지 남은 시간과 같은 버킷 상태가 포함된다.

### 3) 기본 사용법
몇 가지 기본 속도 제한 패턴을 테스트한다.

분당 요청 10개의 속도 제한에 대해 용량이 10이고 리필 속도가 분당 토큰 10개인 버킷을 생성한다.

```java
Refill refill = Refill.intervally(10, Duration.ofMinutes(1));
Bandwidth limit = Bandwidth.classic(10, refill);
Bucket bucket = Bucket.builder()
    .addLimit(limit)
    .build();

for (int i = 1; i <= 10; i++) {
    assertTrue(bucket.tryConsume(1));
}
assertFalse(bucket.tryConsume(1));
```

`Refill.intervally`는 기간이 시작될 때 버킷을 다시 채운다. 이 경우 분 시작 시 토큰 10개이다.

다음으로 리필이 작동하는 모습이다.

2초당 토큰 1개로 재충전 속도를 설정하고 속도 제한을 준수하도록 요청을 제한한다.

```java
Bandwidth limit = Bandwidth.classic(1, Refill.intervally(1, Duration.ofSeconds(2)));
Bucket bucket = Bucket.builder()
    .addLimit(limit)
    .build();
assertTrue(bucket.tryConsume(1));     // first request
Executors.newScheduledThreadPool(1)   // schedule another request for 2 seconds later
    .schedule(() -> assertTrue(bucket.tryConsume(1)), 2, TimeUnit.SECONDS); 
```

분당 10개의 요청으로 비율 제한이 있다고 가정한다. 동시에, 처음 5초 안에 모든 토큰을 소진시키는 급증을 피하고 싶을 수도 있다. Bucket4j를 사용하면 동일한 버킷에 여러 제한(대역폭)을 설정할 수 있다. 20초 동안 5개의 요청만 허용하는 또 다른 제한을 추가한다.

```java
Bucket bucket = Bucket.builder()
    .addLimit(Bandwidth.classic(10, Refill.intervally(10, Duration.ofMinutes(1))))
    .addLimit(Bandwidth.classic(5, Refill.intervally(5, Duration.ofSeconds(20))))
    .build();

for (int i = 1; i <= 5; i++) {
    assertTrue(bucket.tryConsume(1));
}
assertFalse(bucket.tryConsume(1));
```

## 4. Bucket4j를 사용하여 Spring API 속도 제한

### 1) Area Calculator API
간단하지만 매우 인기 있는 면적 계산기 REST API를 구현한다. 현재는 주어진 크기에 따라 직사각형의 면적을 계산하고 반환한다.

```java
@RestController
class AreaCalculationController {

    @PostMapping(value = "/api/v1/area/rectangle")
    public ResponseEntity<AreaV1> rectangle(@RequestBody RectangleDimensionsV1 dimensions) {
        return ResponseEntity.ok(new AreaV1("rectangle", dimensions.getLength() * dimensions.getWidth()));
    }
}
```

API가 실행되고 있는지 확인한다.

```shell
$ curl -X POST http://localhost:9001/api/v1/area/rectangle \
    -H "Content-Type: application/json" \
    -d '{ "length": 10, "width": 12 }'

{ "shape":"rectangle","area":120.0 }
```

### 2) Applying Rate Limit
이제 API에 분당 20개의 요청을 허용하는 순진한 속도 제한을 도입한다. 즉, API는 1분 동안 이미 20개의 요청을 받은 경우 요청을 거부한다.

버킷을 생성 하고 제한(대역폭)을 추가하도록 컨트롤러를 수정한다.

```java
@RestController
class AreaCalculationController {

    private final Bucket bucket;

    public AreaCalculationController() {
        Bandwidth limit = Bandwidth.classic(20, Refill.greedy(20, Duration.ofMinutes(1)));
        this.bucket = Bucket.builder()
            .addLimit(limit)
            .build();
    }
    //..
}
```

이 API에서는 tryConsume 메서드를 사용하여 버킷에서 토큰을 소비하여 요청이 허용되는지 확인할 수 있다. 제한에 도달한 경우 HTTP 429 요청이 너무 많음 상태로 응답하여 요청을 거부할 수 있다.

```java
public ResponseEntity<AreaV1> rectangle(@RequestBody RectangleDimensionsV1 dimensions) {
    if (bucket.tryConsume(1)) {
        return ResponseEntity.ok(new AreaV1("rectangle", dimensions.getLength() * dimensions.getWidth()));
    }

    return ResponseEntity.status(HttpStatus.TOO_MANY_REQUESTS).build();
}
```

```shell
# 21st request within 1 minute
$ curl -v -X POST http://localhost:9001/api/v1/area/rectangle \
    -H "Content-Type: application/json" \
    -d '{ "length": 10, "width": 12 }'

< HTTP/1.1 429
```

### 3) API 클라이언트 및 가격 계획
이제 API 요청을 조절할 수 있는 순진한 속도 제한이 있다. 다음으로, 좀 더 비즈니스 중심의 요금제를 위한 요금제이다.

가격 계획은 API로 수익을 창출하는데 도움이 된다. API 클라이언트에 대해 다음과 같은 계획이 있다고 가정한다.

* Free: API 클라이언트당 시간당 요청 20개

* Basic: API 클라이언트당 시간당 요청 40개

* Professional: API 클라이언트당 시간당 요청 100개

각 API 클라이언트는 각 요청과 함께 전송해야 하는 고유한 API 키를 받는다. 이는 API 클라이언트와 연결된 가격 계획을 식별하는데 도움이 된다.

각 요금제에 대한 속도 제한(대역폭)을 정의한다.

```java
enum PricingPlan {
    FREE {
        Bandwidth getLimit() {
            return Bandwidth.classic(20, Refill.intervally(20, Duration.ofHours(1)));
        }
    },
    BASIC {
        Bandwidth getLimit() {
            return Bandwidth.classic(40, Refill.intervally(40, Duration.ofHours(1)));
        }
    },
    PROFESSIONAL {
        Bandwidth getLimit() {
            return Bandwidth.classic(100, Refill.intervally(100, Duration.ofHours(1)));
        }
    };
    //..
}
```

그런 다음 지정된 API 키에서 가격 책정 계획을 해결하는 메서드를 추가한다.

```java
enum PricingPlan {
    
    static PricingPlan resolvePlanFromApiKey(String apiKey) {
        if (apiKey == null || apiKey.isEmpty()) {
            return FREE;
        } else if (apiKey.startsWith("PX001-")) {
            return PROFESSIONAL;
        } else if (apiKey.startsWith("BX001-")) {
            return BASIC;
        }
        return FREE;
    }
    //..
}
```

다음으로, 각 API 키에 대한 버킷을 저장하고 속도 제한을 위한 버킷을 검색해야 한다.

```java
class PricingPlanService {

    private final Map<String, Bucket> cache = new ConcurrentHashMap<>();

    public Bucket resolveBucket(String apiKey) {
        return cache.computeIfAbsent(apiKey, this::newBucket);
    }

    private Bucket newBucket(String apiKey) {
        PricingPlan pricingPlan = PricingPlan.resolvePlanFromApiKey(apiKey);
        return Bucket.builder()
            .addLimit(pricingPlan.getLimit())
            .build();
    }
}
```

이제 API 키당 메모리 내 버킷 저장소가 생겼다. PricingPlanService를 사용하도록 컨트롤러를 수정한다.

```java
@RestController
class AreaCalculationController {

    private PricingPlanService pricingPlanService;

    public ResponseEntity<AreaV1> rectangle(@RequestHeader(value = "X-api-key") String apiKey,
        @RequestBody RectangleDimensionsV1 dimensions) {

        Bucket bucket = pricingPlanService.resolveBucket(apiKey);
        ConsumptionProbe probe = bucket.tryConsumeAndReturnRemaining(1);
        if (probe.isConsumed()) {
            return ResponseEntity.ok()
                .header("X-Rate-Limit-Remaining", Long.toString(probe.getRemainingTokens()))
                .body(new AreaV1("rectangle", dimensions.getLength() * dimensions.getWidth()));
        }
        
        long waitForRefill = probe.getNanosToWaitForRefill() / 1_000_000_000;
        return ResponseEntity.status(HttpStatus.TOO_MANY_REQUESTS)
            .header("X-Rate-Limit-Retry-After-Seconds", String.valueOf(waitForRefill))
            .build();
    }
}
```

변경 사항을 확인한다. API 클라이언트는 `X-api-key` 요청 헤더와 함께 API 키를 보낸다. PricingPlanService를 사용하여 이 API 키에 대한 버킷을 가져오고 버킷에서 토큰을 소비하여 요청이 허용되는지 확인한다.

API의 클라이언트 경험을 향상시키기 위해 다음과 같은 추가 응답 헤더를 사용하여 속도 제한에 대한 정보를 보낸다.

* `X-Rate-Limit-Remaining`: 현재 시간 창에 남아 있는 토큰 수

* `X-Rate-Limit-Retry-After-Seconds`: 버킷이 다시 채워질 때까지 남은 시간(초)

ConsumptionProbe 메서드 getRemainingTokens 및 getNanosToWaitForRefill을 호출하여 버킷에 남아 있는 토큰 수와 다음 리필까지 남은 시간을 각각 가져올 수 있다. getNanosToWaitForRefill 메소드는 토큰을 성공적으로 사용할 수 있는 경우 0을 반환한다.

API를 호출해 본다.

```shell
## successful request
$ curl -v -X POST http://localhost:9001/api/v1/area/rectangle \
    -H "Content-Type: application/json" -H "X-api-key:FX001-99999" \
    -d '{ "length": 10, "width": 12 }'

< HTTP/1.1 200
< X-Rate-Limit-Remaining: 11
{"shape":"rectangle","area":120.0}

## rejected request
$ curl -v -X POST http://localhost:9001/api/v1/area/rectangle \
    -H "Content-Type: application/json" -H "X-api-key:FX001-99999" \
    -d '{ "length": 10, "width": 12 }'

< HTTP/1.1 429
< X-Rate-Limit-Retry-After-Seconds: 583
```

### 4) Spring MVC 인터셉터 사용
높이와 밑변을 고려하여 삼각형의 면적을 계산하고 반환하는 새로운 API 엔드포인트를 추가해야 한다고 가정한다.

```java
@PostMapping(value = "/triangle")
public ResponseEntity<AreaV1> triangle(@RequestBody TriangleDimensionsV1 dimensions) {
    return ResponseEntity.ok(new AreaV1("triangle", 0.5d * dimensions.getHeight() * dimensions.getBase()));
}
```

결과적으로 새로운 엔드포인트도 속도를 제한해야 한다. 이전 엔드포인트에서 속도 제한 코드를 복사하여 붙여넣기만 하면 된다. 또는 Spring MVC의 HandlerInterceptor를 사용하여 비즈니스 코드에서 속도 제한 코드를 분리 할 수 있다.

RateLimitInterceptor를 생성 하고 preHandle 메소드에 속도 제한 코드를 구현한다.

```java
public class RateLimitInterceptor implements HandlerInterceptor {

    private PricingPlanService pricingPlanService;

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) 
      throws Exception {
        String apiKey = request.getHeader("X-api-key");
        if (apiKey == null || apiKey.isEmpty()) {
            response.sendError(HttpStatus.BAD_REQUEST.value(), "Missing Header: X-api-key");
            return false;
        }

        Bucket tokenBucket = pricingPlanService.resolveBucket(apiKey);
        ConsumptionProbe probe = tokenBucket.tryConsumeAndReturnRemaining(1);
        if (probe.isConsumed()) {
            response.addHeader("X-Rate-Limit-Remaining", String.valueOf(probe.getRemainingTokens()));
            return true;
        } else {
            long waitForRefill = probe.getNanosToWaitForRefill() / 1_000_000_000;
            response.addHeader("X-Rate-Limit-Retry-After-Seconds", String.valueOf(waitForRefill));
            response.sendError(HttpStatus.TOO_MANY_REQUESTS.value(),
              "You have exhausted your API Request Quota"); 
            return false;
        }
    }
}
```

마지막으로 InterceptorRegistry에 인터셉터를 추가한다.

```java
public class Bucket4jRateLimitApp implements WebMvcConfigurer {
    
    private RateLimitInterceptor interceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(interceptor)
            .addPathPatterns("/api/v1/area/**");
    }
}
```

RateLimitInterceptor는 면적 계산 API 엔드포인트에 대한 각 요청을 가로챈다.

새로운 엔드포인트를 시험한다.

```shell
## successful request
$ curl -v -X POST http://localhost:9001/api/v1/area/triangle \
    -H "Content-Type: application/json" -H "X-api-key:FX001-99999" \
    -d '{ "height": 15, "base": 8 }'

< HTTP/1.1 200
< X-Rate-Limit-Remaining: 9
{"shape":"triangle","area":60.0}

## rejected request
$ curl -v -X POST http://localhost:9001/api/v1/area/triangle \
    -H "Content-Type: application/json" -H "X-api-key:FX001-99999" \
    -d '{ "height": 15, "base": 8 }'

< HTTP/1.1 429
< X-Rate-Limit-Retry-After-Seconds: 299
{ "status": 429, "error": "Too Many Requests", "message": "You have exhausted your API Request Quota" }
```

엔드포인트를 계속 추가할 수 있으며 인터셉터는 각 요청에 대해 속도 제한을 적용한다.

## 5. Bucket4j 스프링 부트 스타터
Spring 애플리케이션에서 Bucket4j를 사용하는 또 다른 방법이다. [Bucket4j Spring Boot Starter](https://github.com/MarcGiffing/bucket4j-spring-boot-starter)는 Spring Boot 애플리케이션 속성 또는 구성을 통해 API 속도 제한을 달성하는데 도움이 되는 Bucket4j에 대한 자동 구성을 제공한다.

Bucket4j 스타터를 애플리케이션에 통합하면 애플리케이션 코드 없이 완전히 선언적인 API 속도 제한 구현을 갖게 된다.

### 1) Rate Limit Filters
이 예에서는 요청 헤더 `X-api-key`의 값을 비율 제한을 식별하고 적용하기 위한 키로 사용했다.

Bucket4j Spring Boot Starter는 속도 제한 키를 정의하기 위해 사전 정의된 여러 구성을 제공한다.

* 기본 속도 제한 필터(기본값)

* IP 주소로 필터링

* 표현식 기반 필터

표현식 기반 필터는 [[SpEL(Spring Expression Language)](https://www.baeldung.com/spring-expression-language)을 사용한다. SpEL은 IP 주소(`getRemoteAddr()`), request headers(`getHeader('X-api-key')`) 등에 대한 필터 표현식을 작성하는데 사용할 수 있는 HttpServletRequest와 같은 루트 개체에 대한 액세스를 제공한다.

라이브러리는 필터 표현식에서 사용자 정의 클래스도 지원한다. 이에 대해서는 [documentation](https://github.com/MarcGiffing/bucket4j-spring-boot-starter#filter-types-bad-name-should-be-renamed-in-the-feature)에 설명되어 있다.

### 2) 메이븐 구성
`pom.xml`에 bucket4j-spring-boot-starter 종속성을 추가한다.

```xml
<dependency>
    <groupId>com.giffing.bucket4j.spring.boot.starter</groupId>
    <artifactId>bucket4j-spring-boot-starter</artifactId>
    <version>0.8.1</version>
</dependency>
```

이전 구현에서는 인메모리 맵을 사용하여 API 키(소비자)당 버킷을 저장했다. 여기서는 Caffeine 이나 Guava와 같은 메모리 내 저장소를 구성하기 위해 Spring의 캐싱 추상화를 사용할 수 있다.

캐싱 종속성을 추가한다.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
<dependency>
    <groupId>javax.cache</groupId>
    <artifactId>cache-api</artifactId>
</dependency>
<dependency>
    <groupId>com.github.ben-manes.caffeine</groupId>
    <artifactId>caffeine</artifactId>
    <version>2.8.2</version>
</dependency>
<dependency>
    <groupId>com.github.ben-manes.caffeine</groupId>
    <artifactId>jcache</artifactId>
    <version>2.8.2</version>
</dependency>
```

> 참고: Bucket4j의 캐싱 지원을 따르기 위해 [jcache](https://mvnrepository.com/artifact/javax.cache/cache-api) 종속성도 추가했다.

구성 클래스에 `@EnableCaching` 주석을 추가하여 캐싱 기능을 활성화해야 한다.

### 3) 애플리케이션 구성
Bucket4j 스타터 라이브러리를 사용하도록 애플리케이션을 구성한다. 먼저 API 키와 버킷을 메모리에 저장하도록 Caffeine 캐싱을 구성한다.

```yaml
spring:
  cache:
    cache-names:
    - rate-limit-buckets
    caffeine:
      spec: maximumSize=100000,expireAfterAccess=3600s
```

다음으로 Bucket4j를 구성한다.

```yaml
bucket4j:
  enabled: true
  filters:
  - cache-name: rate-limit-buckets
    url: /api/v1/area.*
    strategy: first
    http-response-body: "{ \"status\": 429, \"error\": \"Too Many Requests\", \"message\": \"You have exhausted your API Request Quota\" }"
    rate-limits:
    - cache-key: "getHeader('X-api-key')"
      execute-condition: "getHeader('X-api-key').startsWith('PX001-')"
      bandwidths:
      - capacity: 100
        time: 1
        unit: hours
    - cache-key: "getHeader('X-api-key')"
      execute-condition: "getHeader('X-api-key').startsWith('BX001-')"
      bandwidths:
      - capacity: 40
        time: 1
        unit: hours
    - cache-key: "getHeader('X-api-key')"
      bandwidths:
      - capacity: 20
        time: 1
        unit: hours
```

구성에 대한 설명이다.

* `bucket4j.enabled=true` - Bucket4j 자동 구성을 활성화한다.

* `bucket4j.filters.cache-name` - 캐시에서 API 키에 대한 버킷을 가져온다.

* `bucket4j.filters.url` - 비율 제한을 적용하기 위한 경로 표현식을 나타낸다.

* `bucket4j.filters.strategy=first` - 첫 번째 일치 비율 제한 구성에서 중지된다.

* `bucket4j.filters.rate-limits.cache-key` - SpEL(Spring Expression Language)을 사용하여 키를 검색한다.

* `bucket4j.filters.rate-limits.execute-condition` - SpEL을 사용하여 속도 제한을 실행할지 여부를 결정한다.

* `bucket4j.filters.rate-limits.bandwidths` - Bucket4j 속도 제한 매개변수를 정의한다.

PricingPlanService 및 RateLimitInterceptor를 순차적으로 평가되는 속도 제한 구성 목록으로 대체했다.

시도해 본다.

```shell
## successful request
$ curl -v -X POST http://localhost:9000/api/v1/area/triangle \
    -H "Content-Type: application/json" -H "X-api-key:FX001-99999" \
    -d '{ "height": 20, "base": 7 }'

< HTTP/1.1 200
< X-Rate-Limit-Remaining: 7
{"shape":"triangle","area":70.0}

## rejected request
$ curl -v -X POST http://localhost:9000/api/v1/area/triangle \
    -H "Content-Type: application/json" -H "X-api-key:FX001-99999" \
    -d '{ "height": 7, "base": 20 }'

< HTTP/1.1 429
< X-Rate-Limit-Retry-After-Seconds: 212
{ "status": 429, "error": "Too Many Requests", "message": "You have exhausted your API Request Quota" }
```

## [출처 및 참고]
* [https://www.baeldung.com/spring-bucket4j](https://www.baeldung.com/spring-bucket4j)
