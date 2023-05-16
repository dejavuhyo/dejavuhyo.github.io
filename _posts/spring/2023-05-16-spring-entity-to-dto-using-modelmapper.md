---
title: Spring Modelmapper를 사용하여 Entity에서 DTO로 변환
author: dejavuhyo
date: 2023-05-16 09:25:00 +0900
categories: [Framework, Spring]
tags: [entity-dto, dto-entity, modelmapper, spring-entity, spring-dto, entity-dto-conversion, entity-dto-변환, 모델매퍼]
---

## 1. Entity에서 DTO로 변환
Spring 애플리케이션의 내부 엔터티와 클라이언트에 다시 게시되는 외부 DTO (데이터 전송 개체) 간에 발생해야 하는 변환을 처리한다.

## 2. Model Mapper
`pom.xml`에 dependency를 추가한다.

```xml
<dependency>
    <groupId>org.modelmapper</groupId>
    <artifactId>modelmapper</artifactId>
    <version>3.1.0</version>
</dependency>
```

그런 다음 Spring 구성에서 ModelMapper 빈을 정의한다.

```java
@Bean
public ModelMapper modelMapper() {
    return new ModelMapper();
}
```

## 3. DTO
양면 문제인 Post DTO의 DTO 측면이다.

```java
public class PostDto {
    private static final SimpleDateFormat dateFormat
      = new SimpleDateFormat("yyyy-MM-dd HH:mm");

    private Long id;

    private String title;

    private String url;

    private String date;

    private UserDto user;

    public Date getSubmissionDateConverted(String timezone) throws ParseException {
        dateFormat.setTimeZone(TimeZone.getTimeZone(timezone));
        return dateFormat.parse(this.date);
    }

    public void setSubmissionDate(Date date, String timezone) {
        dateFormat.setTimeZone(TimeZone.getTimeZone(timezone));
        this.date = dateFormat.format(date);
    }

    // standard getters and setters
}
```

두 가지 사용자 지정 날짜 관련 메서드는 클라이언트와 서버 간의 날짜 변환을 처리한다.

`getSubmissionDateConverted()` 메서드는 날짜 문자열을 서버 시간대의 날짜로 변환하여 지속되는 Post 엔터티에서 사용한다. `setSubmissionDate()` 메서드는 DTO의 날짜를 현재 사용자 시간대의 Post 날짜로 설정하는 것이다.

## 4. Service
이제 Entity(DTO가 아님)와 함께 작동하는 서비스이다.

```java
public List<Post> getPostsList(
  int page, int size, String sortDir, String sort) {
 
    PageRequest pageReq
     = PageRequest.of(page, size, Sort.Direction.fromString(sortDir), sort);
 
    Page<Post> posts = postRepository
      .findByUser(userService.getCurrentUser(), pageReq);
    return posts.getContent();
}
```

## 5. Controller
서비스 위의 계층인 컨트롤러 계층이다. 여기서 변환이 실제로 발생한다.

Post 자원에 대한 간단한 REST API를 노출하는 표준 컨트롤러 구현이다.

```java
@Controller
class PostRestController {

    @Autowired
    private IPostService postService;

    @Autowired
    private IUserService userService;

    @Autowired
    private ModelMapper modelMapper;

    @GetMapping
    @ResponseBody
    public List<PostDto> getPosts(...) {
        //...
        List<Post> posts = postService.getPostsList(page, size, sortDir, sort);
        return posts.stream()
          .map(this::convertToDto)
          .collect(Collectors.toList());
    }

    @PostMapping
    @ResponseStatus(HttpStatus.CREATED)
    @ResponseBody
    public PostDto createPost(@RequestBody PostDto postDto) {
        Post post = convertToEntity(postDto);
        Post postCreated = postService.createPost(post));
        return convertToDto(postCreated);
    }

    @GetMapping(value = "/{id}")
    @ResponseBody
    public PostDto getPost(@PathVariable("id") Long id) {
        return convertToDto(postService.getPostById(id));
    }

    @PutMapping(value = "/{id}")
    @ResponseStatus(HttpStatus.OK)
    public void updatePost(@PathVariable("id") Long id, @RequestBody PostDto postDto) {
        if(!Objects.equals(id, postDto.getId())){
            throw new IllegalArgumentException("IDs don't match");
        }
        Post post = convertToEntity(postDto);
        postService.updatePost(post);
    }
}
```

다음은 **엔터티에서 Dto로의 변환**이다.

```java
private PostDto convertToDto(Post post) {
    PostDto postDto = modelMapper.map(post, PostDto.class);
    postDto.setSubmissionDate(post.getSubmissionDate(), 
        userService.getCurrentUser().getPreference().getTimezone());
    return postDto;
}
```

다음은 **Dto에서 엔터티로의 변환**이다.

```java
private Post convertToEntity(PostDto postDto) throws ParseException {
    Post post = modelMapper.map(postDto, Post.class);
    post.setSubmissionDate(postDto.getSubmissionDateConverted(
      userService.getCurrentUser().getPreference().getTimezone()));
 
    if (postDto.getId() != null) {
        Post oldPost = postService.getPostById(postDto.getId());
        post.setRedditID(oldPost.getRedditID());
        post.setSent(oldPost.isSent());
    }
    return post;
}
```

모델 매퍼의 도움으로 변환 논리가 빠르고 간단하다. 매퍼의 map API를 사용하고 있으며 변환 로직을 한 줄도 작성하지 않고 데이터를 변환하고 있다.

## 6. Unit Testing
엔터티와 DTO 간의 변환이 제대로 작동하는지 확인하기 위한 테스트이다.

```java
public class PostDtoUnitTest {

    private ModelMapper modelMapper = new ModelMapper();

    @Test
    public void whenConvertPostEntityToPostDto_thenCorrect() {
        Post post = new Post();
        post.setId(1L);
        post.setTitle(randomAlphabetic(6));
        post.setUrl("www.test.com");

        PostDto postDto = modelMapper.map(post, PostDto.class);
        assertEquals(post.getId(), postDto.getId());
        assertEquals(post.getTitle(), postDto.getTitle());
        assertEquals(post.getUrl(), postDto.getUrl());
    }

    @Test
    public void whenConvertPostDtoToPostEntity_thenCorrect() {
        PostDto postDto = new PostDto();
        postDto.setId(1L);
        postDto.setTitle(randomAlphabetic(6));
        postDto.setUrl("www.test.com");

        Post post = modelMapper.map(postDto, Post.class);
        assertEquals(postDto.getId(), post.getId());
        assertEquals(postDto.getTitle(), post.getTitle());
        assertEquals(postDto.getUrl(), post.getUrl());
    }
}
```

## [출처 및 참고]
* [https://www.baeldung.com/entity-to-and-from-dto-for-a-java-spring-application](https://www.baeldung.com/entity-to-and-from-dto-for-a-java-spring-application)
