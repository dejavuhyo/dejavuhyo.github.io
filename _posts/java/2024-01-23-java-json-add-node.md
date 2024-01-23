---
title: Java Json 노드 추가
author: dejavuhyo
date: 2024-01-23 17:10:00 +0900
categories: [Language, Java]
tags: [java-json, json-node, json-add-node, jackson, 자바-json, json-노드, json-노드-추가]
---

## 1. Json 노드 추가
JsonNode 객체는 불변(immutable)하는 객체이기 때문에 변경하려면 가변 객체인 ObjectNode로 캐스팅해서 바꿔야 한다.

## 2. Jackson Dependency 추가

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.16.1</version>
</dependency>
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-core</artifactId>
    <version>2.16.1</version>
</dependency>
```

## 3. 예제

* JsonAddNode.java

```java
import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.JsonNode;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.node.JsonNodeFactory;
import com.fasterxml.jackson.databind.node.ObjectNode;
import lombok.extern.slf4j.Slf4j;

@Slf4j
public class JsonAddNode {
    public static void main(String[] args) throws JsonProcessingException {

        String jsonString = "{\n" +
                "    \"date\": \"2024-01-15\",\n" +
                "    \"sendOrgCd\": \"F001\",\n" +
                "    \"stat\": \"01\",\n" +
                "    \"orgCd\": \"N11\",\n" +
                "    \"carNum\": \"123가4567\",\n" +
                "    \"startDate\": \"2024-01-01\",\n" +
                "    \"endDate\": \"9999-12-31\"\n" +
                "}";

        ObjectMapper objectMapper = new ObjectMapper();
        JsonNode jsonNode = objectMapper.readTree(jsonString);
        ObjectNode objectNode = JsonNodeFactory.instance.objectNode();
        objectNode.set("result", jsonNode);
        log.info(String.valueOf(objectNode));
    }
}
```

## [출처 및 참고]
* [https://jdm.kr/blog/224](https://jdm.kr/blog/224)
