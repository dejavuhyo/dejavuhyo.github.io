---
title: 자원 삽입
date: 2021-09-10 06:00:00 +0900
categories: [Language, Java]
tags: [resource-injection, 자원-삽입]
---

## 1. 정의
자원 삽입(Improper Control of Resource Identifiers, Resource Injection)은 외부 입력값을 검증하지 않고 시스템 자원(resource)에 대한 식별자로 사용하는 경우, 공격자는 입력값 조작을 통해 시스템이 보호하는 자원에 임의로 접근하거나 수정할 수 있고 잘못된 입력값으로 인해 시스템 자원 사이에 충돌이 발생할 수 있다.

![resource-injection](/assets/img/2021-09-10-resource-injection/resource-injection.png)

## 2. 안전한 코딩 기법

* 외부의 입력을 자원(파일, 소켓의 포트 등) 식별자로 사용하는 경우, 적절한 검증을 거치도록 하거나 사전에 정의된 적합한 리스트에서 선택되도록 작성한다. 외부의 입력이 파일명인 경우에는 경로 순회(Directory Traversal)를 수행할 수 있는 문자를 제거한다.

## 3. 예제
다음의 예제는 안전하지 않은 코드의 예를 나타낸 것으로, 외부의 입력(service)을 소켓 번호로 그대로 사용하고 있다. 만일, 공격자가 Service No의 값으로 -2920과 같은 값을 지정하면 기존의 80 포트에서 구동되는 서비스와 충돌되어 에러가 발생할 수 있다.

* 안전하지 않은 코드의 예

```java
public void service() throws IOException {
    int def = 1000;
    ServerSocket serverSocket;
    Properties props = new Properties();
    String fileName = "file_list";
    FileInputStream in = new FileInputStream(fileName);
    props.load(in);

    // 외부에서 입력한 데이터를 받는다.
    String service = props.getProperty("Service No");
    int port = Integer.parseInt(service);

    // 외부에서 입력받은 값으로 소켓을 생성한다.
    if (port != 0)
        serverSocket = new ServerSocket(port + 3000);
    else
        serverSocket = new ServerSocket(def + 3000);
}
```

다음은 안전한 예제를 나타낸 것이다. 내부자원에 접근할 때 외부 입력받을 포트 번호와 같은 식별자로 직접 사용하는 것은 바람직하지 않으며, 꼭 필요한 경우엔 가능한 리스트를 설정하고, 해당 범위 내에서 할당되도록 작성한다.

* 안전한 코드의 예

```java
public void service() throws IOException {
    ServerSocket serverSocket;
    Properties props = new Properties();
    String fileName = "file_list";
    FileInputStream in = new FileInputStream(fileName);
    String service = "";
    if (in != null && in.available() > 0) {
        props.load(in);
        // 외부로부터 데이터를 입력받는다.
        service = props.getProperty("Service No");
    }
    // 외부의 입력을 기본적인 내용 검사를 한다.
    if ("".equals(service)) service = "8080";

    int port = Integer.parseInt(service);
    // 외부 입력에서 포트 번호를 검사한 후 리스트에서 적합한 값을 할당한다.
    switch (port) {
        case 1:
            port = 3001;
            break;
        case 2:
            port = 3002;
            break;
        case 3:
            port = 3003;
            break;
        default:
            port = 3000;
    }
    // 서버 소켓에 검사 완료된 포트를 할당한다.
    serverSocket = new ServerSocket(port);
}
```

## [출처 및 참고]
* <https://www.kisa.or.kr/public/laws/laws3_View.jsp?cPage=6&mode=view&p_No=259&b_No=259&d_No=55&ST=T&SV=>
