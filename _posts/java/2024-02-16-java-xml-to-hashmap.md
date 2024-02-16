---
title: Java XML을 HashMap으로 파싱
author: dejavuhyo
date: 2024-02-16 13:06:00 +0900
categories: [Language, Java]
tags: [java-xml, xml-hashmap, xml-parsing, xml-파싱]
---

## 1. 설정
다양한 라이브러리를 사용하여 다음 XML을 HashMap으로 구문 분석한다.

```xml
<employees>
    <employee>
        <id>654</id>
        <firstName>John</firstName>
        <lastName>Doe</lastName>
    </employee>
    <employee>
        <id>776</id>
        <firstName>Steve</firstName>
        <lastName>Smith</lastName>
    </employee>
</employees>
```

아래 POJO를 사용하여 XML 데이터를 저장한다.

```java
public class Employee {
    private String id;
    private String firstName;
    private String lastName;

    // standard getters and setters
}
```

모든 경우에 대한 결과를 검증하기 위해 공통 테스트 방법을 설정한다.

```java
void verify(Map<String, Employee> employeeMap) {
    Employee employee1 = employeeMap.get("654");
    Employee employee2 = employeeMap.get("776");
    Assertions.assertEquals("John", employee1.getFirstName());
    Assertions.assertEquals("Doe", employee1.getLastName());
    Assertions.assertEquals("Steve", employee2.getFirstName());
    Assertions.assertEquals("Smith", employee2.getLastName());
}
```

