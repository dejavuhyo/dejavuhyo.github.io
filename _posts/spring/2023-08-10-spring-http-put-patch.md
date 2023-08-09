---
title: Spring REST API PUT vs HTTP PATCH
author: dejavuhyo
date: 2023-08-10 08:00:00 +0900
categories: [Framework, Spring]
tags: [spring-http-putm spring-http-patch, http-put-patch]
---

## 1. Put을 사용해야 하는 경우와 Patch를 사용해야 하는 경우
클라이언트가 기존 리소스를 완전히 교체해야 하는 경우 PUT을 사용할 수 있다. 부분 업데이트를 수행할 때 HTTP PATCH를 사용할 수 있다.

예를 들어 리소스의 단일 필드를 업데이트할 때 전체 리소스 표현을 보내는 것은 번거로울 수 있으며 불필요한 대역폭을 많이 사용한다. 이러한 경우 PATCH의 의미 체계가 훨씬 더 의미가 있다.

고려해야 할 또 다른 중요한 측면은 멱등성입니다. PUT은 멱등적이다. PATCH는 멱등적일 수 있지만 반드시 그럴 필요는 없다. 따라서 우리가 구현하는 작업의 의미 체계에 따라 이 특성에 따라 둘 중 하나를 선택할 수도 있다.

## 2. PUT 및 PATCH 로직 구현
여러 필드가 있는 HeavyResource를 업데이트하기 위해 REST API를 구현한다고 가정한다.

```java
public class HeavyResource {
    private Integer id;
    private String name;
    private String address;
    // ...
```

먼저 PUT을 사용하여 리소스의 전체 업데이트를 처리하는 엔드포인트를 생성해야 한다.

```java
@PutMapping("/heavyresource/{id}")
public ResponseEntity<?> saveResource(@RequestBody HeavyResource heavyResource,
  @PathVariable("id") String id) {
    heavyResourceRepository.save(heavyResource, id);
    return ResponseEntity.ok("resource saved");
}
```

리소스 업데이트를 위한 표준 엔드포인트이다.

이제 클라이언트가 주소 필드를 자주 업데이트한다고 가정한다. 이 경우 모든 필드가 포함된 전체 HeavyResource 개체를 보내지 않고 PATCH 메서드를 통해 주소 필드만 업데이트하는 기능을 원한다.

주소 필드의 부분 업데이트를 나타내는 HeavyResourceAddressOnly DTO를 만들 수 있다.

```java
public class HeavyResourceAddressOnly {
    private Integer id;
    private String address;
    
    // ...
}
```

다음으로 PATCH 메서드를 활용하여 부분 업데이트를 보낼 수 있다.

```java
@PatchMapping("/heavyresource/{id}")
public ResponseEntity<?> partialUpdateName(
  @RequestBody HeavyResourceAddressOnly partialUpdate, @PathVariable("id") String id) {
    
    heavyResourceRepository.save(partialUpdate, id);
    return ResponseEntity.ok("resource address updated");
}
```

이 보다 세분화된 DTO를 사용하면 전체 HeavyResource를 보내는 오버헤드 없이 업데이트해야 하는 필드만 보낼 수 있다.

이러한 부분 업데이트 작업이 많은 경우 각 출력에 대한 사용자 지정 DTO 생성을 건너뛰고 맵만 사용할 수도 있다.

```java
@RequestMapping(value = "/heavyresource/{id}", method = RequestMethod.PATCH, consumes = MediaType.APPLICATION_JSON_VALUE)
public ResponseEntity<?> partialUpdateGeneric(
  @RequestBody Map<String, Object> updates,
  @PathVariable("id") String id) {
    
    heavyResourceRepository.save(updates, id);
    return ResponseEntity.ok("resource updated");
}
```

이 솔루션은 API 구현에 더 많은 유연성을 제공하지만 유효성 검사와 같은 몇 가지 사항도 잃게 된다.

## 3. PUT 및 PATCH 테스트
두 HTTP 메서드에 대한 테스트이다.

먼저 PUT 메서드를 통해 전체 리소스의 업데이트를 테스트하려고 한다.

```java
mockMvc.perform(put("/heavyresource/1")
  .contentType(MediaType.APPLICATION_JSON_VALUE)
  .content(objectMapper.writeValueAsString(
    new HeavyResource(1, "Tom", "Jackson", 12, "heaven street")))
  ).andExpect(status().isOk());
```

부분 업데이트 실행은 PATCH 방법을 사용하여 수행된다.

```java
mockMvc.perform(patch("/heavyrecource/1")
  .contentType(MediaType.APPLICATION_JSON_VALUE)
  .content(objectMapper.writeValueAsString(
    new HeavyResourceAddressOnly(1, "5th avenue")))
  ).andExpect(status().isOk());
```

보다 일반적인 접근 방식에 대한 테스트를 작성할 수도 있다.

```java
HashMap<String, Object> updates = new HashMap<>();
updates.put("address", "5th avenue");

mockMvc.perform(patch("/heavyresource/1")
    .contentType(MediaType.APPLICATION_JSON_VALUE)
    .content(objectMapper.writeValueAsString(updates))
  ).andExpect(status().isOk());
 
```

## 4. Null 값이 있는 부분 요청 처리
PATCH 메서드에 대한 구현을 작성할 때 HeavyResourceAddressOnly의 주소 필드에 대한 값이 null일 때 처리하는 방법이다.

클라이언트가 다음 요청을 보낸다고 가정합니다.

```json
{
   "id" : 1,
   "address" : null
}
```

그런 다음 주소 필드의 값을 null로 설정 하거나 변경 사항이 없는 것으로 처리하여 이러한 요청을 무시하는 것으로 처리할 수 있다.

null을 처리하기 위한 하나의 전략을 선택 하고 모든 PATCH 메서드 구현에서 이를 고수해야 한다.

## [출처 및 참고]
* [https://www.baeldung.com/http-put-patch-difference-spring](https://www.baeldung.com/http-put-patch-difference-spring)
