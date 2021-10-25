---
title: 절대 디렉터리 경로 조작
author: dejavuhyo
date: 2021-10-08 09:00:00 +0900
categories: [Language, Java]
tags: [absolute-path-traversal, path-traversal, 절대-디렉터리-경로-조작, 디렉터리-경로-조작]
---

## 1. 정의
외부 입력이 파일 시스템을 조작하는 경로를 직접 제어할 수 있거나 영향을 끼치면 위험하다. 사용자 입력이 파일 시스템 작업에 사용되는 경로를 제어하는 것을 허용하면, 공격자가 응용프로그램에 치명적인 시스템 파일 또는 일반 파일에 접근하거나 변경할 가능성이 존재한다. 즉, 경로 조작을 통해서 공격자가 허용되지 않은 권한을 획득하여, 설정에 관계된 파일을 변경할 수 있거나 실행시킬 수 있다.

## 2. 안전한 코딩 기법

* 외부의 입력을 통해 파일 이름의 생성 및 접근을 허용하지 말고, 외부 입력에 따라 접근이 허용된 파일의 리스트에서 선택하도록 프로그램을 작성하는 것이 바람직하다.

## 3. 예제
다음의 예제에서는 외부의 입력(userId)으로부터 직접 파일을 생성하게 되는 경우 임의의 파일 이름을 입력받을 수 있게 되어 있어, 다른 파일에 접근이 가능하거나 의도하지 않은 공격에 노출될 수 있다.

* 안전하지 않은 코드의 예

```java
import java.io.File;
import java.io.FileInputStream;
import java.io.IOException;
import java.net.URLDecoder;
import java.sql.Connection;
import java.sql.Statement;
import java.util.Hashtable;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class DocumentService extends HttpServlet {
    private final String APPLY_STYPLE_COMMAND = "apply_style";
    private final String USER_ID_PARAM = "user_id";
    private final String STYLE_FILE_NAME_PARAM = "styple_file_name";
    private final int BUFFER_SIZE = 256;

    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String command = request.getParameter("command");

        if (command.equals(APPLY_STYPLE_COMMAND)) {
            String userId = request.getParameter(USER_ID_PARAM);
            String styleFileName = request.getParameter(STYLE_FILE_NAME_PARAM);

            String userHomePath = getUserHomeDir(userId);

            byte [] buffer = new byte [BUFFER_SIZE];
            FileInputStream inputStream = new FileInputStream(userHomePath + styleFileName);
            inputStream.read(buffer);
        }
    }
}
```

다음의 예제에서는 외부의 입력(userId)으로부터 값을 받은 후 먼저 그것을 이미 정해진 styleFileName과 매칭 시켜 실제적인 파일명으로 만들었다. 그 후 경로값과 합쳐 완전한 파일 경로로 만든 후 파일을 생성하고 있다. 이렇게 입력받은 값을 미리 정해놓은 값으로 만들어 사용하여 직접적인 외부 입력값 사용을 피하면 의도하지 않은 공격을 사전에 방지할 수 있다.

* 안전한 코드의 예

```java
import java.io.File;
import java.io.FileInputStream;
import java.io.IOException;
import java.net.URLDecoder;
import java.sql.Connection;
import java.sql.Statement;
import java.util.Hashtable;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class DocumentService extends HttpServlet {
    private final String APPLY_STYPLE_COMMAND = "apply_style";
    private final String USER_ID_PARAM = "user_id";
    private final String STYLE_NAME_PARAM = "styple_name";
    private final int BUFFER_SIZE = 256;
    private Hashtable<String, String> styleFileNames;

    public DocumentService() {
        styleFileNames = new Hashtable<String, String>();
        styleFileNames.put("Normal", "NormalStyle.cfg");
        styleFileNames.put("Classic", "ClassicStyle_1.cfg");
        styleFileNames.put("Gothic", "ClassicStyle_2.cfg");
    }
    
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String command = request.getParameter("command");

        if (command.equals(APPLY_STYPLE_COMMAND)) {
            String userId = request.getParameter(USER_ID_PARAM);
            String styleName = request.getParameter(STYLE_NAME_PARAM);

            String userHomePath = getUserHomeDir(userId);
            String styleFilePath = userHomePath + styleFileNames.get(styleName);

            byte [] buffer = new byte [BUFFER_SIZE];
            FileInputStream inputStream = new FileInputStream(userHomePath + styleName);
            inputStream.read(buffer);
        }
    }
}
```

## [출처 및 참고]
* <https://www.kisa.or.kr/public/laws/laws3_View.jsp?cPage=6&mode=view&p_No=259&b_No=259&d_No=55&ST=T&SV=>
