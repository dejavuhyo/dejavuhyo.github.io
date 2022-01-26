---
title: 상대 디렉터리 경로 조작
author: dejavuhyo
date: 2021-10-07 08:30:00 +0900
categories: [Language, Java]
tags: [relative-path-traversal, path-traversal, 상대-디렉터리-경로-조작, 디렉터리-경로-조작]
---

## 1. 정의
외부의 입력을 통하여 '디렉터리 경로 문자열' 생성이 필요한 경우, 외부 입력값에 대해 경로 조작에 사용될 수 있는 문자를 필터링하지 않으면, 예상 밖의 접근 제한 영역에 대한 경로 문자열 구성이 가능해져 시스템 정보누출, 서비스 장애 등을 유발할 수 있다. 즉, 경로 조작을 통해서 공격자가 허용되지 않은 권한을 획득하여, 설정에 관계된 파일을 변경할 수 있거나 실행시킬 수 있다.

## 2. 안전한 코딩 기법

* 외부의 입력이 직접 파일 이름을 생성할 수 없도록 한다. 불가피하게 직접 사용하는 경우, 다른 디렉터리의 파일에 접근할 수 없도록 `replaceAll()` 등의 메소드를 사용하여 위험 문자열(`", /, \`)을 제거하는 필터를 거치도록 한다.

* 외부의 입력을 받아들이되, 내부적인 처리는 미리 정의해놓은 데이터를 사용하도록 코딩한다. 즉, 외부에서 받아들인 데이터 중 미리 정의된 케이스를 제외하고는 모두 무시하도록 한다.

## 3. 예제
외부의 입력(name)이 삭제할 파일의 경로 설정에 사용되고 있다. 만일 공격자에 의해 name의 값으로 `../../../rootFile.txt`와 같은 값을 전달하면 의도하지 않았던 파일이 삭제되어 시스템에 악영향을 준다.

* 안전하지 않은 코드의 예

```java
public void accessFile(Properties request) {
    String name = request.getProperty("filename");
    if( name != null ) {
        File file = new File("/usr/local/tmp/" + name);
        file.delete();
    }
}
```

외부에서 입력되는 값에 대하여 널 여부를 체크하고, 외부에서 입력되는 파일 이름(name)에서 상대 경로(`/, \\, &, .` 등 특수문자)를 설정할 수 없도록 replaceAll을 이용하여 특수문자를 제거한다.

* 안전한 코드의 예

```java
public void accessFile(Properties request) {
    String name = request.getProperty("user");
    if ( name != null && !"".equals(name) ) {
        name = name.replaceAll("/", "");
        name = name.replaceAll("\\", "");
        name = name.replaceAll(".", "");
        name = name.replaceAll("&", "");
        name = name + "-report";
        File file = new File("/usr/local/tmp/" + name);
        if (file != null) file.delete();
    }
}
```

## [출처 및 참고]
* <https://www.kisa.or.kr/public/laws/laws3_View.jsp?cPage=6&mode=view&p_No=259&b_No=259&d_No=55&ST=T&SV=>
