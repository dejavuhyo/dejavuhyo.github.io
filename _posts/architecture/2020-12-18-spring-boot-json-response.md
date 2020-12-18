---
title: Spring Boot Json 응답
author: Hyosik
date: 2020-12-18 09:20:00 +0900
categories: [Application, Architecture]
tags: [spring-boot-json, spring-json, json-response, spring-json-response, 스프링-부트-json, 스프링-json-응답, json-응답]
---

## 1. @Controller(Spring MVC Controller)

### 1) Controller - View
Spring MVC의 @Controller는 주로 View를 반환하기 위해 사용한다. 아래와 같은 과정을 통해 Spring MVC Container는 Client의 요청으로부터 View를 반환한다.

![img001](/assets/img/2020-12-18-spring-boot-json-response/img001.png)

* Client는 URI 형식으로 웹 서비스에 요청을 보낸다.

* Mapping되는 Handler와 그 Type을 찾는 DispatcherServlet이 요청을 인터셉트한다.

* Controller가 요청을 처리한 후에 응답을 DispatcherServlet으로 반환하고, DispatcherServlet은 View를 사용자에게 반환한다.

### 2) Controller - Data
Spring MVC의 컨트롤러에서도 Data를 반환해야 하는 경우도 있다.

Spring MVC의 컨트롤러에서는 데이터를 반환하기 위해 @ResponseBody 어노테이션을 활용해주어야 한다.

이를 통해 Controller도 Json 형태로 데이터를 반환할 수 있다.

![img002](/assets/img/2020-12-18-spring-boot-json-response/img002.png)

* Client는 URI 형식으로 웹 서비스에 요청을 보낸다.

* Mapping되는 Handler와 그 Type을 찾는 DispatcherServlet이 요청을 인터셉트한다.

* @ResponseBody를 사용하여 Client에게 Json 형태로 데이터를 반환한다.

## 2. @RestController(Spring Restful Controller)
@RestController는 Spring MVC Controlle에 @ResponseBody가 추가된 것이다. 주 용도는 Json 형태로 객체 데이터를 반환하는 것이다.

![img003](/assets/img/2020-12-18-spring-boot-json-response/img003.png)

* Client는 URI 형식으로 웹 서비스에 요청을 보낸다.

* Mapping되는 Handler와 그 Type을 찾는 DispatcherServlet이 요청을 인터셉트한다.

* RestController는 해당 요청을 처리하고 데이터를 반환한다.

## 3. Gson 사용
@RestController와 @ResponseBody로 해결할 수 있으면 좋지만, Json 객체를 직접 만들어야 할 때가 발생한다.

JsonObject를 사용해서 Json 객체를 직접 만들 수 있고, JsonArray를 사용하여 배열도 담을 수 있다.

* 샘플 데이터

```sql
CREATE TABLE customer
(
    customer_id INT NOT NULL,
    first_name VARCHAR(40) NOT NULL,
    last_name VARCHAR(20) NOT NULL,
    company VARCHAR(80),
    address VARCHAR(70),
    city VARCHAR(40),
    state VARCHAR(40),
    country VARCHAR(40),
    postal_code VARCHAR(10),
    phone VARCHAR(24),
    fax VARCHAR(24),
    email VARCHAR(60) NOT NULL,
    support_rep_id INT,
    CONSTRAINT pk_customer PRIMARY KEY (customer_id)
);
```

* Maven

```xml
<dependency>
    <groupId>com.google.code.gson</groupId>
    <artifactId>gson</artifactId>
    <version>2.8.5</version>
</dependency>
```

* Controller

```java
import com.example.backend.service.SearchService;
import com.example.backend.vo.SearchVO;
import com.google.gson.JsonArray;
import com.google.gson.JsonObject;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;

@RestController
public class SearchController {

    @Autowired
    SearchService searchService;

    @GetMapping(value = "/getSearchVal")
    public String getSearchVal(SearchVO searchVO, @RequestParam("searchKwd") String searchKwd, @RequestParam("viewCnt") int viewCnt, @RequestParam("pageNum") int pageNum) {

        searchVO.setSearchKwd(searchKwd);
        searchVO.setViewCnt(viewCnt);
        searchVO.setPageNum(pageNum);

        List<SearchVO> searchValList = searchService.getSearchVal(searchVO);

        JsonObject obj = new JsonObject();
        obj.addProperty("title", "검색");

        JsonArray jsonArray = new JsonArray();
        for (SearchVO vo : searchValList) {
            JsonObject jsonObject = new JsonObject();
            jsonObject.addProperty("customer_id", vo.getCustomerId());
            jsonObject.addProperty("first_name", vo.getFirstName());
            jsonObject.addProperty("last_name", vo.getLastName());
            jsonObject.addProperty("company", vo.getCompany());
            jsonObject.addProperty("address", vo.getAddress());
            jsonObject.addProperty("city", vo.getCity());
            jsonObject.addProperty("state", vo.getState());
            jsonObject.addProperty("country", vo.getCountry());
            jsonObject.addProperty("postal_code", vo.getPostalCode());
            jsonObject.addProperty("phone", vo.getPhone());
            jsonObject.addProperty("fax", vo.getFax());
            jsonObject.addProperty("email", vo.getEmail());
            jsonObject.addProperty("support_rep_id", vo.getSupportRepId());
            
            jsonArray.add(jsonObject);
        }
        obj.add("data", jsonArray);

        return obj.toString();
    }
}
```

