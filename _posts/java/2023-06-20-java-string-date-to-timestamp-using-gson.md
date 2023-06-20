---
title: Java Gson을 이용하여 String 형태의 날짜를 Date로 변경
author: dejavuhyo
date: 2023-06-20 09:05:00 +0900
categories: [Language, Java]
tags: [java-gson, java-date, java-tine-zine, invalid-time-zone, string-date, gson-날짜]
---

## 1. yyyy-MM-dd HH:mm:ss 형태

* Json

```json
{
    "dateTime": "2023-05-24 09:30:50"
}
```

* ExampleDto.java

```java
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;

import java.sql.Timestamp;

@Getter
@Setter
@NoArgsConstructor
public class ExampleDto {
    private Timestamp dateTime;
}
```

* Timestamp 변환

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
    public void dateExample1(String json) {
        Gson gson = new GsonBuilder().setDateFormat("yyyy-MM-dd HH:mm:ss").create();
        ExampleDto exampleDTO = gson.fromJson(json, ExampleDto.class);
    }
}
```

## 2. 밀리세컨드 형태

* Json

```json
{
    "dateTime": "1684888240164"
}
```

* ExampleDto.java

```java
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;

import java.sql.Timestamp;

@Getter
@Setter
@NoArgsConstructor
public class ExampleDto {
    private Timestamp dateTime;
}
```

* Timestamp 변환

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

## [출처 및 참고]
* [https://stackoverflow.com/questions/6873020/gson-date-format](https://stackoverflow.com/questions/6873020/gson-date-format)
* [https://stackoverflow.com/questions/41958062/convert-string-date-to-object-yields-invalid-time-zone-indicator-0](https://stackoverflow.com/questions/41958062/convert-string-date-to-object-yields-invalid-time-zone-indicator-0)
