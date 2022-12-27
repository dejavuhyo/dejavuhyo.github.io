---
title: SpringDoc OpenAPI
author: dejavuhyo
date: 2022-04-28 15:20:00 +0900
categories: [Application, Framework]
tags: [springdoc-openapi, spring-boot-openapi, springdoc, openapi, spring-openapi, swagger, spring-swagger, api-docs, swagger-api]
---

## 1. springdoc-openapi란
springdoc-openapi java 라이브러리는 스프링 부트 프로젝트를 사용하여 API 문서 생성을 자동화하는 데 도움이 된다. springdoc-openapi는 스프링 구성, 클래스 구조 및 다양한 주석을 기반으로 API 의미를 추론하기 위해 런타임에 애플리케이션을 검사하여 작동한다.

JSON/YAML 및 HTML 형식 API로 문서를 자동으로 생성한다. 이 문서는 swagger-api 주석을 사용하여 주석으로 완료할 수 있다.

이 라이브러리는 다음을 지원한다.

* OpenAPI 3

* Spring-boot (v1 and v2)

* JSR-303, specifically for @NotNull, @Min, @Max, and @Size.

* Swagger-ui

* OAuth 2

* GraalVM native images

## 2. 설정

### 1) 라이브러리 추가
spring-boot와 swagger-ui 간의 통합을 위해 프로젝트 종속성 목록에 라이브러리를 추가한다.

```xml
<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-ui</artifactId>
    <version>1.6.8</version>
</dependency>
```

swagger-ui가 스프링 부트 애플리케이션에 자동으로 배포된다.

문서는 공식 [swagger-ui jar](https://github.com/swagger-api/swagger-ui)를 사용하여 HTML 형식으로 제공된다.

### 2) UI 접속
Swagger UI 페이지를 `http://server:port/context-path/swagger-ui.html`에서 사용할 수 있으며, OpenAPI json 형식 설명은 `http://server:port/context-path/v3/api-docs`에서 사용할 수 있다.

HTML 형식의 swagger 문서의 사용자 정의 경로의 경우, spring-boot 구성 파일에 사용자 정의 springdoc 속성을 추가한다.

* application.yml

```yml
springdoc:
  swagger-ui:
    path: /swger-ui.html
```

### 3) 주석 작성

* BoardController.java

```java
import com.example.board.service.BoardService;
import com.example.board.vo.BoardVO;
import io.swagger.v3.oas.annotations.Operation;
import io.swagger.v3.oas.annotations.media.ArraySchema;
import io.swagger.v3.oas.annotations.media.Content;
import io.swagger.v3.oas.annotations.media.Schema;
import io.swagger.v3.oas.annotations.responses.ApiResponse;
import io.swagger.v3.oas.annotations.responses.ApiResponses;
import io.swagger.v3.oas.annotations.tags.Tag;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import javax.annotation.Resource;
import java.util.List;

@Tag(name = "BoardController", description = "게시판 컨트롤러")
@RestController
public class BoardController {

    @Resource
    private BoardService boardService;

    @Operation(operationId = "getBoardList", summary = "게시판 목록 조회", description = "게시판 목록을 조회한다.", tags = {"BoardController"})
    @ApiResponses(value = {
            @ApiResponse(responseCode = "200", description = "Successful Operation", content = @Content(array = @ArraySchema(schema = @Schema(implementation = BoardVO.class)))),
            @ApiResponse(responseCode = "400", description = "Invalid", content = @Content(array = @ArraySchema(schema = @Schema(implementation = BoardVO.class)))),
            @ApiResponse(responseCode = "404", description = "Not found", content = @Content(array = @ArraySchema(schema = @Schema(implementation = BoardVO.class))))
    })
    @GetMapping("/board")
    public ResponseEntity getBoardList() {

        List<BoardVO> getBoardList = boardService.getBoardList();

        if (getBoardList == null) {
            return new ResponseEntity("조회 실패", HttpStatus.NOT_FOUND);
        }

        return new ResponseEntity(getBoardList, HttpStatus.OK);
    }
}
```

### 4) Swagger-ui 구성
OpenAPI bean을 이용하여 Swagger-ui를 구성하는 메시지를 수정한다.

* OpenApiConfig.java

```java
import io.swagger.v3.oas.models.Components;
import io.swagger.v3.oas.models.OpenAPI;
import io.swagger.v3.oas.models.info.Contact;
import io.swagger.v3.oas.models.info.Info;
import io.swagger.v3.oas.models.info.License;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.stereotype.Component;

@Component
public class OpenApiConfig {
    @Bean
    public OpenAPI openAPI(@Value("v1.0") String appVersion) {

        Info info = new Info().title("Board API Docs").version(appVersion)
                .description("Spring Boot를 이용한 Board API Docs 입니다.")
                .termsOfService("http://swagger.io/terms/")
                .contact(new Contact().name("dejavuhyo").url("https://dejavuhyo.github.io/").email("dejavuhyo@gmail.com"))
                .license(new License().name("Apache License Version 2.0").url("http://www.apache.org/licenses/LICENSE-2.0"));

        return new OpenAPI()
                .components(new Components())
                .info(info);
    }
}
```

## 3. 실행확인

### 1) swagger-ui

![swagger-ui](/assets/img/2022-04-28-springdoc-openapi/swagger-ui.png)

### 2) api-docs

![api-docs](/assets/img/2022-04-28-springdoc-openapi/api-docs.png)

## 4. GitHub 소스코드

* <https://github.com/dejavuhyo/springdoc-openapi>

## [출처 및 참고]
* <https://springdoc.org/>
* <https://blog.jiniworld.me/83>
