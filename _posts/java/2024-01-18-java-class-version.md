---
title: Java .class 버전 찾기
author: dejavuhyo
date: 2024-01-18 14:20:00 +0900
categories: [Language, Java]
tags: [java-class, java-class-version, class-version, java-version, 자바-클래스, 클래스-버전, 자바-버전]
---

## 1. Java의 .class 버전
Java 파일이 컴파일되면 `.class` 파일이 생성된다. 어떤 경우에는 컴파일된 클래스 파일의 Java 릴리스 버전을 찾아야 한다. 각 Java 주요 릴리스는 생성되는 `.class` 파일에 대한 주요 버전을 할당한다.

이 표에서는 `.class`의 주 버전 번호를 해당 클래스 버전이 도입된 JDK 버전에 매핑하고 해당 버전 번호의 16진수 표현을 보여준다.

| Java Release | Class Major Version | Hex |
|:-----:|:-----:|
| Java SE 18 | 62 | 003e |
| Java SE 17 | 61 | 003d |
| Java SE 16 | 60 | 003c |
| Java SE 15 | 59 | 003b |
| Java SE 14 | 58 | 003a |
| Java SE 13 | 57 | 0039 |
| Java SE 12 | 56 | 0038 |
| Java SE 11 | 55 | 0037 |
| Java SE 10 | 54 | 0036 |
| Java SE 9 | 53 | 0035 |
| Java SE 8 | 52 | 0034 |
| Java SE 7 | 51 | 0033 |
| Java SE 6 | 50 | 0032 |
| Java SE 5 | 49 | 0031 |
| JDK 1.4 | 48 | 0030 |
| JDK 1.3 | 47 | 002f |
| JDK 1.2 | 46 | 002e |
| JDK 1.1 | 45 | 002d |

## 2. .class 버전  javap Command
간단한 클래스를 만들고 JDK8로 빌드한다.

```java
public class Sample {
    public static void main(String[] args) {
        System.out.println("Baeldung tutorials");
    }
}
```

클래스 파일의 버전을 식별하기 위해 Java 클래스 파일 디스어셈블러 [javap](https://www.baeldung.com/java-class-view-bytecode#javaCommandLine)를 사용할 수 있다.

javap 명령의 구문은 다음과 같다.

```shell
javap [option] [classname]
```

예를 들어 `Sample.class`의 버전을 확인해 본다.

```shell
javap -verbose Sample

//stripped output ..
..
..
Compiled from "Sample.java"
public class test.Sample
  minor version: 0
  major version: 52
..
..
```

javap 명령의 출력에서 ​​볼 수 있듯이 주 버전은 52이며 이는 JDK8임을 나타낸다.

javap는 많은 세부 정보를 제공 하지만 주요 버전에만 관심을 두고 있다.

Linux 기반 시스템의 경우 다음 명령을 사용하여 주요 버전만 얻을 수 있다.

```shell
javap -verbose Sample | grep major
```

마찬가지로 Windows 시스템의 경우 사용할 수 있는 명령은 다음과 같다.

```shell
javap -verbose Sample | findstr major
```

예에서는 주 버전인 52가 제공된다.

**이 버전 값은 해당 JDK를 사용하여 애플리케이션이 빌드되었음을 나타내지 않는다. 클래스 파일 버전은 컴파일에 사용되는 JDK와 다를 수 있다.**

예를 들어 JDK11로 코드를 빌드하면 버전 55의 `.class` 파일이 생성된다. 그러나 컴파일 중에 `-target 8`을 전달하면 `.class` 파일의 버전은 52가 된다.

## 3. .class 버전 hexdump
Hex Editor를 사용하여 버전을 확인할 수도 있다. Java 클래스 파일은 [specification](https://en.wikipedia.org/wiki/Java_class_file)을 따른다. 구조는 다음과 같다.

```text
ClassFile {
    u4             magic;
    u2             minor_version;
    u2             major_version;
    // other details
}
```

여기서 u1, u2 및 u4 유형은 각각 부호 없는 1, 2, 4바이트 정수를 나타낸다.
u4는 클래스 파일 형식을 식별하는 매직 넘버이다. 값은 `0xCAFEBABE`이고 u2가 주 버전입니다.

Linux 기반 시스템의 경우 [hexdump](https://www.baeldung.com/linux/create-hex-dump#using-hexdump) 유틸리티를 사용하여 모든 `.class` 파일을 구문 분석 할 수 있다.

```shell
> hexdump -v Sample.class
0000000 ca fe ba be 00 00 00 34 00 22 07 00 02 01 00 0b
0000010 74 65 73 74 2f 53 61 6d 70 6c 65 07 00 04 01 00
...truncated
```

예에서는 JDK8을 사용하여 컴파일했다. 첫 번째 줄의 7 및 8 인덱스는 클래스 파일의 주요 버전을 제공한다. 따라서 0034는 16진수 표현이고 JDK8은 해당 릴리스 번호이다(앞서 본 매핑 테이블에 있음).

대안으로, hexdump를 사용하여 주요 릴리스 버전을 십진수로 직접 얻을 수 있다.

```shell
> hexdump -s 7 -n 1 -e '"%d"' Sample.class
52
```

여기서 출력 52는 JDK8에 해당하는 클래스 버전이다.

## 4. jars 버전
Java 생태계의 jar 파일은 함께 묶인 클래스 파일 모음으로 구성된다. jar가 빌드되거나 컴파일된 Java 버전을 확인하려면 jar 파일을 추출하고 [javap](https://www.baeldung.com/java-class-view-bytecode#javaCommandLine) 또는 [hexdump](https://www.baeldung.com/linux/create-hex-dump#using-hexdump)를 사용하여 `.class` 파일 버전을 확인할 수 있다.

jar 파일에는 사용된 JDK에 대한 일부 헤더 정보가 포함된 [MANIFEST.MF](https://www.baeldung.com/java-jar-manifest) 파일도 있다.

예를 들어 Build-Jdk 또는 Created-By 헤더는 jar 빌드 방법에 따라 JDK 값을 저장한다.

```text
Build-Jdk: 17.0.4
```

또는

```text
Created-By: 17.0.4
```

## [출처 및 참고]
* [https://www.baeldung.com/java-find-class-version](https://www.baeldung.com/java-find-class-version)
