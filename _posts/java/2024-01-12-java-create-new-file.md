---
title: Java 새 파일 생성
author: dejavuhyo
date: 2024-01-12 09:55:00 +0900
categories: [Language, Java]
tags: [java-file, java-new-file, 자바-파일, 자바-새-파일, 파일-생성]
---

## 1. Java NIO를 사용
`Files.createFile(path, attribs)`은 Java에서 비어 있고 쓰기 가능한 새 파일을 생성하는 가장 좋은 방법이며 선호되는 접근 방식이다.

* `createFile()` 메소드는 File 대신 Path 인터페이스를 사용한다. 파일이 이미 존재하는지 확인하고 이후에 파일을 생성한다.

* 기존 파일을 확인하고 파일을 생성하는 작업은 단일 원자 작업으로 수행된다.

* attribs은 파일을 생성할 때 원자적으로 설정할 파일 속성의 선택적 목록이다.

* 해당 이름의 파일이 이미 존재하는 경우 FileAlreadyExistsException을 반환한다.

* I/O 오류가 발생하거나 상위 디렉터리가 존재하지 않으면 IOException을 반환한다.

쓰기 가능한 새 파일 생성 예제이다.

```java
String TEXT_FILE = "C:/temp/io/textFile.txt";

Path textFilePath = Paths.get(TEXT_FILE);
Files.createFile(textFilePath);
```

새로운 읽기 전용 파일 만들기 예제이다. 파일을 생성하는 동안 파일 속성을 설정한다. 주어진 예에서는 `r–r–r–` 문자열을 사용하여 소유자 그룹 및 기타 사용자에 대한 읽기 전용("r") 액세스를 설정한다.

```java
String TEXT_FILE = "C:/temp/io/textFile.txt";

Set<PosixFilePermission> permissions = PosixFilePermissions.fromString("r--r--r--");
FileAttribute<Set<PosixFilePermission>> attribs = PosixFilePermissions.asFileAttribute(permissions);

Path textFilePath = Paths.get(TEXT_FILE);
Files.createFile(textFilePath, attribs); 
```

## 2. File.createNewFile() 사용
`File.createNewFile()`이 이름을 가진 파일이 아직 존재하지 않는 경우에만 메소드를 사용하여 새 파일을 생성한다. 기존 파일을 확인하고 파일을 생성하는 것은 원자성 작업 이다.

이 메서드는 boolean 값을 반환한다.

* true: 파일이 성공적으로 생성된 경우

* false: 파일이 이미 존재하는 경우

* IOException: I/O 오류가 발생한 경우

```java
String TEXT_FILE = "C:/temp/io/textFile.txt";

File textFile = new File(TEXT_FILE);
boolean isFileCreated = textFile.createNewFile(); 
```

## 3. FileOutputStream 사용
생성자는 지정된 위치에 자동으로 새 파일을 만든다. 해당 이름의 파일이 이미 존재하는 경우 해당 파일을 덮어쓰게 되므로 주의한다.

주어진 파일 경로가 디렉터리를 나타내거나 어떤 이유로든 새 파일을 만들 수 없는 경우 FileNotFoundException이 발생한다.

```java
String TEXT_FILE = "C:/temp/io/textFile.txt";

try(FileOutputStream fos = new FileOutputStream(TEXT_FILE)){
  // We can write data as byte[]
  // fos.write(data, 0, data.length);
}
```

## 4. Guava Files.touch()
Guava를 포함하려면 `pom.xml`에 dependency를 추가한다.

```xml
<dependency>
    <groupId>com.google.guava</groupId>
    <artifactId>guava</artifactId>
    <version>31.1-jre</version>
</dependency>
```

`Files.touch()` 메서드는 Unix touch 명령과 유사하다. 빈 파일을 생성하거나 마지막으로 업데이트된 타임스탬프를 업데이트한다.

옵션 없이 touch 명령을 사용하면 파일이 존재하지 않는다고 가정하고 빈 파일을 생성한다. 파일이 존재하면 타임스탬프가 변경된다.

```java
String TEXT_FILE = "C:/temp/io/textFile.txt";

com.google.common.io.Files.touch(new File(TEXT_FILE));
```

## 5. Apache Commons IO의 FileUtils
Apache Commons IO를 포함하려면 `pom.xml`에 dependency를 추가한다.

```xml
<dependency>
    <groupId>commons-io</groupId>
    <artifactId>commons-io</artifactId>
    <version>2.11.0</version>
</dependency>
```

`FileUtils.touch()`는 Guava 예제와 매우 유사하다. 또한 Unix의 "touch" 유틸리티와 동일한 동작을 구현한다. 

또한 v1.3부터 ​​이 방법은 상위 디렉토리가 존재하지 않는 경우 상위 디렉토리를 생성한다. 파일의 마지막 수정 날짜를 설정할 수 없으면 IOException이 발생한다.

```java
String TEXT_FILE = "C:/temp/io/textFile.txt";

org.apache.commons.io.FileUtils.touch(new File(TEXT_FILE));
```

## [출처 및 참고]
* [https://howtodoinjava.com/java/io/how-to-create-a-new-file-in-java/](https://howtodoinjava.com/java/io/how-to-create-a-new-file-in-java/)
