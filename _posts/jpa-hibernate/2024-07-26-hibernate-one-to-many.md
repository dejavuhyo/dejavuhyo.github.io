---
title: Hibernate One to Many
author: dejavuhyo
date: 2024-07-26 08:39:00 +0900
categories: [Framework, JPA/Hibernate]
tags: [hibernate-one-to-many, one-to-many, 하이버네이트-one-to-many]
---

## 1. 설명
일대다 매핑은 한 테이블의 한 행이 다른 테이블의 여러 행에 매핑된다는 것을 의미한다.

일대다 연관 관계를 확인하기 위해 다음의 엔티티-관계 다이어그램을 확인한다.

![one-to-many](/assets/img/2024-07-26-hibernate-one-to-many/one-to-many.png)

예제에서는 각 cart에 대한 테이블과 각 항목에 대한 또 다른 테이블이 있는 cart 시스템을 구현한다. 하나의 cart는 여러 항목을 가질 수 있으므로 여기서는 일대다 매핑이 ​​있다.

데이터베이스 수준에서 작동하는 방식은 cart 테이블에 기본 키로 cart_id를 두고, items에 외래 키로 cart_id를 두는 것이다.

코드에서는 `@OneToMany`를 사용하여 이를 구현한다.

데이터베이스의 관계를 반영하는 방식으로 Cart 클래스를 Item 객체 컬렉션에 매핑한다.

```java
public class Cart {

    //...     
 
    @OneToMany(mappedBy="cart")
    private Set<Item> items;
	
    //...
}
```

`@ManyToOne`을 사용하여 각 Item에 Cart에 대한 참조를 추가할 수도 있으며, 이를 양방향 관계로 만들 수 있다. 양방향은 carts에서 items에 액세스할 수 있고, items에서 carts에 액세스할 수 있다는 것을 의미한다.

mappedBy 속성은 Hibernate에 자식 클래스에서 부모 클래스를 나타내는데 어떤 변수를 사용할지 알려주는데 사용된다.

다음 기술과 라이브러리는 일대다 연관을 구현하는 샘플 Hibernate 애플리케이션을 개발하는데 사용된다.

* JDK 1.8 or later

* Hibernate 6

* Maven 3 or later

* H2 database

## 2. 설정

### 1) 데이터베이스 설정
도메인 모델에서 스키마를 관리하기 위해 Hibernate를 사용한다. 다시 말해, 엔터티 간의 다양한 테이블과 관계를 생성하기 위해 SQL 문을 제공할 필요가 없다.

### 2) Maven 종속성
`pom.xml` 파일에 Hibernate와 H2 드라이버 종속성을 추가한다. Hibernate 종속성은 JBoss 로깅을 사용하며, 자동으로 전이적 종속성으로 추가된다.

```xml
<dependency>
    <groupId>org.hibernate.orm</groupId>
    <artifactId>hibernate-core</artifactId>
    <version>6.4.2.Final</version>
</dependency>
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <version>2.2.224</version>
</dependency>
```

