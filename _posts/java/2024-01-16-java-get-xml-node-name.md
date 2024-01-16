---
title: Java XML 노드명 전체 가져오기
author: dejavuhyo
date: 2024-01-16 14:20:00 +0900
categories: [Language, Java]
tags: [java-xml, xml-parsing, xml-node-name, xml-파싱, xml-노드명, 노드명-가져오기]
---

## 1. 노드 접근
XML DOM을 이용하면 노드 트리에 포함된 모든 노드에 접근할 수 있다. 노드 간의 관계는 다음과 같은 속성들로 정의된다.

* `parentNode`: 부모 노드

* `childNodes`: 자식 노드 리스트

* `firstChild`: 첫 번째 자식 노드

* `lastChild`: 마지막 자식 노드

* `nextSibling`: 다음 형제 노드

* `previousSibling`: 이전 형제 노드

## 2. 노드명 전체 가져오기
XML을 파싱하면 주로 노드값을 가져온다. 그러나 노드명을 가져오는 경우도 있다.

아래는 노드명 전체를 가져오는 예제이다.

```java
import org.w3c.dom.Document;
import org.w3c.dom.Element;
import org.w3c.dom.Node;
import org.w3c.dom.NodeList;
import org.xml.sax.SAXException;

import javax.xml.parsers.DocumentBuilder;
import javax.xml.parsers.DocumentBuilderFactory;
import javax.xml.parsers.ParserConfigurationException;
import java.io.ByteArrayInputStream;
import java.io.IOException;
import java.io.InputStream;

public class GetNodeName {

    public static void main(String[] args) throws ParserConfigurationException, IOException, SAXException {

        String sampleXML = "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n" +
                "<request>\n" +
                "    <header comment=\"헤더\">\n" +
                "        <cd comment=\"코드\"/>\n" +
                "        <org_cd comment=\"기관 코드\"/>\n" +
                "        <trns_org_cd comment=\"송신 기관 코드\"/>\n" +
                "        <rc_org_cd comment=\"수신 기관 구분코드\"/>\n" +
                "        <resp_cd comment=\"응답 코드\"/>\n" +
                "        <resp_msg comment=\"응답메시지\"/>\n" +
                "    </header>\n" +
                "    <body comment=\"바디\">\n" +
                "        <bank_cd comment=\"은행코드\"/>\n" +
                "        <acc_no comment=\"계좌번호\"/>\n" +
                "        <nme comment=\"성명\"/>\n" +
                "        <mact_bal comment=\"계좌잔액\"/>\n" +
                "        <cmsn comment=\"수수료\"/>\n" +
                "        <pyr_no comment=\"납부자번호\"/>\n" +
                "    </body>\n" +
                "</request>";

        InputStream is = new ByteArrayInputStream(xml.getBytes(StandardCharsets.UTF_8));
        DocumentBuilderFactory factory = DocumentBuilderFactory.newInstance();
        factory.setFeature("http://apache.org/xml/features/disallow-doctype-decl", true);
        factory.setFeature("http://xml.org/sax/features/external-general-entities", false);
        factory.setFeature("http://xml.org/sax/features/external-parameter-entities", false);
        factory.setAttribute(XMLConstants.ACCESS_EXTERNAL_DTD, "");
        factory.setAttribute(XMLConstants.ACCESS_EXTERNAL_SCHEMA, "");
        factory.setExpandEntityReferences(false);
        DocumentBuilder build = factory.newDocumentBuilder();
        Document document = build.parse(is);

        Element root = document.getDocumentElement(); // root 요소 가져오기
        NodeList firstNodeList = root.getChildNodes();

        IntStream.range(0, firstNodeList.getLength()).boxed().forEach(i -> {
            Node firstNode = firstNodeList.item(i);
            NodeList childNodeList = firstNode.getChildNodes();

            IntStream.range(0, childNodeList.getLength()).boxed().forEach(j -> {
                Node childNode = childNodeList.item(j);
                if (childNode.getNodeType() == Node.ELEMENT_NODE) { // 노드의 타입이 Element일 경우(공백이 아닌 경우)
                    resultList.add(childNode.getNodeName());
                }
            });
        });
    }
}
```

## [출처 및 참고]
* [https://reinvestment.tistory.com/53](https://reinvestment.tistory.com/53)
* [https://tcpschool.com/xml/xml_dom_nodeAccess](https://tcpschool.com/xml/xml_dom_nodeAccess)
