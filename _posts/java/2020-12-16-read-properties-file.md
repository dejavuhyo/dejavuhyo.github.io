--- 
title: Properties 파일 읽기
author: Hyosik
date: 2020-12-16 09:30:00 +0900
categories: [Language, Java]
tags: [properties, read-properties, properties-file, properties-파일-읽기, 파일-읽기]
---

## 1. Properties 파일

```properties
spring.datasource.driver-class-name=org.postgresql.Driver
spring.datasource.jdbc-url=jdbc:postgresql:/localhost:5432/postgres
spring.datasource.username=postgres
spring.datasource.password=postgres
```

## 2. Key를 이용하여 읽기

```java
import java.io.BufferedInputStream;
import java.io.FileInputStream;
import java.util.Properties;

public class PropertiesValue {

    public static void main(String[] args) {

        String keyName = "spring.datasource.driver-class-name";

        PropertiesValue getPropVal = new PropertiesValue();
        System.out.println(getPropVal.getPropVal(keyName));
    }

    public String getPropVal(String keyName) {

        String value = "";

        try {
            Properties prop = new Properties();
            FileInputStream fis = new FileInputStream("./config/setting.properties");
            prop.load(new BufferedInputStream(fis));
            value = prop.getProperty(keyName).trim();
            fis.close();

        } catch (Exception e) {
            e.printStackTrace();
        }
        return value;
    }
}
```
