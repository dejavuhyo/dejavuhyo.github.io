---
title: Java XML root 노드 추가
author: dejavuhyo
date: 2024-01-24 09:15:00 +0900
categories: [Language, Java]
tags: [java-xml, xml-node, xml-add-node, jackson-xml, 자바-xml, xml-노드, xml-노드-추가]
---

## 1. XML root 노드 추가
표준 XML은 root 태그가 있다. 그러나 XML 변환시 표준 XML 형식을 따르지 않는 경우가 있다.

XML을 더 읽기 쉽고 표준을 준수하도록 root 태그를 추가할 수 있다.

## 2. Jackson Dependency 추가

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-core</artifactId>
    <version>2.16.1</version>
</dependency>
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.16.1</version>
</dependency>
<dependency>
    <groupId>com.fasterxml.jackson.dataformat</groupId>
    <artifactId>jackson-dataformat-xml</artifactId>
    <version>2.16.1</version>
</dependency>
```

## 3. 예제

* XmlAddNode.java

```java
import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.SerializationFeature;
import com.fasterxml.jackson.dataformat.xml.XmlMapper;
import com.fasterxml.jackson.dataformat.xml.ser.ToXmlGenerator;
import lombok.extern.slf4j.Slf4j;

@Slf4j
public class XmlAddNode {
    public static void main(String[] args) throws JsonProcessingException {

        String xmlString = "<date>2024-01-15</date>\n" +
                "<sendOrgCd>F001</sendOrgCd>\n" +
                "<stat>01</stat>\n" +
                "<orgCd>N11</orgCd>\n" +
                "<carNum>123가4567</carNum>\n" +
                "<startDate>2024-01-01</startDate>\n" +
                "<endDate>9999-12-31</endDate>";

        XmlMapper xmlMapper = new XmlMapper();
        xmlMapper.configure(SerializationFeature.INDENT_OUTPUT, true);
        xmlMapper.configure(ToXmlGenerator.Feature.WRITE_XML_DECLARATION, true);
        xmlMapper.configure(ToXmlGenerator.Feature.WRITE_XML_1_1, true);

        String resultXml = xmlMapper.writer().withRootName("root").writeValueAsString(xmlString);
        log.info(resultXml);
    }
}
```

## [출처 및 참고]
* [https://www.baeldung.com/java-convert-json-to-xml](https://www.baeldung.com/java-convert-json-to-xml)
