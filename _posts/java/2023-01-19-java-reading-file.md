---
title: Java 파일 읽기
author: dejavuhyo
date: 2023-01-19 21:00:00 +0900
categories: [Language, Java]
tags: [java-read-file, bufferedreader, scannerm, stream-tokenizer, data-input-stream, sequence-input-stream, file-channel, 자바-파일-읽기, 파일-읽기]
---

## 1. 사용 정리

* BufferedReader: 한 줄씩 읽을때

* Scanner: 다른 구분 기호를 사용하여 읽을때

* StreamTokenizer: 파일을 토큰으로 읽을때

* DataInputStream: 이진 데이터 및 기본 데이터 유형을 읽을때

* SequenceInput Stream: 여러 파일을 하나의 스트림으로 연결

* FileChannel: 대용량 파일에서 더 빠르게 읽을때

## 2. 클래스패스에서 파일 읽기

### 1) 표준 자바 사용
`src/main/resources` 아래에서 사용할 수 있는 `fileTest.txt`를 읽는다.

```java
@Test
public void givenFileNameAsAbsolutePath_whenUsingClasspath_thenFileData() {
    String expectedData = "Hello, world!";
    
    Class clazz = FileOperationsTest.class;
    InputStream inputStream = clazz.getResourceAsStream("/fileTest.txt");
    String data = readFromInputStream(inputStream);

    Assert.assertThat(data, containsString(expectedData));
}
```

위의 코드에서는 현재 클래스를 사용하여 `getResourceAsStream` 메서드를 사용하여 파일을 로드하고 로드할 파일의 절대 경로를 전달했다.

같은 방법을 `ClassLoader` 인스턴스에서도 사용할 수 있다.

```java
ClassLoader classLoader = getClass().getClassLoader();
InputStream inputStream = classLoader.getResourceAsStream("fileTest.txt");
String data = readFromInputStream(inputStream);
```

`getClass().getClassLoader()`를 사용하여 현재 클래스의 classLoader를 얻는다.

주요 차이점은 `ClassLoader` 인스턴스에서 `getResourceAsStream`을 사용할 때 경로가 클래스 경로의 루트에서 시작하는 절대 경로로 처리된다는 것이다.

Class 인스턴스에 대해 사용되는 경우 경로는 패키지에 대한 상대 경로이거나 선행 슬래시로 암시되는 절대 경로일 수 있다.

