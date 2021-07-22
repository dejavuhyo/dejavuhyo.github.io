---
title: Java 메일 발송
date: 2021-07-23 06:00:00 +0900
categories: [Language, Java]
tags: [java-send-mail, java-mail, java-smtp, smtp, 자바-메일-발송, 자바-메일, 자바-smtp]
---

## 1. SMTP란
SMTP(Simple Mail Transfer Protocol)는 전자 우편을 송신하고 수신하는 데 사용되는 TCP/IP 프로토콜이다. 일반적으로 POP3 또는 IMAP(Internet Message Access Protocol)와 함께 사용되어 메시지를 서버 메일함에 저장하고 사용자를 위해 서버에서 주기적으로 메시지를 다운로드한다.

## 2. 메일 발송

### 1) SMTP 설정

* 네이버 SMTP 설정

![smtp](/assets/img/2021-07-23-java-send-mail/smtp.png)

### 2) Dependency 추가

* pom.xml

```xml
<dependency>
    <groupId>javax.mail</groupId>
    <artifactId>mail</artifactId>
    <version>1.4.7</version>
</dependency>
```

### 3) 계정 인증

* MyAuthentication.java

```java
import javax.mail.Authenticator;
import javax.mail.PasswordAuthentication;

class MyAuthentication extends Authenticator {

    PasswordAuthentication account;

    public MyAuthentication() {
        String id = "From ID";
        String pwd = "From Password";
        account = new PasswordAuthentication(id, pwd);
    }

    public PasswordAuthentication getPasswordAuthentication() {
        return account;
    }
}
```

### 4) 발송

* SendMail.java

```java
import javax.mail.*;
import javax.mail.internet.AddressException;
import javax.mail.internet.InternetAddress;
import javax.mail.internet.MimeMessage;
import java.util.Date;
import java.util.Properties;

public class SendMail {

    public void send(String title, String contents) {
        Properties prop = System.getProperties();
        prop.put("mail.smtp.starttls.enable", "true");
        prop.put("mail.smtp.host", "smtp.naver.com");
        prop.put("mail.smtp.auth", "true");
        prop.put("mail.smtp.port", "587");

        Authenticator authentication = new MyAuthentication();
        Session session = Session.getDefaultInstance(prop, authentication);
        MimeMessage msg = new MimeMessage(session);

        try {
            msg.setSentDate(new Date());
            InternetAddress internetAddress = new InternetAddress();

            internetAddress = new InternetAddress("dejavuhyo<from_email@naver.com>"); // 보내는사람
            msg.setFrom(internetAddress);

            InternetAddress ia = new InternetAddress("to_email@naver.com"); // 받는사람
            msg.setRecipient(Message.RecipientType.TO, ia);

            msg.setSubject(title, "UTF-8");
            msg.setText(contents, "UTF-8");
            msg.setHeader("content-Type", "text/html");

            javax.mail.Transport.send(msg);
        } catch (AddressException ae) {
            ae.printStackTrace();
        } catch (MessagingException me) {
            me.printStackTrace();
        }
    }

    public static void main(String[] args) {
        SendMail sendMail = new SendMail();
        sendMail.send("제목", "내용");
    }
}
```

## [출처 및 참고]
* <https://ktko.tistory.com/entry/JAVA-SMTP%EC%99%80-Mail-%EB%B0%9C%EC%86%A1%ED%95%98%EA%B8%B0Google-Naver>
