---
title: MyBatis List 파라미터 foreach 사용
author: dejavuhyo
date: 2022-11-25 22:00:00 +0900
categories: [DevOps, MyBatis]
tags: [mybatis-list, mybatis, mybatis-foreach, mybatis-list-mybatis, mybatis-foreach-사용, mybatis-list-파라미터]
---

## 1. Json

```json
{
    "compCd": "50",
    "custCdList": [{
        "custCd": "001"
    }, {
        "custCd": "002"
    }, {
        "custCd": "003"
    }]
}
```

## 2. Controller 설정

```java
@Slf4j
@RequiredArgsConstructor
@RestController
@RequestMapping(path = "/master")
public class CenResource {
    private final InfoService infoService;

    @PostMapping(path = "/info")
    public ResponseEntity<Result> findInfo(@RequestBody InfoDTO infoDTO) {

        Result result = new Result();
        HttpHeaders httpHeaders = new HttpHeaders();
        httpHeaders.setContentType(new MediaType("application", "json", Charset.forName("UTF-8")));

        List<InfoDTO> rs = infoService.findInfo(infoDTO);
        result.setStatus(StatusEnum.OK);
        result.setData(rs);
        result.setMessage("success");

        return new ResponseEntity<>(result, httpHeaders, HttpStatus.OK);
    }
}
```

## 3. DTO 설정

```java
@Getter
@Setter
@NoArgsConstructor
public class Cen {
    private String compCd;
    private String custCd;
    private List<Info> custCdList;
    private String custLoclNm;
    private String custEngNm;
    private String rprsvNm;
}
```

## 4. SQL 설정

```xml
<select id="selectCenBascInfo" parameterType="com.example.info.InfoDTO" resultType="com.example.info.InfoDTO">
    select tmch.cust_cd
         , tmch.cust_locl_nm
         , tmch.cust_eng_nm
         , tmch.rprsv_nm
    from tb_mm_cust_h tmch
         inner join tb_mm_cust_m tmcm
            on tmch.cust_cd = tmcm.cust_cd

            <if test="@org.apache.commons.lang3.StringUtils@isNotBlank(compCd)">
                and tmcm.comp_cd = #{compCd}
            </if>

            <if test="@org.apache.commons.collections4.CollectionUtils@isNotEmpty(custCdList)">
                and tmcm.cust_cd in
                <foreach collection="custCdList" item="item" index="index" separator="," open="(" close=")">
                    #{item.custCd}
                </foreach>
            </if>

</select>
```

## [출처 및 참고]
* <https://linked2ev.github.io/mybatis/2020/01/05/MyBatis-5-foreach-%EA%B5%AC%EB%AC%B8,-parameterType-List-Model-Map/>