* VO

```java
public class SearchVO {

    private String searchKwd;
    private int viewCnt;
    private int pageNum;

    private String customerId;
    private String firstName;
    private String lastName;
    private String company;
    private String address;
    private String city;
    private String state;
    private String country;
    private String postalCode;
    private String phone;
    private String fax;
    private String email;
    private String supportRepId;

    public String getSearchKwd() {
        return searchKwd;
    }

    public void setSearchKwd(String searchKwd) {
        this.searchKwd = searchKwd;
    }

    public int getViewCnt() {
        return viewCnt;
    }

    public void setViewCnt(int viewCnt) {
        this.viewCnt = viewCnt;
    }

    public int getPageNum() {
        return pageNum;
    }

    public void setPageNum(int pageNum) {
        this.pageNum = pageNum;
    }

    public String getCustomerId() {
        return customerId;
    }

    public void setCustomerId(String customerId) {
        this.customerId = customerId;
    }

    public String getFirstName() {
        return firstName;
    }

    public void setFirstName(String firstName) {
        this.firstName = firstName;
    }

    public String getLastName() {
        return lastName;
    }

    public void setLastName(String lastName) {
        this.lastName = lastName;
    }

    public String getCompany() {
        return company;
    }

    public void setCompany(String company) {
        this.company = company;
    }

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }

    public String getCity() {
        return city;
    }

    public void setCity(String city) {
        this.city = city;
    }

    public String getState() {
        return state;
    }

    public void setState(String state) {
        this.state = state;
    }

    public String getCountry() {
        return country;
    }

    public void setCountry(String country) {
        this.country = country;
    }

    public String getPostalCode() {
        return postalCode;
    }

    public void setPostalCode(String postalCode) {
        this.postalCode = postalCode;
    }

    public String getPhone() {
        return phone;
    }

    public void setPhone(String phone) {
        this.phone = phone;
    }

    public String getFax() {
        return fax;
    }

    public void setFax(String fax) {
        this.fax = fax;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public String getSupportRepId() {
        return supportRepId;
    }

    public void setSupportRepId(String supportRepId) {
        this.supportRepId = supportRepId;
    }
}
```

* SQL

```xml
<select id="getSearchVal" resultType="com.example.backend.vo.SearchVO">
	select
	    customer_id
	    , first_name
	    , last_name
	    , company
	    , address
	    , city
	    , state
	    , country
	    , postal_code
	    , phone
	    , fax
	    , email
	    , support_rep_id
	from
	    customer
	where
	    (company like '%${searchKwd}%')
	order by customer_id asc
	limit #{viewCnt} offset (#{pageNum} - 1) * #{viewCnt}
</select>
```

* 결과
  - 요청 URL: http://localhost:8080/getTableVal?searchKwd=회사&viewCnt=10&pageNum=1

```json
{
	"title": "키워드 검색",
	"data": [{
		"customer_id": "1",
		"first_name": "Ahn",
		"last_name": "Hyosik",
		"company": "(주)우리회사",
		"address": "서울시 강남구",
		"city": "서울",
		"state": "activation",
		"country": "대한민국",
		"postal_code": "06000",
		"phone": "010-1234-5678",
		"fax": "02-1111-2222",
		"email": "hsahn@email.com",
		"support_rep_id": "2"
	}, {
		"customer_id": "2",
		"first_name": "Shin",
		"last_name": "Yujin",
		"company": "(주)월드회사",
		"address": "서울시 송파구",
		"city": "서울",
		"state": "activation",
		"country": "대한민국",
		"postal_code": "05500",
		"phone": "010-2345-6789",
		"fax": "02-2222-3333",
		"email": "yjshin@email.com",
		"support_rep_id": "1"
	}]
}
```

## [출처 및 참고]
* <https://mangkyu.tistory.com/49>
* <https://shinsunyoung.tistory.com/47>
