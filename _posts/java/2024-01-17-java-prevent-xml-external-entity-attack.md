---
title: Java XML 외부 엔터티 공격(XXE attack) 방지 방법
author: dejavuhyo
date: 2024-01-17 15:30:00 +0900
categories: [Language, Java]
tags: [java-xml, external-entity-attack, xxe-attack, 외부-엔터티-공격]
---

## 1. XML 외부 엔터티 공격 (XXE attack)
XML 외부 엔터티 공격(XXE 공격 이라고도 함)은 외부 엔터티가 포함된 XML 데이터를 구문 분석하는 응용 프로그램에 대한 공격이다. XML 파서는 서버 파일 시스템이나 네트워크에서 외부 엔터티의 콘텐츠를 로드하므로 임의 파일 공개 또는 SSRF(서버 측 요청 위조) 취약점이 발생할 수 있다.

> 참고: [CWE-611](https://cwe.mitre.org/data/definitions/611.html)

## 2. XXE 공격 예시
예에서는 XXE 공격을 사용하여 서버에 있는 파일의 콘텐츠를 검색하는 방법이다.

애플리케이션은 아래 XML 파일을 구문 분석하고 직원 ID를 가져와 직원 이름을 표시한다.

* staff.xml

```xml
  <?xml version="1.0" encoding="utf-8"?>
  <company>
    <staffId>1001</staffId>
  </company>
```

* Output

```xml
<?xml version="1.0" encoding="utf-8"?>
  <company>
    <staffId>Yong Mook Kim</staffId>
  </company>
```

공격자는 들어오는 XML 파일의 내용을 조작하여 XML 데이터를 서버의 파일을 참조하는 악의적인 외부 엔터티 `/etc/passwd`로 대체한다.

* staff.xml

```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
  <company>
      <staffId>&xxe;</staffId>
  </company>
```

XML 파서가 위의 악성 외부 개체를 구문 분석할 때 해당 위치에 `/etc/passwdXML` 파일 내용이 포함된다. 약하게 구성된 XML 파서(일반적으로 기본 XML 파서)의 경우 `&xxe;`엔터티는 `/etc/passwd` 파일의 내용으로 대체된다.

* Output

```xml
<?xml version="1.0" encoding="utf-8"?>
  <company>
    <staffId>nobody:*:-2:-2:Unprivileged User:/var/empty:/usr/bin/false
    root:*:0:0:System Administrator:/var/root:/bin/sh
    daemon:*:1:1:System Services:/var/root:/usr/bin/false
    </staffId>
  </company>
```

XXE 공격에 취약한 SAX XML 파서이다.

* ReadXmlSaxParserXXE.java

```java
import com.mkyong.xml.sax.handler.PrintAllHandlerSax;
import org.xml.sax.SAXException;

import javax.xml.parsers.ParserConfigurationException;
import javax.xml.parsers.SAXParser;
import javax.xml.parsers.SAXParserFactory;
import java.io.IOException;

public class ReadXmlSaxParserXXE {

    private static final String FILENAME = "src/main/resources/staff.xml";

    public static void main(String[] args) {
        SAXParserFactory factory = SAXParserFactory.newInstance();
        try {
            // XXE attack
            SAXParser saxParser = factory.newSAXParser();
            PrintAllHandlerSax handler = new PrintAllHandlerSax();
            saxParser.parse(FILENAME, handler);
        } catch (ParserConfigurationException | SAXException | IOException e) {
            e.printStackTrace();
        }
    }
}
```

## 3. SAX 파서 XXE 공격 방지 방법
SAX 파서 setFeature를 사용하여 DOCTYPE 선언을 완전히 비활성화 할 수 있다.

```java
SAXParserFactory factory = SAXParserFactory.newInstance();

try {
    // https://rules.sonarsource.com/java/RSPEC-2755
    // prevent XXE, completely disable DOCTYPE declaration:
    factory.setFeature("http://apache.org/xml/features/disallow-doctype-decl", true);
    SAXParser saxParser = factory.newSAXParser();
    PrintAllHandlerSax handler = new PrintAllHandlerSax();
    saxParser.parse(FILENAME, handler);
} catch (ParserConfigurationException | SAXException | IOException e) {
    e.printStackTrace();
}
```

SAX 파서가 외부 엔터티를 구문 분석하면 오류 메시지가 표시된다.

* Output

```text
org.xml.sax.SAXParseException; staffId: file:///Users/yongmookkim/projects/core-java/java-xml/src/main/resources/staff-xxe.xml;
 lineNumber: 2; columnNumber: 10; DOCTYPE is disallowed
 when the feature "http://apache.org/xml/features/disallow-doctype-decl" set to true.
```

## 4. XML 파서 XXE 공격 방지를 위한 Sonar Rule
Sonar Rule에서 널리 사용되는 XML 파서 목록에 대한 완전한 솔루션을 위해서는 [XML 파서가 XXE 공격에 취약해서는 안된다.](https://rules.sonarsource.com/java/RSPEC-2755/)

* [DOM 파서](https://docs.oracle.com/en/java/javase/17/docs/api/java.xml/javax/xml/parsers/DocumentBuilderFactory.html)의 경우

```java
DocumentBuilderFactory factory = DocumentBuilderFactory.newInstance();
// to be compliant, completely disable DOCTYPE declaration:
factory.setFeature("http://apache.org/xml/features/disallow-doctype-decl", true);
// or completely disable external entities declarations:
factory.setFeature("http://xml.org/sax/features/external-general-entities", false);
factory.setFeature("http://xml.org/sax/features/external-parameter-entities", false);
// or prohibit the use of all protocols by external entities:
factory.setAttribute(XMLConstants.ACCESS_EXTERNAL_DTD, "");
factory.setAttribute(XMLConstants.ACCESS_EXTERNAL_SCHEMA, "");
// or disable entity expansion but keep in mind that this doesn't prevent fetching external entities
// and this solution is not correct for OpenJDK < 13 due to a bug: https://bugs.openjdk.java.net/browse/JDK-8206132
factory.setExpandEntityReferences(false);
```

* [SAX 파서](https://docs.oracle.com/en/java/javase/17/docs/api/java.xml/javax/xml/parsers/SAXParserFactory.html)의 경우

```java
SAXParserFactory factory = SAXParserFactory.newInstance();
// to be compliant, completely disable DOCTYPE declaration:
factory.setFeature("http://apache.org/xml/features/disallow-doctype-decl", true);
// or completely disable external entities declarations:
factory.setFeature("http://xml.org/sax/features/external-general-entities", false);
factory.setFeature("http://xml.org/sax/features/external-parameter-entities", false);
// or prohibit the use of all protocols by external entities:
SAXParser parser = factory.newSAXParser(); // Noncompliant
parser.setProperty(XMLConstants.ACCESS_EXTERNAL_DTD, "");
parser.setProperty(XMLConstants.ACCESS_EXTERNAL_SCHEMA, "");
```

* [XMLInput](https://docs.oracle.com/en/java/javase/17/docs/api/java.xml/javax/xml/stream/XMLInputFactory.html), [Transformer](https://docs.oracle.com/en/java/javase/17/docs/api/java.xml/javax/xml/transform/TransformerFactory.html), [Schema](https://docs.oracle.com/en/java/javase/17/docs/api/java.xml/javax/xml/validation/SchemaFactory.html)

```java
XMLInputFactory factory = XMLInputFactory.newInstance();
// to be compliant, completely disable DOCTYPE declaration:
factory.setProperty(XMLInputFactory.SUPPORT_DTD, false);
// or completely disable external entities declarations:
factory.setProperty(XMLInputFactory.IS_SUPPORTING_EXTERNAL_ENTITIES, Boolean.FALSE);
// or prohibit the use of all protocols by external entities:
factory.setProperty(XMLConstants.ACCESS_EXTERNAL_DTD, "");
factory.setProperty(XMLConstants.ACCESS_EXTERNAL_SCHEMA, "");

TransformerFactory factory = javax.xml.transform.TransformerFactory.newInstance();
// to be compliant, prohibit the use of all protocols by external entities:
factory.setAttribute(XMLConstants.ACCESS_EXTERNAL_DTD, "");
factory.setAttribute(XMLConstants.ACCESS_EXTERNAL_STYLESHEET, "");

SchemaFactory factory = SchemaFactory.newInstance(XMLConstants.W3C_XML_SCHEMA_NS_URI);
// to be compliant, completely disable DOCTYPE declaration:
factory.setFeature("http://apache.org/xml/features/disallow-doctype-decl", true);
// or prohibit the use of all protocols by external entities:
factory.setProperty(XMLConstants.ACCESS_EXTERNAL_DTD, "");
factory.setProperty(XMLConstants.ACCESS_EXTERNAL_SCHEMA, "");
```

* [Dom4j 라이브러리](https://dom4j.github.io/)의 경우

```java
SAXReader xmlReader = new SAXReader();
xmlReader.setFeature("http://apache.org/xml/features/disallow-doctype-decl", true);
```

* [JDOM 라이브러리](http://www.jdom.org/)의 경우

```java
SAXBuilder builder = new SAXBuilder();
builder.setProperty(XMLConstants.ACCESS_EXTERNAL_DTD, "");
builder.setProperty(XMLConstants.ACCESS_EXTERNAL_SCHEMA, "");
```

## [출처 및 참고]
* [https://mkyong.com/java/how-to-prevent-xml-external-entity-attack-xxe-attack/](https://mkyong.com/java/how-to-prevent-xml-external-entity-attack-xxe-attack/)
