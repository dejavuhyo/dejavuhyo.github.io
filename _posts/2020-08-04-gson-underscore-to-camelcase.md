---
title: Gson 파싱시 Underscore를 CamelCase로 변환
author: Hyosik
date: 2020-08-04
categories: [Language, Java]
tags: [gson_underscore_to_camelcase, gson_camelcase, gson_underscore, json_object, json_parsing, gson_parsing, json_파싱, gson_파싱]
---

## 1. Underscore를 CamelCase로 변환하여 Json 객체로 변환

* JsonObject.java

```java
import com.google.gson.FieldNamingPolicy;
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import pbb.ga.subscriber.service.JsonVO;

public class JsonObject {

    public static void main(String[] args) {
        JsonObject gson = new JsonObject();
        gson.parsing("{\"cntlr_id\":\"3001\",\"pnl_tp\":\"20.99552\",\"pnl_hd\":\"52.51834\"" +
                ",\"colctdate\":\"2020-06-30 00:00:00\",\"cntlr_mode\":\"Manual\"}");
    }

    private void parsing(String jsonData) {
        Gson gson = new GsonBuilder().setFieldNamingPolicy(FieldNamingPolicy.LOWER_CASE_WITH_UNDERSCORES).create();
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

## 출처 및 참고
* <https://stackoverflow.com/questions/2370745/convert-json-style-properties-names-to-java-camelcase-names-with-gson>