최신 버전의 [Hibernate](https://mvnrepository.com/artifact/org.hibernate/hibernate-core)와 [H2](https://mvnrepository.com/artifact/com.h2database/h2) 종속성은 Maven central repository에서 확인한다.

### 3) 하이버네이트 세션팩토리
데이터베이스 상호작용을 위한 Hibernate SessionFactory를 만든다.

```java
public static SessionFactory getSessionFactory() {

    ServiceRegistry serviceRegistry = new StandardServiceRegistryBuilder()
      .applySettings(dbSettings())
      .build();

    Metadata metadata = new MetadataSources(serviceRegistry)
      .addAnnotatedClass(Cart.class)
      // other domain classes
      .buildMetadata();

    return metadata.buildSessionFactory();
}

private static Map<String, Object> dbSettings() {
    // return Hibernate settings
}
```

## 3. 모델
매핑 관련 구성은 모델 클래스의 JPA 주석을 사용하여 수행된다.

```java
@Entity
@Table(name="CART")
public class Cart {

    //...

    @OneToMany(mappedBy="cart")
    private Set<Item> items;
	
    // getters and setters
}
```

`@OneToMany` 주석은 mappedBy 변수를 매핑하는데 사용될 Item 클래스의 속성을 정의하는데 사용된다. 그래서 Item 클래스에 "cart"라는 이름의 속성이 있는 것이다.

```java
@Entity
@Table(name="ITEMS")
public class Item {
    
    //...
    @ManyToOne
    @JoinColumn(name="cart_id", nullable=false)
    private Cart cart;

    public Item() {}
    
    // getters and setters
}
```

`@ManyToOne` 주석이 Cart 클래스 변수와 연결되어 있다. `@JoinColumn` 주석은 매핑된 열을 참조한다.

## 4. 액션
테스트 프로그램에서는 Hibernate Session을 얻고, 일대다 연관 관계를 구현하여 모델 객체를 데이터베이스에 저장하기 위한 `main()` 메서드가 있는 클래스를 생성한다.

```java
sessionFactory = HibernateAnnotationUtil.getSessionFactory();
session = sessionFactory.getCurrentSession();
LOGGER.info("Session created");

tx = session.beginTransaction();

session.save(cart);
session.save(item1);
session.save(item2);

tx.commit();
LOGGER.info("Cart ID={}", cart.getId());
LOGGER.info("item1 ID={}, Foreign Key Cart ID={}", item1.getId(), item1.getCart().getId());
LOGGER.info("item2 ID={}, Foreign Key Cart ID={}", item2.getId(), item2.getCart().getId());
```

이는 테스트 프로그램의 출력이다.

```text
Session created
Hibernate: insert into CART values ()
Hibernate: insert into ITEMS (cart_id)
  values (?)
Hibernate: insert into ITEMS (cart_id)
  values (?)
Cart ID=7
item1 ID=11, Foreign Key Cart ID=7
item2 ID=12, Foreign Key Cart ID=7
Closing SessionFactory
```

## 5. @ManyToOne 주석
`@ManyToOne` 주석을 사용하여 다대일 관계를 지정할 수 있다. 다대일 매핑은 이 엔터티의 여러 인스턴스가 다른 엔터티의 한 인스턴스에 매핑된다는 것을 의미한다. 즉, 한 카트에 여러 항목이 있는 것이다.

`@ManyToOne` 주석은 양방향 관계도 만들 수 있게 해준다.

### 1) 불일치 및 소유권
만약 Cart가 Item을 참조했지만, Item이 Cart를 참조하지 않았다면, 관계는 단방향일 것이다. 객체는 또한 자연스러운 일관성을 가질 것이다.

하지만 관계는 양방향이어서 일관성이 부족할 가능성이 있다.

개발자가 cart1 인스턴스에 item1을 추가하고 cart2 인스턴스에 item2를 추가하려는 상황을 가정한다. 하지만 실수로 cart2와 item2 간의 참조가 일관되지 않게 되었다고 가정한다.

```java
Cart cart1 = new Cart();
Cart cart2 = new Cart();

Item item1 = new Item(cart1);
Item item2 = new Item(cart2); 
Set<Item> itemsSet = new HashSet<Item>();
itemsSet.add(item1);
itemsSet.add(item2); 
cart1.setItems(itemsSet); // wrong!
```

위에 표시된 대로, item2는 cart2를 참조 하지만, cart2는 item2를 참조하지 않는다. 이는 좋지 않다.

Hibernate는 item2를 데이터베이스에 관계의 소유 측이라는 개념을 사용하여 이러한 모호성을 해결한다. 소유 측에 속하는 참조가 우선권을 갖고 데이터베이스에 저장된다.

### 2) 소유 측 item
JPA 사양의 섹션 2.9에 명시된 대로, 다대일 측을 소유 측으로 표시하는 것이 좋다.

즉, Item은 소유 측이고 Cart는 그 반대 측이 된다.

Cart 클래스에 mappedBy 속성을 포함시켜서 이를 반대 변으로 표시한다.

동시에 `@ManyToOne`으로 Item.cart 필드에 주석을 달아서 Item을 소유 측으로 만든다.

"불일치" 예로 돌아가면, Hibernate는 item2의 참조가 더 중요하다는 것을 알고 item2의 참조를 데이터베이스에 저장한다.

결과를 확인한다.

```text
item1 ID=1, Foreign Key Cart ID=1
item2 ID=2, Foreign Key Cart ID=2
```

cart가 item2를 참조 하지만, item2의 cart2 참조는 데이터베이스에 저장된다.

### 3) 소유 측 Cart
일대 다 측면을 소유 측면으로 표시하고, 다대일 측면을 반대 측면으로 표시하는 것도 가능하다.

권장하는 방식은 아니다.

아래 코드는 소유 측으로서 일대다 측의 구현 일부이다.

```java
public class ItemOIO {
    
    //  ...
    @ManyToOne
    @JoinColumn(name = "cart_id", insertable = false, updatable = false)
    private CartOIO cart;
    //..
}

public class CartOIO {
    
    //..  
    @OneToMany
    @JoinColumn(name = "cart_id") // we need to duplicate the physical information
    private Set<ItemOIO> items;
    //..
}
```

mappedBy 요소를 제거 하고 many-to-one `@JoinColumn`을 삽입 및 업데이트 가능하게 false로 설정한다.

같은 코드를 실행하면 결과는 반대가 된다.

```text
item1 ID=1, Foreign Key Cart ID=1
item2 ID=2, Foreign Key Cart ID=1
```

위에 표시된 대로 이제 item2가 장바구니에 속한다.

## [출처 및 참고]
* [https://www.baeldung.com/hibernate-one-to-many](https://www.baeldung.com/hibernate-one-to-many)
