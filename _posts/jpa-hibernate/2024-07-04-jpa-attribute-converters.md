---
title: JPA 속성 변환기
author: dejavuhyo
date: 2024-07-04 10:35:00 +0900
categories: [Framework, JPA/Hibernate]
tags: [jpa-converter, attribute-converters, 속성-변환기]
---

## 1. 변환기 생성
사용자 정의 Java 클래스에 대한 속성 변환기를 구현하는 방법이다.

먼저, 나중에 변환될 PersonName 클래스를 만든다.

```java
public class PersonName implements Serializable {

    private String name;
    private String surname;

    // getters and setters
}
```

그런 다음 `@Entity` 클래스에 PersonName 유형의 속성을 추가한다.

```java
@Entity(name = "PersonTable")
public class Person {
   
    private PersonName personName;

    //...
}
```

PersonName 속성을 데이터베이스 열로 변환하고 그 반대로 변환하는 변환기를 만든다. 속성을 이름과 성 필드가 모두 포함된 문자열 값으로 변환한다.

그렇게 하려면 `@Converter`로 변환기 클래스에 주석을 달고 AttributeConverter 인터페이스를 구현 해야 한다. 클래스 유형과 데이터베이스 열의 순서로 인터페이스를 매개변수화한다.

```java
@Converter
public class PersonNameConverter implements 
  AttributeConverter<PersonName, String> {

    private static final String SEPARATOR = ", ";

    @Override
    public String convertToDatabaseColumn(PersonName personName) {
        if (personName == null) {
            return null;
        }

        StringBuilder sb = new StringBuilder();
        if (personName.getSurname() != null && !personName.getSurname()
            .isEmpty()) {
            sb.append(personName.getSurname());
            sb.append(SEPARATOR);
        }

        if (personName.getName() != null 
          && !personName.getName().isEmpty()) {
            sb.append(personName.getName());
        }

        return sb.toString();
    }

    @Override
    public PersonName convertToEntityAttribute(String dbPersonName) {
        if (dbPersonName == null || dbPersonName.isEmpty()) {
            return null;
        }

        String[] pieces = dbPersonName.split(SEPARATOR);

        if (pieces == null || pieces.length == 0) {
            return null;
        }

        PersonName personName = new PersonName();        
        String firstPiece = !pieces[0].isEmpty() ? pieces[0] : null;
        if (dbPersonName.contains(SEPARATOR)) {
            personName.setSurname(firstPiece);

            if (pieces.length >= 2 && pieces[1] != null 
              && !pieces[1].isEmpty()) {
                personName.setName(pieces[1]);
            }
        } else {
            personName.setName(firstPiece);
        }

        return personName;
    }
}
```

`convertToDatabaseColumn()`과 `convertToEntityAttribute()`라는 2개의 메서드를 구현한다.

두 가지 방법은 속성을 데이터베이스 열로 변환하는데 사용되며, 그 반대의 경우도 마찬가지이다.

## 2. 변환기 사용
변환기를 사용하려면 속성에 `@Convert` 주석을 추가 하고 사용하려는 변환기 클래스를 지정하기만 하면 된다.

```java
@Entity(name = "PersonTable")
public class Person {

    @Convert(converter = PersonNameConverter.class)
    private PersonName personName;
    
    // ...
}
```

마지막으로, 이것이 실제로 작동하는지 확인하기 위해 단위 테스트를 만든다.

먼저 데이터베이스에 Person 객체를 저장한다.

```java
@Test
public void givenPersonName_whenSaving_thenNameAndSurnameConcat() {
    String name = "name";
    String surname = "surname";

    PersonName personName = new PersonName();
    personName.setName(name);
    personName.setSurname(surname);

    Person person = new Person();
    person.setPersonName(personName);

    Long id = (Long) session.save(person);

    session.flush();
    session.clear();
}
```

데이터베이스 테이블에서 해당 필드를 검색하여 변환기에서 정의한 대로 PersonName이 저장되었는지 테스트한다.

```java
@Test
public void givenPersonName_whenSaving_thenNameAndSurnameConcat() {
    // ...

    String dbPersonName = (String) session.createNativeQuery(
      "select p.personName from PersonTable p where p.id = :id")
      .setParameter("id", id)
      .getSingleResult();

    assertEquals(surname + ", " + name, dbPersonName);
}
```

데이터베이스에 저장된 값에서 PersonName 클래스로의 변환이 변환기에 정의된 대로 작동하는지 테스트한다. 이를 위해 Person 클래스 전체를 검색하는 쿼리를 작성한다.

```java
@Test
public void givenPersonName_whenSaving_thenNameAndSurnameConcat() {
    // ...

    Person dbPerson = session.createNativeQuery(
      "select * from PersonTable p where p.id = :id", Person.class)
        .setParameter("id", id)
        .getSingleResult();

    assertEquals(dbPerson.getPersonName()
      .getName(), name);
    assertEquals(dbPerson.getPersonName()
      .getSurname(), surname);
}
```

## [출처 및 참고]
* [https://www.baeldung.com/jpa-attribute-converters](https://www.baeldung.com/jpa-attribute-converters)
