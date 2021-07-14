---
title: Spring Annotation 개념 및 정리
author: dejavuhyo
date: 2020-12-01 11:25:00 +0900
categories: [Application, Architecture]
tags: [spring-annotation, annotation, 스프링-어노테이션, 어노테이션]
---

## 1. Annotation 이란

Annotation은 Java5부터 새롭게 추가된 문법요소로, 사전적 의미는 주석이라는 의미를 가지고 있다.

자바에서 사용될 때의 Annotation은 코드 사이에 주석처럼 쓰여서 특별한 의미를 부여한다.

컴파일러가 특정 오류를 억제하도록 지시하는 것과 같이 프로그램 코드의 일부가 아닌 프로그램에 관한 데이터를 제공, 코드에 정보를 추가하는 정형화된 방법이다.

즉, 프로그램에게 추가적인 정보를 제공해주는 메타데이터(meta data: 데이터를 위한 데이터)라고 볼 수 있다.

## 2. 종류

### 1) Spring Annotation

| 어노테이션 | 설명 |
|:---:|:---:|
| @Controller | 스프링 MVC의 컨트롤러 객체임을 명시하는 어노테이션 |
| @RequestMapping | 특정 URI에 매칭되는 클래스나 메소드임을 명시하는 어노테이션 |
| @RequestParam | request의 파라미터를 가져오는 기능을 하며 메소드내에 변수로 활용 |
| @RequestHeader | 요청(request)에서 특정 HTTP헤더 정보를 추출할 때 사용 |
| @PathVariable | 메소드 파라미터 앞에 사용하며 해당 URL에 { } 특정 파라미터를 변수로 활용 가능 |
| @CookieValue | 현재 사용자의 쿠키가 존재하는 경우 쿠키 이름을 이용해서 쿠키 값을 추출 |
| @ModelAttribute | view에서 전달해주는 파라미터를 클래스의 멤버변수로 binding 해주는 어노테이션 |
| @InitBinder | 파라미터를 수집해서 객체로 만들 경우에 커스터마이징 |
| @ResponseBody | JSON 형식의 데이터값을 응답할 때 사용하는 것으로 response Body에 형식이 노출객체를 return시 json 라이브러리에 의해 문자열로 변환 |
| @RequestBody | 요청이 들어온 데이터(Json, XML)를 클래스나 model로 매핑 |
| @Repository | DAO 객체 |
| @Service | 서비스 객체 |
| @SessionAttributes | @SessionAttributes("name")이라고 하면 Model에 key값이 "name"으로 있는 값은 자동으로 세션에 저장 |
| @Runwith | JUnit 프레임워크의 테스트를 할수 있도록 설정 @Runwith에 Runner클래스 설정시 JUnit의 내장된 runner대신 설정한 SpringJUnit4ClassRunner를 실행 |
| @ContextConfiguration | 지정된 클래스나 문자열을 이용해 필요한 객체들을 스프링내 객체로 등록 |
| @Log4j | Lombok을 이용해 로그를 기록하기 위한 Logger 변수 생성 |
| @Autowired | 해당 인스턴스 변수에 스프링으로부터 자동으로 Bean을 주입, new 연사자와 같음 |
| @Test | JUnit에서 테스트 대상을 표시하며 단위테스트 메소드임을 명시 |
| @Component | Component는 스프링에게 해당 클래스가 관리해야할 대상임을 표시 |
| @Setter | Set( ) 메소드를 자동으로 생성하여, 사용할수 있게 해줌 |
| @Getter | Get( ) 메소드를 자동으로 생성하여, 사용할수 있게 해줌 | 
| @GetMapping | RequestMapping(Method=RequestMethod.GET)과 동일 |
| @PostMapping | RequestMapping(Method=RequestMethod.POST)과 동일 |
| @Transactional | 데이터베이스 트랜잭션 설정 어노테이션 (AutoCommit, rollback, commit 등)  DB의 접근이 하나라도 실패시 rollback 비지니스 로직과 트랜잭션 관리는 모두 Service에서 하기때문에 Service 메소드는 @Transactional 사용 |
| @Cacheable | 메소드 앞에 지정하여 사용하며 메소드를 최초 호출시 캐시에 적재하고 추후 동일한 요청이 들어올 시 캐시의 결과를 리턴. 메소드의 호출 횟수를 줄여주는 어노테이션 |
| @RestController | Spring Restful Controller로 데이터를 반환하는 컨트롤러이다. view가 필요없는 API에 지원(Spring 4.0.1 이후)하며, @RequestMapping 메소드가 @ResponseBody 의미를 가정한다. data(json, xml) return 시 사용 |

### 2) Lombok Annotation

| 어노테이션 | 설명 |
|:---:|:---:|
| @AllArgsConstructor | 모든 필드를 파라미터로 가지는 생성자를 생성 |
| @NoArgsConstructor | 파라미터가 없는 기본 생성자를 생성 |
| @RequiredArgsConstructor | final, @NonNull 인 필드값만 파라미터로 받는 생성자를 생성 |
| @EqualsAndHashCode | equals( )메소드와 hashCode( )메소드를 오버라이드 |
| @Data | @toString, @getter/setter, @RequiredArgsConstructor 등 모두 사용한것과 같은 기능 |

### 3) Jpa Annotation

| 어노테이션 | 설명 |
|:---:|:---:|
| @Entity | 데이터베이스의 테이블과 1대1 매칭 |
| @Id | 테이블 컬럼의 기본키에 설정 |
| @Column | 테이블 컬럼에 설정, @Entity로 지정시 생략가능 |
| @GeneratedValue | 기본키에 지정된 auto increment 등의 속성을 지정 |
| @EmbeddedId | 테이블 컬럼의 복합키에 설정 |
| @Enumerated | enum과 관련되어 있으며, EnumType.ORDINAL, STRING의 속성 |
| @Transient | 데이터베이스에서 실제 사용하지 않는 것을 지정, 임시값으로 활용 |

## [출처 및 참고]
* <https://chunjyu.tistory.com/23>
* <https://palyoung.tistory.com/72>
* <https://helloworld-88.tistory.com/147>
