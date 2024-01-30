---
title: Java 인코딩 확인
author: dejavuhyo
date: 2024-01-30 10:54:00 +0900
categories: [Language, Java]
tags: [java-encoding, encoding, 자바-인코딩, 인코딩]
---

## 1. 인코딩 확인
데이터를 수신했을 때 글자가 깨져 보이는 경우, 인코딩 방식 확인이 필요할 때 아래 로직으로 확인한다.

```java
public void checkEncoding(String textString) {
    byte[] bytes = textString.getBytes(StandardCharsets.UTF_8);
    textString = new String(bytes);

    String[] charSet = {"utf-8", "euc-kr", "ksc5601", "iso-8859-1", "x-windows-949"};

    String inputText = textString;
    IntStream.range(0, charSet.length).boxed().forEach(i -> IntStream.range(0, charSet.length).boxed().forEach(j -> {
        try {
            System.out.println("[" + charSet[i] + ", " + charSet[j] + "]" + new String(inputText.getBytes(charSet[i]), charSet[j]));
        } catch (UnsupportedEncodingException e) {
            throw new RuntimeException(e);
        }
    }));
}
```

## [출처 및 참고]
* [https://gongam100.tistory.com/10](https://gongam100.tistory.com/10)
* [https://d2.naver.com/helloworld/19187](https://d2.naver.com/helloworld/19187)
* [https://d2.naver.com/helloworld/76650](https://d2.naver.com/helloworld/76650)
