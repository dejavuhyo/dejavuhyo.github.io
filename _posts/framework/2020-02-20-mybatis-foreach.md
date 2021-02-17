---
title: MyBatis foreach 문법
author: dejavuhyo
date: 2020-02-20 11:00:00 +0900
categories: [Application, Framework]
tags: [mybatis-foreach, mybatis-반복문]
---

## 1. Array 사용

* Controller

```java
String[] array = {"a", "b", "c"};
demoVO.setDemoArray(array);
```

* VO

```java
public class DemoVO {

    private String[] demoArray;

    public String[] getDemoArray() {
        return demoArray;
    }

    public void setDemoArray(String[] demoArray) {
        this.demoArray = demoArray;
    }
}
```

* SQL

```xml
<select id="selectDemoList" resultType="com.example.demo.vo.DemoVO">
    select
        id
        , name
        , addr
    from
        table_name
    where
        id in
    <foreach collection="demoArray" item="item" index="index" open="(" close=")" separator=",">
        #{item}
    </foreach>
</select>
```

## 2. List 사용

* Controller

```java
List<String> list = new ArrayList<>();
list.add("a");
list.add("b");
list.add("c");
demoVO.setDemoList(list);
```

* VO

```java
import java.util.List;

public class DemoVO {

    private List<String> demoList;

    public List<String> getDemoList() {
        return demoList;
    }

    public void setDemoList(List<String> demoList) {
        this.demoList = demoList;
    }
}
```

* SQL

```xml
<select id="selectDemoList" resultType="com.example.demo.vo.DemoVO">
    select
        id
        , name
        , addr
    from
        table_name
    where
        id in
    <foreach collection="demoList" item="item" index="index" open="(" close=")" separator=",">
        #{item}
    </foreach>
</select>
```
