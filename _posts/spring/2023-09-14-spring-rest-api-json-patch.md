---
title: Spring REST API에서 JSON Patch 사용
author: dejavuhyo
date: 2023-09-14 09:45:00 +0900
categories: [Framework, Spring]
tags: [spring-json, spring-rest-api, rest-api-json, json-patch]
---

## 1. 사용 사례
JSON 문서로 표현되는 HTTP 고객 리소스의 예이다.

```json
{ 
    "id":"1",
    "telephone":"001-555-1234",
    "favorites":["Milk","Eggs"],
    "communicationPreferences": {"post":true, "email":true}
}
```

이 고객의 전화번호가 변경되었고 고객이 즐겨찾는 제품 목록에 새 항목을 추가했다고 가정한다. 즉, 고객의 전화번호 및 즐겨찾기 필드만 업데이트하면 된다.

널리 사용되는 HTTP PUT 방법이 먼저 떠오른다. 그러나 PUT은 리소스를 완전히 대체하기 때문에 부분 업데이트를 적용하는데 적합한 방법이 아니다. 또한 클라이언트는 업데이트를 적용하고 저장하기 전에 GET을 수행해야 한다.

여기서 HTTP PATCH 방법이 유용하다.

## 2. HTTP PATCH 방식과 JSON 패치 형식
HTTP PATCH 방법은 리소스에 부분 업데이트를 적용하는 좋은 방법을 제공한다. 결과적으로 클라이언트는 요청의 차이점만 보내면 된다.

HTTP PATCH 요청의 간단한 예이다.

```text
PATCH /customers/1234 HTTP/1.1
Host: www.example.com
Content-Type: application/example
If-Match: "e0023aa4e"
Content-Length: 100

[description of changes]
```

HTTP PATCH 요청 본문은 새 버전을 생성하기 위해 대상 리소스를 수정하는 방법을 설명한다. 또한, [변경사항 설명]을 표현하는데 사용되는 형식은 리소스 유형에 따라 다르다. JSON 리소스 유형의 경우 변경 사항을 설명하는데 사용되는 형식은 JSON Patch 이다.

JSON 패치 형식은 "일련의 작업"을 사용하여 대상 리소스를 수정하는 방법을 설명한다. JSON 패치 문서는 JSON 개체의 배열이다. 배열의 각 객체는 정확히 하나의 JSON 패치 작업을 나타낸다.

이제 몇 가지 예와 함께 JSON 패치 작업을 확인한다.

## 3. JSON 패치 작업
JSON 패치 작업은 단일 op 개체로 표현된다.

예를 들어, 여기에서는 고객의 전화번호를 업데이트하는 JSON 패치 작업을 정의한다.

```json
{
    "op":"replace",
    "path":"/telephone",
    "value":"001-555-5678"
}
```

