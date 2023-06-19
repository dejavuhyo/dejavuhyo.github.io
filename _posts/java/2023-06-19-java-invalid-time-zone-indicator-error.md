---
title: Java java.text.ParseException Invalid time zone indicator
author: dejavuhyo
date: 2023-06-19 09:40:00 +0900
categories: [Language, Java]
tags: [java-gson, java-date, java-tine-zine, invalid-time-zone, string-date]
---

## 1. 오류 메시지
String 형태의 DateTime을 DB에 입력하기 위해 Timestamp 형으로 변환시 생기는 오류이다.

```text
gson java.text.ParseException Invalid time zone indicator '0'
```

## 2. 해결방법
밀리세컨드 형태의 DateTime이기 때문에 Gson의 설정이 필요하다.

* Json

```json
{
    "dateTime": "1684888240164"
}
```

* Example.java

```java
import com.example.webclient.dto.ExampleDto;
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonDeserializationContext;
import com.google.gson.JsonDeserializer;
import com.google.gson.JsonElement;
import com.google.gson.JsonParseException;

import java.lang.reflect.Type;
import java.util.Date;

public class Example {
    final Gson builder = new GsonBuilder().registerTypeAdapter(Date.class, new JsonDeserializer<Date>() {
        public Date deserialize(JsonElement jsonElement, Type type, JsonDeserializationContext context) throws JsonParseException {
            return new Date(jsonElement.getAsJsonPrimitive().getAsLong());
        }
    }).create();

    public void dateExample2(String json) {
        ExampleDto exampleDto = builder.fromJson(json, ExampleDto.class);
    }
}
```

> __참고:__ `2023년 05월 24일 09시 30분 50초`를 날짜와 시간을 모두 붙인 `20230524093050`은 *밀리세컨드 형태의 데이터*이기 때문에 Gson을 사용하여 Timestamp로 변환하면 잘못된 날짜와 시간이 나온다.

## [출처 및 참고]
* [https://stackoverflow.com/questions/41958062/convert-string-date-to-object-yields-invalid-time-zone-indicator-0](https://stackoverflow.com/questions/41958062/convert-string-date-to-object-yields-invalid-time-zone-indicator-0)