```java
InputStream inputStream = null;
try {
    File file = new File(classLoader.getResource("fileTest.txt").getFile());
    inputStream = new FileInputStream(file);
    
    //...
}     
finally {
    if (inputStream != null) {
        try {
            inputStream.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### 2) commons-io 라이브러리 사용
다른 일반적인 옵션은 `commons-io` 패키지의 `FileUtils` 클래스를 사용하는 것이다.

```java
@Test
public void givenFileName_whenUsingFileUtils_thenFileData() {
    String expectedData = "Hello, world!";
        
    ClassLoader classLoader = getClass().getClassLoader();
    File file = new File(classLoader.getResource("fileTest.txt").getFile());
    String data = FileUtils.readFileToString(file, "UTF-8");
        
    assertEquals(expectedData, data.trim());
}
```

File 객체를 FileUtils 클래스의 `readFileToString()` 메서드에 전달한다. 이 유틸리티 클래스는 `InputStream` 인스턴스를 만들고 데이터를 읽기 위해 상용구 코드를 작성할 필요 없이 콘텐츠 로드를 관리한다.

동일한 라이브러리는 `IOUtils` 클래스도 제공한다.

```java
@Test
public void givenFileName_whenUsingIOUtils_thenFileData() {
    String expectedData = "Hello, world!";
        
    FileInputStream fis = new FileInputStream("src/test/resources/fileTest.txt");
    String data = IOUtils.toString(fis, "UTF-8");
        
    assertEquals(expectedData, data.trim());
}
```

## 3. BufferedReader로 읽기
`readLine()`은 파일 끝에 도달하면 null을 반환 한다.

```java
@Test
public void whenReadWithBufferedReader_thenCorrect()
  throws IOException {
     String expected_value = "Hello, world!";
     String file ="src/test/resources/fileTest.txt";
     
     BufferedReader reader = new BufferedReader(new FileReader(file));
     String currentLine = reader.readLine();
     reader.close();

    assertEquals(expected_value, currentLine);
}
```

## 4. Java NIO를 사용하여 파일에서 읽기
JDK7에서는 NIO 패키지가 크게 업데이트되었다.

Files 클래스와 `readAllLines` 메서드를 사용하는 예이다. `readAllLines` 메소드는 경로를 허용한다.

경로 클래스는 일부 추가 작업이 있는 `java.io.File`의 업그레이드로 간주할 수 있다.

### 1) 작은 파일 읽기

```java
@Test
public void whenReadSmallFileJava7_thenCorrect()
  throws IOException {
    String expected_value = "Hello, world!";

    Path path = Paths.get("src/test/resources/fileTest.txt");

    String read = Files.readAllLines(path).get(0);
    assertEquals(expected_value, read);
}
```

바이너리 데이터가 필요한 경우에도 `readAllBytes()` 메서드를 사용할 수 있다.

### 2) 대용량 파일 읽기
`Files` 클래스로 큰 파일을 읽으려면 `BufferedReader`를 사용할 수 있다.

```java
@Test
public void whenReadLargeFileJava7_thenCorrect()
  throws IOException {
    String expected_value = "Hello, world!";

    Path path = Paths.get("src/test/resources/fileTest.txt");

    BufferedReader reader = Files.newBufferedReader(path);
    String line = reader.readLine();
    assertEquals(expected_value, line);
}
```

### 3) Files.lines()를 사용하여 파일 읽기
JDK8은 `Files` 클래스 내에서 `lines()` 메서드를 제공한다. 문자열 요소의 스트림을 반환한다.

데이터를 바이트로 읽고 `UTF-8` 문자 집합을 사용하여 디코딩하는 방법의 예이다.

```java
@Test
public void givenFilePath_whenUsingFilesLines_thenFileData() {
    String expectedData = "Hello, world!";
         
    Path path = Paths.get(getClass().getClassLoader()
      .getResource("fileTest.txt").toURI());
         
    Stream<String> lines = Files.lines(path);
    String data = lines.collect(Collectors.joining("\n"));
    lines.close();
         
    Assert.assertEquals(expectedData, data.trim());
}
```

파일 작업과 같은 IO 채널과 함께 `Stream`을 사용하면 `close()` 메서드를 사용하여 명시적으로 스트림을 닫아야 한다.

## 5. Scanner로 읽기
`Scanner` 클래스는 콘솔에서 콘텐츠를 읽을 때 또는 콘텐츠에 알려진 구분 기호(예: 공백으로 구분된 정수 목록)가 있는 기본 값이 포함 된 경우에 유용하다.

```java
@Test
public void whenReadWithScanner_thenCorrect()
  throws IOException {
    String file = "src/test/resources/fileTest.txt";
    Scanner scanner = new Scanner(new File(file));
    scanner.useDelimiter(" ");

    assertTrue(scanner.hasNext());
    assertEquals("Hello,", scanner.next());
    assertEquals("world!", scanner.next());

    scanner.close();
}
```

## 6. StreamTokenizer로 읽기
`StreamTokenizer` 방식은 입력 스트림을 토큰으로 구문 분석하는데 유용하다.

* `tokenizer.nval`: 유형이 숫자인 경우

* `tokenizer.sval`: 유형이 문자열인 경우

```text
Hello 1
```

```java
@Test
public void whenReadWithStreamTokenizer_thenCorrectTokens()
  throws IOException {
    String file = "src/test/resources/fileTestTokenizer.txt";
   FileReader reader = new FileReader(file);
    StreamTokenizer tokenizer = new StreamTokenizer(reader);

    // token 1
    tokenizer.nextToken();
    assertEquals(StreamTokenizer.TT_WORD, tokenizer.ttype);
    assertEquals("Hello", tokenizer.sval);

    // token 2    
    tokenizer.nextToken();
    assertEquals(StreamTokenizer.TT_NUMBER, tokenizer.ttype);
    assertEquals(1, tokenizer.nval, 0.0000001);

    // token 3
    tokenizer.nextToken();
    assertEquals(StreamTokenizer.TT_EOF, tokenizer.ttype);
    reader.close();
}
```

## 7. DataInputStream으로 읽기
`DataInputStream`을 사용 하여 파일에서 이진 또는 기본 데이터 유형을 읽을 수 있다.

```java
@Test
public void whenReadWithDataInputStream_thenCorrect() throws IOException {
    String expectedValue = "Hello, world!";
    String file ="src/test/resources/fileTest.txt";
    String result = null;

    DataInputStream reader = new DataInputStream(new FileInputStream(file));
    int nBytesToRead = reader.available();
    if(nBytesToRead > 0) {
        byte[] bytes = new byte[nBytesToRead];
        reader.read(bytes);
        result = new String(bytes);
    }

    assertEquals(expectedValue, result);
}
```

## 8. FileChannel로 읽기
대용량 파일을 읽는 경우 `FileChannel`이 표준 IO보다 빠를 수 있다.

```java
@Test
public void whenReadWithFileChannel_thenCorrect()
  throws IOException {
    String expected_value = "Hello, world!";
    String file = "src/test/resources/fileTest.txt";
    RandomAccessFile reader = new RandomAccessFile(file, "r");
    FileChannel channel = reader.getChannel();

    int bufferSize = 1024;
    if (bufferSize > channel.size()) {
        bufferSize = (int) channel.size();
    }
    ByteBuffer buff = ByteBuffer.allocate(bufferSize);
    channel.read(buff);
    buff.flip();
    
    assertEquals(expected_value, new String(buff.array()));
    channel.close();
    reader.close();
}
```

## [출처 및 참고]
* [https://www.baeldung.com/reading-file-in-java](https://www.baeldung.com/reading-file-in-java)