각 작업에는 하나의 경로 멤버가 있어야 한다. 또한 일부 작업 개체에는 from 멤버도 포함되어야 한다. 경로 및 멤버의 값은 [JSON 포인터](https://datatracker.ietf.org/doc/html/rfc6901) 이다. 대상 문서 내의 위치를 ​​나타낸다. 이 위치는 대상 개체의 특정 키나 배열 요소를 가리킬 수 있다.

사용 가능한 JSON 패치 작업을 확인한다.

### 1) add 작업
add 작업을 사용하여 개체에 새 멤버를 추가한다. 또한 이를 사용하여 기존 멤버를 업데이트하고 지정된 인덱스의 배열에 새 값을 삽입할 수 있다.

예를 들어, 인덱스 0에 있는 고객의 즐겨찾기 목록에 "Bread"를 추가한다.

```json
{
    "op":"add",
    "path":"/favorites/0",
    "value":"Bread"
}
```

add 작업 후 수정된 고객 세부정보는 다음과 같다.

```json
{
    "id":"1",
    "telephone":"001-555-1234",
    "favorites":["Bread","Milk","Eggs"],
    "communicationPreferences": {"post":true, "email":true}
}
```

### 2) remove 작업

remove 작업은 대상 위치에서 값을 제거한다. 게다가, 지정된 인덱스의 배열에서 요소를 제거할 수도 있다.

예를 들어 고객의 communcationPreferences를 제거한다.

```json
{
    "op":"remove",
    "path":"/communicationPreferences"
}
```

remove 작업 후 수정된 고객 세부정보는 다음과 같다.

```json
{
    "id":"1",
    "telephone":"001-555-1234",
    "favorites":["Bread","Milk","Eggs"],
    "communicationPreferences":null
}
```

### 3) replace 작업
replace 작업은 대상 위치의 값을 새 값으로 업데이트한다.

예를 들어 고객의 전화번호를 업데이트 한다.

```json
{
    "op":"replace",
    "path":"/telephone",
    "value":"001-555-5678"
}
```

replace 작업 후 수정된 고객 세부정보는 다음과 같다.

```json
{ 
    "id":"1", 
    "telephone":"001-555-5678",
    "favorites":["Bread","Milk","Eggs"],
    "communicationPreferences":null
}
```

### 4) move 작업
move 작업은 지정된 위치의 값을 제거하고 대상 위치에 추가한다.

예를 들어, 고객의 즐겨찾기 목록 상단에서 "빵"을 목록 하단으로 이동한다.

```json
{
    "op":"move",
    "from":"/favorites/0",
    "path":"/favorites/-"
}
```

move 작업 후 수정된 고객 세부정보는 다음과 같다.

```json
{ 
    "id":"1",
    "telephone":"001-555-5678",
    "favorites":["Milk","Eggs","Bread"],
    "communicationPreferences":null
}
```

위 예에서 `/favorites/0` 및 `/favorites/-`는 즐겨찾기 배열의 시작 및 끝 인덱스에 대한 JSON 포인터 이다.

### 5) copy 작업
copy 작업은 지정된 위치의 값을 대상 위치에 복사한다.

예를 들어, 즐겨찾기 목록에 "Milk"를 복제해 본다.

```json
{
    "op":"copy",
    "from":"/favorites/0",
    "path":"/favorites/-"
}
```

복copy사 작업 후 수정된 고객 세부정보는 다음과 같다.

```json
{ 
    "id":"1", 
    "telephone":"001-555-5678", 
    "favorites":["Milk","Eggs","Bread","Milk"], 
    "communicationPreferences":null
}
```

### 6) test 작업
test 작업은 "경로"의 값이 "값"과 동일한지 테스트한다. PATCH 작업은 원자성이므로 해당 작업 중 하나라도 실패하면 PATCH를 삭제해야 한다. 테스트 작업을 사용하여 사전 조건과 사후 조건이 충족되었는지 확인할 수 있다.

예를 들어, 고객의 전화번호 필드가 성공적으로 업데이트되었는지 테스트 한다.

```json
{
    "op":"test", 
    "path":"/telephone",
    "value":"001-555-5678"
}
```

이제 위의 개념을 예제에 어떻게 적용할 수 있는지 확인한다.

## 4. JSON 패치 형식을 사용한 HTTP 패치 요청
고객 사용 사례이다.

다음은 JSON 패치 형식을 사용하여 고객의 전화번호 및 즐겨찾기 목록에 대한 부분 업데이트를 수행하기 위한 HTTP PATCH 요청이다.

```shell
curl -i -X PATCH http://localhost:8080/customers/1 -H "Content-Type: application/json-patch+json" -d '[
    {"op":"replace","path":"/telephone","value":"+1-555-56"},
    {"op":"add","path":"/favorites/0","value":"Bread"}
]'
```

가장 중요한 점은 JSON 패치 요청의 `Content-Type`이 `application/json-patch+json` 이라는 것이다. 또한 요청 본문은 JSON 패치 작업 개체의 배열이다.

```json
[
    {"op":"replace","path":"/telephone","value":"+1-555-56"},
    {"op":"add","path":"/favorites/0","value":"Bread"}
]
```

서버 측에서는 이러한 요청 작업을 순차적으로 평가하고 이를 원자 단위로 대상 리소스에 적용하는 사용자 지정 프레임워크를 작성하는 것이다. 분명히 이 접근 방식은 복잡해 보인다. 또한 패치 문서를 표준화되지 않은 방식으로 소비하게 될 수도 있다.

다행히도 JSON 패치 요청 처리를 직접 처리할 필요는 없다.

원래 [JSR 353](https://jcp.org/en/jsr/detail?id=353)에서 정의된 JSON 처리 1.0용 Java API 또는 JSON-P 1.0은 [JSR 374](https://www.jcp.org/en/jsr/detail?id=374)에서 JSON 패치에 대한 지원을 도입했다. JSON-P API는 JSON 패치 구현을 나타내는 JsonPatch 유형을 제공한다.

그러나 JSON-P는 API일 뿐이다. JSON-P API를 사용하려면 이를 구현하는 라이브러리를 사용해야 한다. 예제에서는 json-patch라는 라이브러리 중 하나를 사용한다.

위의 JSON 패치 형식을 사용하여 HTTP PATCH 요청을 사용하는 REST 서비스를 구축할 수 있는 방법이다.

## 5. Spring Boot 애플리케이션에서 JSON 패치 구현

### 1) 종속성
최신 버전의 [json-patch](https://mvnrepository.com/artifact/com.github.java-json-tools/json-patch)는 Maven Central 저장소에서 찾을 수 있다.

먼저 `pom.xml`에 종속성을 추가한다.

```xml
<dependency>
    <groupId>com.github.java-json-tools</groupId>
    <artifactId>json-patch</artifactId>
    <version>1.12</version>
</dependency>
```

이제 고객 JSON 문서를 나타내는 스키마 클래스를 정의한다.

```java
public class Customer {
    private String id;
    private String telephone;
    private List<String> favorites;
    private Map<String, Boolean> communicationPreferences;

    // standard getters and setters
}
```

### 2) REST Controller Method
고객 사용 사례에 맞게 HTTP PATCH를 구현할 수 있다.

```java
@PatchMapping(path = "/{id}", consumes = "application/json-patch+json")
public ResponseEntity<Customer> updateCustomer(@PathVariable String id, @RequestBody JsonPatch patch) {
    try {
        Customer customer = customerService.findCustomer(id).orElseThrow(CustomerNotFoundException::new);
        Customer customerPatched = applyPatchToCustomer(patch, customer);
        customerService.updateCustomer(customerPatched);
        return ResponseEntity.ok(customerPatched);
    } catch (JsonPatchException | JsonProcessingException e) {
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).build();
    } catch (CustomerNotFoundException e) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND).build();
    }
}
```

이 메서드에서 무슨 일이 일어나고 있는지 확인한다.

* 먼저 `@PatchMapping` 주석을 사용하여 해당 메소드를 PATCH 핸들러 메소드로 표시한다.

* `application/json-patch+json "Content-Type"`이 포함된 패치 요청이 도착하면 Spring Boot는 기본 MappingJackson2HttpMessageConverter를 사용하여 요청 페이로드를 JsonPatch 인스턴스로 변환한다. 결과적으로 컨트롤러 메서드는 요청 본문을 JsonPatch 인스턴스로 수신한다.

메소드 내에서 이다.

* 먼저, `customerService.findCustomer(id)` 메소드를 호출하여 고객 기록을 찾는다.

* 이후에 고객 기록이 발견되면 `applyPatchToCustomer(patch, customer)` 메소드를 호출한다. 이렇게 하면 JsonPatch가 고객에게 적용된다.

* 그런 다음 `customerService.updateCustomer(customerPatched)`를 호출하여 고객 기록을 저장한다.

* 마지막으로 응답에 패치된 고객 세부 정보가 포함된 200 OK 응답을 클라이언트에 반환한다.

가장 중요한 것은 `ApplyPatchToCustomer(patch, customer)` 메소드에서 발생한다는 것이다.

```java
private Customer applyPatchToCustomer(
  JsonPatch patch, Customer targetCustomer) throws JsonPatchException, JsonProcessingException {
    JsonNode patched = patch.apply(objectMapper.convertValue(targetCustomer, JsonNode.class));
    return objectMapper.treeToValue(patched, Customer.class);
}
```

* 우선 대상 고객에게 적용할 작업 목록을 보유하는 JsonPatch 인스턴스가 있다.

* 그런 다음 대상 Customer를 `com.fasterxml.jackson.databind.JsonNode`의 인스턴스로 변환하고 이를 `JsonPatch.apply` 메서드에 전달하여 패치를 적용한다. 그 뒤에서 `JsonPatch.apply`는 대상에 작업을 적용하는 작업을 처리한다. 패치 결과도 `com.fasterxml.jackson.databind.JsonNode` 인스턴스 이다.

* 그런 다음 패치된 `com.fasterxml.jackson.databind.JsonNode`의 데이터를 Customer 유형에 바인딩하는 `objectMapper.treeToValue` 메서드를 호출한다. 이것은 패치된 고객 인스턴스 이다.

* 마지막으로 패치된 Customer 인스턴스를 반환한다

### 3) 테스트
API에 대한 POST 요청을 사용하여 고객을 생성한다.

```shell
curl -i -X POST http://localhost:8080/customers -H "Content-Type: application/json" 
  -d '{"telephone":"+1-555-12","favorites":["Milk","Eggs"],"communicationPreferences":{"post":true,"email":true}}'
```

201 Created 응답을 받는다.

```text
HTTP/1.1 201
Location: http://localhost:8080/customers/1
```

위치 응답 헤더는 새 리소스의 위치로 설정된다. 신규 Customer의 ID가 1임을 나타낸다.

다음으로 PATCH 요청을 사용하여 이 고객에게 부분 업데이트를 요청한다.

```shell
curl -i -X PATCH http://localhost:8080/customers/1 -H "Content-Type: application/json-patch+json" -d '[
    {"op":"replace","path":"/telephone","value":"+1-555-56"}, 
    {"op":"add","path":"/favorites/0","value": "Bread"}
]'
```

패치된 고객 세부 정보가 포함된 200 OK 응답을 받았다.

```text
HTTP/1.1 200
Content-Type: application/json
Transfer-Encoding: chunked
Date: Fri, 14 Feb 2020 21:23:14 GMT

{"id":"1","telephone":"+1-555-56","favorites":["Bread","Milk","Eggs"],"communicationPreferences":{"post":true,"email":true}}
```

## [출처 및 참고]
* [https://www.baeldung.com/spring-rest-json-patch](https://www.baeldung.com/spring-rest-json-patch)
