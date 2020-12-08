---
title: FileWriter, BufferedWriter를 이용한 파일 생성
author: Hyosik
date: 2020-08-12 11:00:00 +0900
categories: [Language, Java]
tags: [filewriter, bufferedwriter, file_creation, 파일생성]
---

아래 코드는 모두 동일한 형태의 파일을 생성하지만 성능에는 큰 차이가 있다.

파일 크기가 100K를 넘는다면 FileWriter를 단독으로 쓰기보다는 BufferedWriter와 FileWriter를 혼합하여 사용하는 것이 파일을 기록할 때 속도가 더 빠르다.

## 1. FileWriter를 이용한 파일 생성

```java
import java.io.File;
import java.io.FileWriter;

public class FileWriterTest {

    public static void main(String[] args) {

        String text = "File Writer Test";
        String fileName = "D://result.txt";

        try{
            // 파일 객체 생성
            File file = new File(fileName);

            // true 지정시 파일의 기존 내용에 이어서 작성
            FileWriter fw = new FileWriterTest(file, true);

            // 파일안에 문자열 쓰기
            fw.write(text);
            fw.flush();

            // 객체 닫기
            fw.close();

        }catch(Exception e){
            e.printStackTrace();
        }
    }
}
```

## 2. FileWriter와 BufferedWriter를 이용한 파일 생성

```java
import java.io.BufferedWriter;

public class FileWriterTest {

    public static void main(String[] args) {

        String text = "File Writer Test";
        String fileName = "D://result.txt";

        try {
            // BufferedWriter 와 FileWriter를 조합하여 사용 (속도 향상)
            BufferedWriter bw = new BufferedWriter(new java.io.FileWriter(fileName, true));

            // 파일안에 문자열 쓰기
            bw.write(text);
            bw.flush();

            // 객체 닫기
            bw.close();

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

## 출처 및 참고
* <https://fruitdev.tistory.com/54>
