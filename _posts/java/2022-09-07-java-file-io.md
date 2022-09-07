---
title: Java 파일 입출력
author: dejavuhyo
date: 2022-09-07 09:30:00 +0900
categories: [Language, Java]
tags: [java-file-io, java-file, java-io, file-io, 자바-파일-입출력, 자바-파일, 자바-입출력, 파일-입출력]
---

## 1. 표준 입출력
자바에서는 콘솔과 같은 표준 입출력 장치를 위해 System이라는 표준 입출력 클래스를 정의하고 있다.

java.lang 패키지에 포함되어 있는 System 클래스는 표준 입출력을 위해 다음과 같은 클래스 변수를 제공한다.

| 클래스 변수 | 입출력 스트림 | 설명 |
|:-----:|:-----:|:-----:|
| System.in | InputStream | 콘솔로부터 데이터를 입력받음 |
| System.out | PrintStream | 콘솔로 데이터를 출력함 |
| System.err | PrintStream | 콘솔로 데이터를 출력함 |

표준 입출력 스트림은 자바가 자동으로 생성하므로, 개발자가 별도로 스트림을 생성하지 않아도 사용할 수 있다.

## 2. 표준 입출력의 대상 변경
앞서 살펴본 세 가지 입출력 스트림은 모두 콘솔과 같은 표준 입출력 장치를 대상으로 한다.

하지만 이와 같은 스트림에 다음 System 메소드를 사용하면 스트림의 대상을 다른 입출력 장치로 변경할 수 있다.

| 메소드 | 설명 |
|:-----:|:-----:|
| static void setIn(InputStream in) | 입력 스트림의 대상을 전달된 입력 스트림으로 변경함 |
| static void setOut(PrintStream out) | 출력 스트림의 대상을 전달된 출력 스트림으로 변경함 |
| static void setErr(PrintStream err) | 출력 스트림의 대상을 전달된 출력 스트림으로 변경함 |

## 3. RandomAccessFile 클래스
앞서 살펴본 다양한 입출력 스트림을 이용하면 파일에 순차적으로 입출력 작업을 수행할 수 있다.

하지만 순차적인 접근이 아닌 임의의 지점에 접근하여 작업을 수행하고 싶다면, RandomAccessFile 클래스를 사용하면 된다.

이 클래스는 파일만을 대상으로 하며, 임의의 지점에서 입출력을 동시에 수행할 수 있다.

RandomAccessFile 클래스의 생성자에는 인수로 파일의 이름뿐만 아니라 파일 모드까지 함께 전달해야 한다.

파일 모드란 파일의 사용 용도를 나타내는 문자열로, 자바에서 사용할 수 있는 대표적인 파일 모드는 다음과 같다.

| 파일 모드 | 설명 |
|:-----:|:-----:|
| "r" | 파일을 오로지 읽는 것만 가능한 모드로 개방함 |
| "rw" | 파일을 읽고 쓰는 것이 모두 가능한 모드로 개방함. 만약 파일이 없으면 새로운 파일을 생성함 |

`getFilePointer()` 메소드를 사용하면 파일 포인터의 현재 위치를 확인할 수 있다.

또한, `seek()` 메소드를 사용하면 파일 포인터의 위치를 변경할 수도 있다.

* 예제

```java
public static void main(String[] args){
    try {
        // "rw" 모드로 "data.txt" 파일을 개방함.
        RandomAccessFile file = new RandomAccessFile("data.txt", "rw");
        System.out.println(file.getFilePointer()); // 0 : 파일 포인터의 현재 위치를 반환함
        file.writeInt(10);                         // 정수 10을 저장함
        System.out.println(file.getFilePointer()); // 4
        file.seek(20);                             // 파일 포인터의 위치를 20으로 이동시킴
        System.out.println(file.getFilePointer()); // 20
    } catch (IOException e) {
        e.printStackTrace();
    }
}
```

* 실행 결과

```text
0
4
20
```

## 4. File 클래스
앞서 살펴본 입출력 스트림을 사용하면 파일을 통한 입출력 작업을 수행할 수 있다.

하지만 파일의 제거나 디렉터리에 관한 작업 등은 입출력 스트림을 통해서는 수행할 수 없다.

자바는 이러한 입출력 작업 이외의 파일과 디렉터리에 관한 작업을 File 클래스를 통해 처리하도록 하고 있다.

File 클래스에는 위와 같은 작업을 위한 다양한 메소드가 정의되어 있다.

| 메소드 | 설명 |
|:-----:|:-----:|
| `boolean canRead()` | 해당 파일이 읽을 수 있는 파일인지를 검사함 |
| `boolean canWrite()` | 해당 파일이 쓸 수 있는 파일인지를 검사함 |
| `boolean delete()` | 해당 파일 또는 디렉터리를 삭제함 |
| `boolean exists()` | 해당 파일이 존재하는지를 검사함 |
| `String getPath()` | 해당 파일의 경로명을 문자열로 반환함 |
| `boolean isAbsolute()` | 해당 파일의 경로명이 절대 경로인지를 검사함 |
| `boolean isDirectory()` | 해당 파일이 디렉터리인지를 검사함 |
| `boolean isFile()` | 해당 파일이 파일인지를 검사함 |
| `long length()` | 해당 파일의 크기를 반환함 |
| `boolean mkdir()` | 지정된 경로에 디렉터리를 생성함 |
| `boolean mkdirs()` | 지정된 경로에 디렉터리를 생성하며, 필요한 모든 상위 디렉터리도 생성함 |
| `boolean renameTo(File dest)` | 해당 파일의 이름을 전달된 파일 이름으로 변경함 |
| `boolean setExecutable(boolean executable)`, `boolean setReadable(boolean readable)`, `boolean setWritable(boolean writable)`, `boolean setReadOnly()` | 해당 파일의 속성을 변경함 |

* 예제

```java
public static void main(String[] args){
    File dir = new File("D:\\data");       // 디렉터리 생성
    File file = new File(dir, "data.txt"); // 파일 생성
    if(!file.exists()) {                   // 파일이 생성되었는지를 확인함
        System.out.println("파일이 존재하지 않습니다.");
        System.exit(0);
    }
    System.out.println(file.getPath());    // D:\data\data.txt
    System.out.println(file.length());     // 파일의 크기를 반환함
}
```

* 실행 결과

```text
D:\data\data.txt
0
```

## [출처 및 참고]
* <http://www.tcpschool.com/java/java_io_file>
