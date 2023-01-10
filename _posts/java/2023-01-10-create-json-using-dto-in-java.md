---
title: Java에서 DTO를 이용하여 JSON 만들기
author: dejavuhyo
date: 2023-01-10 21:10:00 +0900
categories: [Language, Java]
tags: [java-json, java-jackson, jackson, java-dto-json, dto-json, json, 자바-제이슨, 제이슨, dto-제이슨]
---

## 1. Dependency 설정

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.14.1</version>
</dependency>
```

## 2. JSON 생성 예제

* JsonGenerate.java

```java
import com.example.json.dto.JsonDto;
import com.fasterxml.jackson.databind.ObjectMapper;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class JsonGenerate {
    public static void main(String[] args) {

        JsonDto jsonDto = new JsonDto();
        jsonDto.setUserId("uid");
        jsonDto.setUserName("홍길동");

        List<String> hobbyList = new ArrayList<>();
        hobbyList.add("축구");
        hobbyList.add("야구");
        hobbyList.add("농구");
        jsonDto.setHobbyList(hobbyList);

        Map<String, Object> data = new HashMap<>();
        data.put("data", jsonDto);

        try {
            String json = new ObjectMapper().writeValueAsString(data);
            System.out.println(json);

        } catch (Exception e) {
            e.getMessage();
        }
    }
}
```

* JsonDto

```java
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;

import java.util.List;

@Getter
@Setter
@NoArgsConstructor
public class JsonDto {
    private String userId;
    private String userName;
    private List<String> hobbyList;
}
```

* 결과

```json
{
    "data": {
        "userId": "uid",
        "userName": "홍길동",
        "hobbyList": ["축구", "야구", "농구"]
    }
}
```

## [출처 및 참고]
* [https://bono915.tistory.com/entry/JAVA-%EC%9E%90%EB%B0%94%EC%97%90%EC%84%9C-JSON-%EB%A7%8C%EB%93%A4%EA%B8%B0-%EB%B0%8F-JSON-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EC%B6%9C%EB%A0%A5](https://bono915.tistory.com/entry/JAVA-%EC%9E%90%EB%B0%94%EC%97%90%EC%84%9C-JSON-%EB%A7%8C%EB%93%A4%EA%B8%B0-%EB%B0%8F-JSON-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EC%B6%9C%EB%A0%A5)
