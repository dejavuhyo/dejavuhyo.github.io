---
title: DAO와 Repository Patterns의 차이점
author: dejavuhyo
date: 2022-10-17 11:25:00 +0900
categories: [Application, Architecture]
tags: [dao-repository-patterns, dao-vs-repository, difference-dao-repository, dao-repository-차이점, dao-패턴, repository-패턴]
---

## 1. DAO 패턴
DAO 패턴이라고 하는 데이터 액세스 개체 패턴은 데이터 지속성을 추상화한 것으로, 종종 테이블 중심인 기본 스토리지에 더 가깝다고 간주된다.

따라서 많은 경우 DAO가 데이터베이스 테이블과 일치하므로 스토리지에서 데이터를 더욱 쉽게 전송/검색할 수 있으므로 추한 쿼리를 숨길 수 있다.

DAO 패턴의 간단한 구현이다.

### 1) User
먼저 기본 사용자 도메인 클래스를 생성한다.

```java
public class User {
    private Long id;
    private String userName;
    private String firstName;
    private String email;

    // getters and setters
}
```

### 2) UserDao
그런 다음 사용자 도메인에 대한 간단한 CRUD 작업을 제공하는 UserDao 인터페이스를 생성한다.

```java
public interface UserDao {
    void create(User user);
    User read(Long id);
    void update(User user);
    void delete(String userName);
}
```

### 3) UserDaoImpl
마지막으로 UserDao 인터페이스를 구현하는 UserDaoImpl 클래스를 생성한다.

```java
public class UserDaoImpl implements UserDao {
    private final EntityManager entityManager;
    
    @Override
    public void create(User user) {
        entityManager.persist(user);
    }

    @Override
    public User read(long id) {
        return entityManager.find(User.class, id);
    }

    // ...
}
```

여기서는 단순화를 위해 JPA Entity Manager 인터페이스를 사용하여 기본 스토리지와 상호 작용하고 사용자 도메인에 대한 데이터 액세스 메커니즘을 제공했다.

## 2. Repository Pattern
Eric Evans의 저서 Domain-Driven Design에 따르면, "저장소는 개체 컬렉션을 에뮬레이트하는 저장, 검색 및 검색 동작을 캡슐화하기 위한 메커니즘이다."

마찬가지로, Patterns of Enterprise Application Architecture에 따르면, "도메인 개체에 액세스하기 위해 컬렉션과 같은 인터페이스를 사용하여 도메인과 데이터 매핑 레이어 사이를 중재한다."


즉, 저장소도 DAO와 유사한 데이터를 처리하고 쿼리를 숨긴다. 그러나 앱의 비즈니스 로직에 더 가까운 더 높은 수준에 있다.

결과적으로 리포지토리는 DAO를 사용하여 데이터베이스에서 데이터를 가져오고 도메인 개체를 채울 수 있다. 또는 도메인 개체에서 데이터를 준비하고 지속성을 위해 DAO를 사용하여 스토리지 시스템으로 보낼 수 있다.

사용자 도메인에 대한 저장소 패턴의 간단한 구현이다.

### 1) UserRepository
먼저 UserRepository 인터페이스를 생성한다.

```java
public interface UserRepository {
    User get(Long id);
    void add(User user);
    void update(User user);
    void remove(User user);
}
```

여기에 get, add, update 및 remove와 같은 몇 가지 일반적인 메서드를 추가하여 개체 컬렉션과 함께 작동한다.

### 2) UserRepositoryImpl
그런 다음 UserRepositoryImpl 클래스를 만들어 UserRepository 인터페이스를 구현한다.

```java
public class UserRepositoryImpl implements UserRepository {
    private UserDaoImpl userDaoImpl;
    
    @Override
    public User get(Long id) {
        User user = userDaoImpl.read(id);
        return user;
    }

    @Override
    public void add(User user) {
        userDaoImpl.create(user);
    }

    // ...
}
```

여기서는 UserDaoImpl을 사용하여 데이터베이스에서 데이터를 전송/검색했다.

지금까지는 User 클래스가 빈약한 도메인이기 때문에 DAO와 저장소의 구현이 매우 유사해 보인다고 말할 수 있다. 그리고 리포지토리는 DAO(Data Access Layer) 위의 또 다른 계층일 뿐이다.

