---
title: Java 메일 발송 TLS 오류
date: 2021-10-21 06:00:00 +0900
categories: [Language, Java]
tags: [java-send-mail-error, java-mail-error, could-not-convert-socket-to-tls, tsl-error, java-메일-발송-오류, tls-오류]
---

## 1. 오류 메시지
원인은 서버/클라이언트간 사용하려는 SSL/TLS 버전이 맞지 않을 경우 해당 오류가 발생한다.

```text
javax.mail.MessagingException: Could not convert socket to TLS;
  nested exception is:
	javax.net.ssl.SSLHandshakeException: No appropriate protocol (protocol is disabled or cipher suites are inappropriate)
	at com.sun.mail.smtp.SMTPTransport.startTLS(SMTPTransport.java:1907)
	at com.sun.mail.smtp.SMTPTransport.protocolConnect(SMTPTransport.java:666)
	at javax.mail.Service.connect(Service.java:317)
	at javax.mail.Service.connect(Service.java:176)
	at javax.mail.Service.connect(Service.java:125)
	at javax.mail.Transport.send0(Transport.java:194)
	at javax.mail.Transport.send(Transport.java:124)
	at org.price.finance.mail.SendMail.send(SendMail.java:38)
	at org.price.finance.mail.SendMail.main(SendMail.java:48)
Caused by: javax.net.ssl.SSLHandshakeException: No appropriate protocol (protocol is disabled or cipher suites are inappropriate)
	at java.base/sun.security.ssl.HandshakeContext.<init>(HandshakeContext.java:170)
	at java.base/sun.security.ssl.ClientHandshakeContext.<init>(ClientHandshakeContext.java:98)
	at java.base/sun.security.ssl.TransportContext.kickstart(TransportContext.java:221)
	at java.base/sun.security.ssl.SSLSocketImpl.startHandshake(SSLSocketImpl.java:443)
	at java.base/sun.security.ssl.SSLSocketImpl.startHandshake(SSLSocketImpl.java:421)
	at com.sun.mail.util.SocketFetcher.configureSSLSocket(SocketFetcher.java:549)
	at com.sun.mail.util.SocketFetcher.startTLS(SocketFetcher.java:486)
	at com.sun.mail.smtp.SMTPTransport.startTLS(SMTPTransport.java:1902)
	... 8 more
```

## 2. 해결 방법
TLSv1.2를 추가 한다.

### 1) Java 설정 사용시

```java
Properties props = System.getProperties();
props.put("mail.smtp.starttls.enable", "true");
props.put("mail.smtp.host", "smtp.naver.com");
props.put("mail.smtp.auth", "true");
props.put("mail.smtp.port", "587");
props.put("mail.smtp.ssl.protocols", "TLSv1.2");
```

### 2) XML 설정 사용시

```xml
<bean id="mailSender" class="org.springframework.mail.javamail.JavaMailSenderImpl">
    <property name="host" value="smtp.naver.com" />
    <property name="port" value="587" />
    <property name="username" value="이메일주소" />
    <property name="password" value="이메일계정비밀번호" />
    <property name="javaMailProperties">
        <props>
            <prop key="mail.smtp.starttls.enable">true</prop>
            <prop key="mail.smtp.auth">true</prop>
            <prop key="mail.transport.protocol">smtp</prop>
            <prop key="mail.debug">true</prop>
            <prop key="mail.smtp.ssl.trust">smtp.naver.com</prop>
            <prop key="mail.smtp.ssl.protocols">TLSv1.2</prop>
        </props>
    </property>
</bean>
```

## [출처 및 참고]
* <https://bug41.tistory.com/128>
* <https://heestory217.tistory.com/124>
