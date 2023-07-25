---
title: 자바 HTTP URL 호출
author: dejavuhyo
date: 2023-07-25 09:20:00 +0900
categories: [Language, Java]
tags: [java-http-url, java-httpurlconnection, httpurlconnection, 자바-http-호출, http-url-호출]
---

## 1. 자바 HTTP URL 호출

```java
import lombok.extern.slf4j.Slf4j;

import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;
import java.nio.charset.StandardCharsets;

@Slf4j
public class HttpUrlConnectionTest {
    public static void main(String[] args) {
        try {
            URL url = new URL("https://dejavuhyo.github.io/");
            HttpURLConnection con = (HttpURLConnection) url.openConnection();
            con.setRequestMethod("GET");
            BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(con.getInputStream(), StandardCharsets.UTF_8));

            String line;
            while ((line = bufferedReader.readLine()) != null) {
                log.info(line);
            }

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

## [출처 및 참고]
* [https://overcome26.tistory.com/77](https://overcome26.tistory.com/77)