그러나 DAO는 데이터에 액세스하기에 완벽한 후보로 보이며 저장소는 비즈니스 사용 사례를 구현하는 이상적인 방법이다.

## 3. 다중 DAO가 있는 Repository Pattern
마지막 문장을 명확하게 이해하기 위해 비즈니스 사용 사례를 처리하도록 사용자 도메인을 개선해 본다.

Twitter 트윗, Facebook 게시물 등을 집계하여 사용자의 소셜 미디어 프로필을 준비한다고 상상해보십시오.

사용자의 트위터, 페이스북 게시물 등을 종합하여 사용자의 소셜 미디어 프로필을 준비한다고 상상해 보십시오.

### 1) Tweet
먼저 트윗 정보를 저장하는 몇 가지 속성을 가진 트윗 클래스를 생성한다.

```java
public class Tweet {
    private String email;
    private String tweetText;
    private Date dateCreated;

    // getters and setters
}
```

### 2) TweetDao and TweetDaoImpl
그런 다음 UserDao와 유사하게 트윗을 가져올 수 있는 TweetDao 인터페이스를 생성한다.

```java
public interface TweetDao {
    List<Tweet> fetchTweets(String email);
}
```

마찬가지로 fetchTweets 메서드의 구현을 제공하는 TweetDaoImpl 클래스를 생성한다.

```java
public class TweetDaoImpl implements TweetDao {
    @Override
    public List<Tweet> fetchTweets(String email) {
        List<Tweet> tweets = new ArrayList<Tweet>();
        
        //call Twitter API and prepare Tweet object
        
        return tweets;
    }
}
```

여기에서 Twitter API를 호출하여 이메일을 사용하는 사용자의 모든 트윗을 가져온다.

따라서 이 경우 DAO는 타사 API를 사용하여 데이터 액세스 메커니즘을 제공한다.

### 3) Enhance User Domain
마지막으로 트윗 객체 목록을 유지하기 위해 User 클래스의 UserSocialMedia 하위 클래스를 생성한다.

```java
public class UserSocialMedia extends User {
    private List<Tweet> tweets;

    // getters and setters
}
```

여기서 UserSocialMedia 클래스는 User 도메인의 속성도 포함하는 복잡한 도메인이다.

### 4) UserRepositoryImpl
이제 트윗 목록과 함께 사용자 도메인 개체를 제공하도록 UserRepositoryImpl 클래스를 업그레이드한다.

```java
public class UserRepositoryImpl implements UserRepository {
    private UserDaoImpl userDaoImpl;
    private TweetDaoImpl tweetDaoImpl;
    
    @Override
    public User get(Long id) {
        UserSocialMedia user = (UserSocialMedia) userDaoImpl.read(id);

        List<Tweet> tweets = tweetDaoImpl.fetchTweets(user.getEmail());
        user.setTweets(tweets);

        return user;
    }
}
```

여기서 UserRepositoryImpl은 UserDaoImpl을 사용하여 사용자 데이터를 추출하고 TweetDaoImpl을 사용하여 사용자의 트윗을 추출한다.

그런 다음 두 정보 집합을 집계하고 비즈니스 사용 사례에 편리한 UserSocialMedia 클래스의 도메인 개체를 제공한다. 따라서 리포지토리는 다양한 소스의 데이터에 액세스하기 위해 DAO에 의존한다.

마찬가지로 Facebook 게시물 목록을 유지하도록 사용자 도메인을 향상할 수 있다.

## 4. 두 패턴 비교
이제 DAO 및 Repository 패턴의 뉘앙스를 보았으므로 차이점을 요약한다.

* DAO는 데이터 지속성의 추상화이다. 그러나 repository는 개체 모음의 추상화이다.

* DAO는 스토리지 시스템에 더 가까운 하위 수준 개념이다. 그러나 Repository는 더 높은 수준의 개념으로 Domain 객체에 더 가깝다.

* DAO는 데이터 매핑/접근 계층으로 작동하여 못생긴 쿼리를 숨긴다. 그러나 Repository는 도메인과 데이터 액세스 레이어 사이의 레이어로, 데이터를 대조하고 도메인 객체를 준비하는 복잡성을 숨긴다.

* DAO는 저장소를 사용하여 구현할 수 없다. 그러나 Repository는 기본 저장소에 액세스하기 위해 DAO를 사용할 수 있다.

## [출처 및 참고]
* <https://www.baeldung.com/java-dao-vs-repository>
