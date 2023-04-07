---
title: Spring MyBatis 대량의 데이터 bulk insert 처리
author: dejavuhyo
date: 2022-05-03 09:40:00 +0900
categories: [Framework, Spring]
tags: [spring-mybatis-bulk-insert, bulk-insert, spring-mybatis, massive-data-bulk-insert, spring-bulk-insert, mybatis-batch]
---

## 1. DB IOException 메시지
대량의 데이터를 bulk insert 하면 PostgreSQL에서 `Tried to send an out-of-range integer as a 2-byte value` IOException이 발생한다.

PostgreSQL의 JDBC에서 바인딩할 수 있는 매개변수 갯수에 제한이 있기 때문이다.

![ioexception](/assets/img/2022-05-03-spring-mybatis-bulk-insert-processing-large-data/ioexception.png)

## 2. 해결방법
Controller에서 select 데이터를 잘라서 insert 한다.

아래 소스는 2개의 DB에 연결하여 첫 번재 DB는 select, 두 번째 DB는 insert하는 소스이다. 따라서 두 개의 mapper가 존재한다.

### 1) Controller.java

```java
@RestController
public class RetailController {

    @Resource
    private RetailService retailService;

    @GetMapping("/retailList")
    public ResponseEntity retailList() {

        // data select 40000건
        List<RetailDTO> selectRetailList = retailService.selectRetailList();
        ArrayList<RetailDTO> retailDataList = new ArrayList<RetailDTO>();

        int count;
        int insCount = 0;
        int selSize = selectRetailList.size();

        for (count = 0; count < selSize; count++) {
            RetailDTO retailDTO = new RetailDTO();
            retailDTO = selectRetailList.get(count);
            retailDataList.add(retailDTO);

            // 1000건씩 자른다.
            if (insCount == 1000 || selSize == count) {
                // 1000건씩 insert
                retailService.insertRetailList(retailDataList);
                retailDataList = new ArrayList<RetailDTO>();
                insCount = 0;
            } else {
                insCount++;
            }
        }
        return new ResponseEntity(HttpStatus.OK);
    }
}
```

### 2) Service.java

```java
public interface RetailService {
    List<RetailDTO> selectRetailList();
    int insertRetailList(List<RetailDTO> list);
}
```

### 3) ServiceImpl.java

```java
@Service
public class RetailServiceImpl implements RetailService {

    @Autowired
    PrimaryMapper primaryMapper;

    @Autowired
    SecondaryMapper secondaryMapper;

    @Override
    public List<RetailDTO> selectRetailList() {
        return primaryMapper.selectRetailList();
    }

    @Override
    public int insertRetailList(List<RetailDTO> list) {
        return secondaryMapper.insertRetailList(list);
    }
}
```

### 4) SelectMapper.java

```java
@Mapper
public interface SelectMapper {
    List<RetailDTO> selectRetailList();
}
```

### 5) InsertMapper.java

```java
@Mapper
public interface InsertMapper {
    int insertRetailList(List<RetailDTO> list);
}
```

### 6) DTO.java

```java
@Getter
@Setter
public class RetailDTO {
    private String bizplCd;
    private String bizplNm;
    private String bizplAddr;
    private String telNo;
    private String openDt;
    private String closeDt;
}
```

### 7) SelectQuery.xml

```xml
<select id="selectRetailList" resultType="com.example.dto.RetailDTO">
    SELECT DISTINCT BIZPL_CD
                  , BIZPL_NM
                  , BIZPL_ADDR
                  , TEL_NO
                  , OPEN_DT
                  , CLOSE_DT
    FROM TS_BIZPL
</select>
```

### 8) InsertQuery.xml

```xml
<insert id="insertRetailList" parameterType="java.util.List" useGeneratedKeys="true">
    INSERT INTO BIZPL ( BIZPL_CD
                     , BIZPL_NM
                     , BIZPL_ADDR
                     , TEL_NO
                     , OPEN_DT
                     , CLOSE_DT)
    VALUES
    <foreach collection="list" item="item" separator=",">
           ( #{item.bizplCd}
           , #{item.bizplNm}
           , #{item.bizplAddr}
           , #{item.telNo}
           , #{item.openDt}
           , #{item.closeDt})
    </foreach>
</insert>
```

## [출처 및 참고]
* [https://yamea-guide.tistory.com/entry/Spring-MyBatis-Batch-Transaction-%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EB%8C%80%EC%9A%A9%EB%9F%89-SQL%EC%9E%91%EC%97%85](https://yamea-guide.tistory.com/entry/Spring-MyBatis-Batch-Transaction-%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EB%8C%80%EC%9A%A9%EB%9F%89-SQL%EC%9E%91%EC%97%85)
* [https://coder-question-ko.com/cq-ko-blog/101377](https://coder-question-ko.com/cq-ko-blog/101377)
