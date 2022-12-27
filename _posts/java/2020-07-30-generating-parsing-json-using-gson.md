---
title: Gson 사용하여 Json 생성 및 파싱
author: dejavuhyo
date: 2020-07-30 11:00:00 +0900
categories: [Language, Java]
tags: [gson, json-parsing, json-make, json-object, gson-사용, json-파싱, json-생성, json-객체]
---

## 1. Gson Download

* Gradle

```gradle
dependencies {
    implementation 'com.google.code.gson:gson:2.8.6'
}
```

* Maven

```xml
<dependency>
    <groupId>com.google.code.gson</groupId>
    <artifactId>gson</artifactId>
    <version>2.8.6</version>
</dependency>
```

## 2. Json 생성

```java
import com.google.gson.Gson;
import com.google.gson.JsonObject;

public class JsonGenerating {

    public static void main(String[] args) {

        Gson gson = new Gson();
        JsonObject jsonObject = new JsonObject();
        jsonObject.addProperty("cntlrId", "3001");
        jsonObject.addProperty("pnlTp", "20.99552");
        jsonObject.addProperty("pnlHd", "52.51834");
        jsonObject.addProperty("colctdate", "2020-06-30 00:00:00");
        jsonObject.addProperty("cntlrMode", "Manual");

        String json = gson.toJson(jsonObject);
        System.out.println(json);
    }
}
```

## 3. Json 파싱
```java
import com.google.gson.JsonElement;
import com.google.gson.JsonParser;

public class JsonParsing {

    public static void main(String[] args) {
        JsonParsing gson = new JsonParsing();
        gson.parsing("{\"cntlrId\":\"3001\",\"pnlTp\":\"20.99552\",\"pnlHd\":\"52.51834\"" +
                ",\"colctdate\":\"2020-06-30 00:00:00\",\"cntlrMode\":\"Manual\"}");
    }

    private void parsing(String jsonData) {

        JsonParser jsonParser = new JsonParser();
        JsonElement element = jsonParser.parse(jsonData);

        String cntlrId = element.getAsJsonObject().get("cntlrId").getAsString();
        System.out.println("cntlrId : " + cntlrId);

        String pnlTp = element.getAsJsonObject().get("pnlTp").getAsString();
        System.out.println("pnlTp : " + pnlTp);

        String pnlHd = element.getAsJsonObject().get("pnlHd").getAsString();
        System.out.println("pnlHd : " + pnlHd);

        String colctdate = element.getAsJsonObject().get("colctdate").getAsString();
        System.out.println("colctdate : " + colctdate);

        String cntlrMode = element.getAsJsonObject().get("cntlrMode").getAsString();
        System.out.println("cntlrMode : " + cntlrMode);
    }
}
```

## 4. Json 객체로 변환

* JsonObject.java

```java
import com.google.gson.Gson;

public class JsonObject {

    public static void main(String[] args) {
        JsonObject gson = new JsonObject();
        gson.parsing("{\"cntlrId\":\"3001\",\"pnlTp\":\"20.99552\",\"pnlHd\":\"52.51834\"" +
                ",\"colctdate\":\"2020-06-30 00:00:00\",\"cntlrMode\":\"Manual\"}");
    }

    private void parsing(String jsonData) {

        Gson gson = new Gson();
        JsonVO jsonVO = gson.fromJson(jsonData, JsonVO.class);

        System.out.println("cntlrId : " + jsonVO.getCntlrId());
        System.out.println("pnlTp : " + jsonVO.getPnlTp());
        System.out.println("pnlHd : " + jsonVO.getPnlHd());
        System.out.println("colctdate : " + jsonVO.getColctdate());
        System.out.println("cntlrMode : " + jsonVO.getCntlrMode());
    }
}
```

* JsonVO.java

```java
public class JsonVO {

    private String cntlrId;
    private String pnlTp;
    private String pnlHd;
    private String colctdate;
    private String cntlrMode;

    public String getCntlrId() {
        return cntlrId;
    }

    public void setCntlrId(String cntlrId) {
        this.cntlrId = cntlrId;
    }

    public String getPnlTp() {
        return pnlTp;
    }

    public void setPnlTp(String pnlTp) {
        this.pnlTp = pnlTp;
    }

    public String getPnlHd() {
        return pnlHd;
    }

    public void setPnlHd(String pnlHd) {
        this.pnlHd = pnlHd;
    }

    public String getColctdate() {
        return colctdate;
    }

    public void setColctdate(String colctdate) {
        this.colctdate = colctdate;
    }

    public String getCntlrMode() {
        return cntlrMode;
    }

    public void setCntlrMode(String cntlrMode) {
        this.cntlrMode = cntlrMode;
    }
}
```

## [출처 및 참고]
* <https://github.com/google/gson>
* <https://yookeun.github.io/java/2017/05/27/java-gson/>