## 2. XStream을 사용하여 XML 구문 분석
[XStream](https://www.baeldung.com/xstream-serialize-object-to-xml)은 XML과 개체를 직렬화 및 역직렬화하는 타사 라이브러리이다. 최소한의 구성으로 XStream은 XML 데이터를 구문 분석하는 기능을 제공한다.

아래 [Maven 종속성](https://mvnrepository.com/artifact/com.thoughtworks.xstream/xstream)을 사용한다.

```xml
<dependency>
    <groupId>com.thoughtworks.xstream</groupId>
    <artifactId>xstream</artifactId>
    <version>1.4.18</version>
</dependency>
```

XStream의 새 인스턴스를 생성 하고 몇 가지 별칭을 설정한다.

```java
XStream xStream=new XStream(); 
xStream.alias("employees", List.class); 
xStream.alias("employee", Employee.class);
```

List로 해석되도록 XML의 직원 요소에 대한 별칭을 설정했다. 또한 직원 개체로 해석되도록 직원 요소에 대한 별칭을 설정했다.

XML을 객체 목록으로 역직렬화하기 위해 XStream에 필요한 모든 유형의 역마샬링을 허용하는 권한을 추가한다.

```java
xStream.addPermission(AnyTypePermission.ANY);
```

XStream의 `fromXML()` 메소드를 사용하여 XML 문자열을 Employee 객체 목록으로 구문 분석한다.

```java
List<Employee> employees = (List<Employee>) xStream.fromXML(xml);
```

그런 다음 스트림을 사용하여 id를 키로 사용하고 직원 개체 자체를 값으로 사용하여 직원 목록을 Map으로 변환한다.

```java
employees.stream().collect(Collectors.toMap(Employee::getId, Function.identity()))
```

## 3. Underscore-java를 사용하여 XML 구문 분석
[Underscore-java](https://javadev.github.io/underscore-java/)는 광범위한 기능적 프로그래밍 및 데이터 조작 기능을 제공하는 유틸리티 라이브러리이다. Java 11 이상이 필요하다.

아래 [Maven 종속성](https://mvnrepository.com/artifact/com.github.javadev/underscore)을 사용한다.

```xml
<dependency>
    <groupId>com.github.javadev</groupId>
    <artifactId>underscore</artifactId>
    <version>1.89</version>
</dependency>
```

Underscore-java의 `fromXmlMap()` 함수를 사용하여 XML 문자열을 구문 분석하고 이를 중첩된 맵 구조로 변환한다.

```java
Map<String, Object> employeeList = (Map<String, Object>)U.fromXmlMap(xml).get("employees");
List<LinkedHashMap<String, String>> list=(List<LinkedHashMap<String,String>>)employeeList.get("employee");
parseXmlToMap(employeeMap, list);
```

결과 맵에서 직원 요소를 추출한다. 그런 다음 결과 LinkedHashMap을 HashMap으로 변환한다.

```java
void parseXmlToMap(Map<String, Employee> employeeMap, List<LinkedHashMap<String, String>> list) {
    list.forEach(empMap -> {
        Employee employee = new Employee();
        for (Map.Entry<String, String> key : empMap.entrySet()) {
            switch (key.getKey()) {
            case "id":
                employee.setId(key.getValue());
                break;
            case "firstName":
                employee.setFirstName(key.getValue());
                break;
            case "lastName":
                employee.setLastName(key.getValue());
                break;
            default:
                break;
            }
        }
        employeeMap.put(employee.getId(), employee);
    });
}
```

중첩된 맵 구조가 있으면 목록의 각 LinkedHashMap을 반복하여 개별 직원의 데이터를 나타낸다. 그런 다음 새 Employee 개체를 만들고 맵의 데이터를 기반으로 해당 필드를 채운다.

## 4. Jackson을 사용하여 XML 구문 분석
[Jackson](https://www.baeldung.com/jackson)은 주석이나 사용자 정의 가능한 구성을 사용하여 XML 요소와 속성을 Java 객체에 원활하게 매핑하는 Java 라이브러리이다.

다음 Maven 종속성을 사용한다.

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
</dependency>
<dependency>
    <groupId>com.fasterxml.jackson.dataformat</groupId>
    <artifactId>jackson-dataformat-xml</artifactId>
</dependency>
```

XmlMapper는 XML 데이터를 위한 특수 매퍼로, 이를 통해 XML을 읽고 쓸 수 있다.

```java
XmlMapper xmlMapper = new XmlMapper();
Map<String, Object> map= xmlMapper.readValue(xml, Map.class);
```

XML 데이터를 읽고 이를 key-value 쌍의 맵으로 변환한다. Jackson은 XML을 동적으로 구문 분석하고 해당 맵 구조를 구축한다. 맵에서 직원 요소 목록을 추출한다.

```java
List<LinkedHashMap<String, String>> list= (List<LinkedHashMap<String, String>>) map.get("employee");
```

그런 다음 앞서 정의한 것과 동일한 `parseXmlToMap()` 메서드를 사용하여 직원 맵을 추출할 수 있다.

## 5. JAXB를 사용하여 XML 구문 분석
[JAXB](https://www.baeldung.com/jaxb)는 XML 바인딩을 위한 Java 아키텍처이며 주석을 사용하여 XML 요소와 속성을 Java에 매핑하는 바인딩 프레임워크를 지원한다.

다음 [Maven 종속성](https://mvnrepository.com/artifact/com.sun.xml.bind/jaxb-impl)을 사용한다.

```xml
<dependency>
    <groupId>com.sun.xml.bind</groupId>
    <artifactId>jaxb-impl</artifactId>
    <version>2.3.3</version>
</dependency>
```

Java 객체에 바인딩하는데 도움이 되도록 다음 주석을 사용하여 Employees 클래스를 설정한다.

```java
@XmlRootElement(name = "employees")
public class Employees {

    private List<Employee> employeeList;

    @XmlElement(name = "employee")
    public List<Employee> getEmployeeList() {
        return employeeList;
    }
    // standard setter
}
```

XML 데이터와 Java 객체 간의 바인딩을 관리하는데 사용되는 JAXBContext를 생성한다.

```java
JAXBContext context = JAXBContext.newInstance(Employees.class); 
Unmarshaller unmarshaller = context.createUnmarshaller(); 
Employees employees = (Employees) unmarshaller.unmarshal(new StringReader(xmlData));
```

Unmarshaller는 클래스의 JAXB 주석에 의해 정의된 매핑을 기반으로 XML 데이터를 객체로 변환하는 일을 담당한다.

Java Streams를 사용하여 ID를 키로 사용하고 직원 개체 자체를 값으로 사용하여 직원 목록을 맵으로 변환한다.

## 6. DOM 구문 분석기와 XPath를 사용하여 XML 구문 분석
[DOM Parser](https://www.baeldung.com/java-xpath)는 타사 라이브러리 없이 XML을 구문 분석하는 방법이다. DOM Parser는 XML을 탐색하고 데이터를 추출하기 위해 XPath를 지원한다.

XML 문서를 구문 분석하는데 사용되는 DOM 구문 분석기를 생성하기 위한 팩토리를 만든다.

```java
DocumentBuilderFactory factory = DocumentBuilderFactory.newInstance();
DocumentBuilder builder = factory.newDocumentBuilder();
Document doc = builder.parse(new InputSource(new StringReader(xmlData)));
```

XML의 DOM 표현을 작성하는 빌더를 사용하여 XML 데이터를 Document 객체로 구문 분석한다.

그런 다음 DOM을 쿼리하기 위해 XPath 인스턴스를 설정한다.

```java
XPathFactory xPathfactory = XPathFactory.newInstance(); 
XPath xpath = xPathfactory.newXPath(); 
XPathExpression xPathExpr = xpath.compile("/employees/employee");
```

XML 문서의 직원 요소 내에 있는 모든 직원 요소를 선택하는 XPath 표현식을 컴파일하는 XPath 인스턴스를 구성한다.

일치하는 모든 직원 요소가 포함된 NodeList를 검색하기 위해 doc에서 XPath 표현식을 평가한다.

```java
NodeList nodes = (NodeList) xPathExpr.evaluate(doc, XPathConstants.NODESET);
```

NodeList를 반복하고 직원 요소를 HashMap으로 추출한다.

```java
for (int i = 0; i < nodes.getLength(); i++) {
    Element node = (Element) nodes.item(i);
    Employee employee = new Employee();
    employee.setId(node.getElementsByTagName("id").item(0).getTextContent());
    employee.setFirstName(node.getElementsByTagName("firstName").item(0).getTextContent());
    employee.setLastName(node.getElementsByTagName("lastName").item(0).getTextContent());
    map.put(employee.getId(), employee);
}
```

## [출처 및 참고]
* [https://www.baeldung.com/java-xml-read-into-hashmap](https://www.baeldung.com/java-xml-read-into-hashmap)
