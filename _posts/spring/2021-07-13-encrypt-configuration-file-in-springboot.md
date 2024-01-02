---
title: Spring Boot 설정파일 암호화
author: dejavuhyo
date: 2021-07-13 05:00:00 +0900
categories: [Framework, Spring]
tags: [spring-boot-properties-encrypt, spring-boot-yml-encrypt, properties-encrypt, yml-encrypt, jasypt, java-simplified-encryption, spring-boot-설정파일-암호화, 설정파일-암호화]
---

## 1. Jasypt
Jasypt(Java Simplified Encryption)는 개발자가 암호화 작동 방식에 대한 깊은 지식 없이도 최소한의 노력으로 자신의 프로젝트에 기본 암호화 기능을 추가할 수 있도록 하는 Java 라이브러리이다.

## 2. 암호화 설정

### 1) Dependency 추가

* 설정파일 암호화

```xml
<dependency>
    <groupId>com.github.ulisesbocchio</groupId>
    <artifactId>jasypt-spring-boot-starter</artifactId>
    <version>3.0.3</version>
</dependency>
```

* 암호 알고리즘

```xml
<dependency>
    <groupId>org.bouncycastle</groupId>
    <artifactId>bcprov-jdk15on</artifactId>
    <version>1.69</version>
</dependency>
```

### 2) PBEWithMD5AndDES 알고리즘 사용

* JasyptConfigDES.java

```java
import com.ulisesbocchio.jasyptspringboot.annotation.EnableEncryptableProperties;
import org.jasypt.encryption.StringEncryptor;
import org.jasypt.encryption.pbe.PooledPBEStringEncryptor;
import org.jasypt.encryption.pbe.config.SimpleStringPBEConfig;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
@EnableEncryptableProperties
public class JasyptConfigDES {

    @Bean("jasyptEncryptor")
    public StringEncryptor stringEncryptor() {
        PooledPBEStringEncryptor encryptor = new PooledPBEStringEncryptor();
        SimpleStringPBEConfig config = new SimpleStringPBEConfig();
        config.setPassword("password"); // 암호화 키
        config.setAlgorithm("PBEWithMD5AndDES"); // 알고리즘
        config.setKeyObtentionIterations("1000");
        config.setPoolSize("1");
        config.setSaltGeneratorClassName("org.jasypt.salt.RandomSaltGenerator");
        config.setStringOutputType("base64");
        encryptor.setConfig(config);
        return encryptor;
    }
}
```

* 테스트

```java
import org.jasypt.encryption.pbe.StandardPBEStringEncryptor;

public class DESEncyptTest {

    public static void main(String[] args) {
        StandardPBEStringEncryptor pbeEnc = new StandardPBEStringEncryptor();
        pbeEnc.setAlgorithm("PBEWithMD5AndDES");
        pbeEnc.setPassword("password");

        String enc = pbeEnc.encrypt("plain_text");
        System.out.println("enc = " + enc);

        String des = pbeEnc.decrypt(enc);
        System.out.println("des = " + des);
    }
}
```


### 3) PBEWithSHA256And128BitAES-CBC-BC 알고리즘 사용
SHA256, AES128 사용을 위해 BouncyCastle 라이브러리를 사용한다. BouncyCastle은 PBE(Password Based Encryption)에 보다 많은 알고리즘을 제공해 준다.

* JasyptConfigAES.java

```java
import com.ulisesbocchio.jasyptspringboot.annotation.EnableEncryptableProperties;
import org.bouncycastle.jce.provider.BouncyCastleProvider;
import org.jasypt.encryption.StringEncryptor;
import org.jasypt.encryption.pbe.PooledPBEStringEncryptor;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
@EnableEncryptableProperties
public class JasyptConfigAES {

    @Bean("jasyptEncryptor")
    public StringEncryptor stringEncryptor() {
        PooledPBEStringEncryptor encryptor = new PooledPBEStringEncryptor();
        encryptor.setProvider(new BouncyCastleProvider());
        encryptor.setPoolSize(2);
        encryptor.setPassword("password"); // 암호화 키
        encryptor.setAlgorithm("PBEWithSHA256And128BitAES-CBC-BC"); // 알고리즘

        return encryptor;
    }
}
```

* 테스트

```java
import org.bouncycastle.jce.provider.BouncyCastleProvider;
import org.jasypt.encryption.pbe.PooledPBEStringEncryptor;

public class AESEncyptTest {

    public static void main(String[] args) {
        PooledPBEStringEncryptor encryptor = new PooledPBEStringEncryptor();
        encryptor.setProvider(new BouncyCastleProvider());
        encryptor.setPoolSize(2);
        encryptor.setPassword("password");
        encryptor.setAlgorithm("PBEWithSHA256And128BitAES-CBC-BC");

        String plainText = "plain_text";
        String encryptedText = encryptor.encrypt(plainText);
        String decryptedText = encryptor.decrypt(encryptedText);
        System.out.println("Enc = " + encryptedText);
        System.out.println("Dec = " + decryptedText);
    }
}
```

## 3. properties 파일 적용
properties 파일에 암호화 bean 이름과 암호화된 내용을 작성한다.

* application.properties

```text
jasypt.encryptor.bean=jasyptEncryptor

spring.datasource.driver-class-name=net.sf.log4jdbc.sql.jdbcapi.DriverSpy
spring.datasource.jdbc-url=ENC(OCVOlP4CAmC/49yWqP4rn/6ZKuleEtEyLJNUh5KjuJEfGzd4iGrFMShHVjoCL6GCeCK9jmArUZO/G7F0jQmsarR6TYMUwag6trEv33e3tcs=)
spring.datasource.username=ENC(MHEf37ImCLMjbioeXLqYCRpgyjUAcZAo88Nq9NbCd4I=)
spring.datasource.password=ENC(BbJAsSr4uISv+mTAw2fN+UTy2dodoDh3++YchPhw5qI=)
```

## [출처 및 참고]
* [https://www.jasypt.org/](https://www.jasypt.org/)
* [https://goateedev.tistory.com/131](https://goateedev.tistory.com/131)
* [https://elfinlas.github.io/2017/12/21/jsaypt/](https://elfinlas.github.io/2017/12/21/jsaypt/)
