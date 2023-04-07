---
title: org.springframework.validation.beanvalidation.OptionalValidatorFactoryBean 에러
author: dejavuhyo
date: 2022-04-05 15:30:00 +0900
categories: [Framework, Spring]
tags: [springframework-error, error, optional-validator-factory-bean-error, invocation-of-init-method-failed]
---

## 1. 오류 내용
org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'org.springframework.validation.beanvalidation.OptionalValidatorFactoryBean#0': Invocation of init method failed; nested exception is java.lang.NoClassDefFoundError: javax/xml/bind/JAXBException

```text
org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'org.springframework.validation.beanvalidation.OptionalValidatorFactoryBean#0': Invocation of init method failed; nested exception is java.lang.NoClassDefFoundError: javax/xml/bind/JAXBException
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.initializeBean(AbstractAutowireCapableBeanFactory.java:1553) ~[spring-beans-4.0.6.RELEASE.jar:4.0.6.RELEASE]
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.doCreateBean(AbstractAutowireCapableBeanFactory.java:539) ~[spring-beans-4.0.6.RELEASE.jar:4.0.6.RELEASE]
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBean(AbstractAutowireCapableBeanFactory.java:475) ~[spring-beans-4.0.6.RELEASE.jar:4.0.6.RELEASE]
	at org.springframework.beans.factory.support.AbstractBeanFactory$1.getObject(AbstractBeanFactory.java:302) ~[spring-beans-4.0.6.RELEASE.jar:4.0.6.RELEASE]
	at org.springframework.beans.factory.support.DefaultSingletonBeanRegistry.getSingleton(DefaultSingletonBeanRegistry.java:228) ~[spring-beans-4.0.6.RELEASE.jar:4.0.6.RELEASE]
	at org.springframework.beans.factory.support.AbstractBeanFactory.doGetBean(AbstractBeanFactory.java:298) ~[spring-beans-4.0.6.RELEASE.jar:4.0.6.RELEASE]
	at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:193) ~[spring-beans-4.0.6.RELEASE.jar:4.0.6.RELEASE]
	at org.springframework.beans.factory.support.DefaultListableBeanFactory.preInstantiateSingletons(DefaultListableBeanFactory.java:703) ~[spring-beans-4.0.6.RELEASE.jar:4.0.6.RELEASE]
	at org.springframework.context.support.AbstractApplicationContext.finishBeanFactoryInitialization(AbstractApplicationContext.java:760) ~[spring-context-4.0.6.RELEASE.jar:4.0.6.RELEASE]
	at org.springframework.context.support.AbstractApplicationContext.refresh(AbstractApplicationContext.java:482) ~[spring-context-4.0.6.RELEASE.jar:4.0.6.RELEASE]
	at org.springframework.web.servlet.FrameworkServlet.configureAndRefreshWebApplicationContext(FrameworkServlet.java:658) ~[spring-webmvc-4.0.6.RELEASE.jar:4.0.6.RELEASE]
	at org.springframework.web.servlet.FrameworkServlet.createWebApplicationContext(FrameworkServlet.java:624) ~[spring-webmvc-4.0.6.RELEASE.jar:4.0.6.RELEASE]
	at org.springframework.web.servlet.FrameworkServlet.createWebApplicationContext(FrameworkServlet.java:672) ~[spring-webmvc-4.0.6.RELEASE.jar:4.0.6.RELEASE]
	at org.springframework.web.servlet.FrameworkServlet.initWebApplicationContext(FrameworkServlet.java:543) ~[spring-webmvc-4.0.6.RELEASE.jar:4.0.6.RELEASE]
	at org.springframework.web.servlet.FrameworkServlet.initServletBean(FrameworkServlet.java:484) ~[spring-webmvc-4.0.6.RELEASE.jar:4.0.6.RELEASE]
	at org.springframework.web.servlet.HttpServletBean.init(HttpServletBean.java:136) ~[spring-webmvc-4.0.6.RELEASE.jar:4.0.6.RELEASE]
	at javax.servlet.GenericServlet.init(GenericServlet.java:158) ~[servlet-api.jar:4.0.FR]
	at org.apache.catalina.core.StandardWrapper.initServlet(StandardWrapper.java:1164) ~[catalina.jar:9.0.62]
	at org.apache.catalina.core.StandardWrapper.loadServlet(StandardWrapper.java:1117) ~[catalina.jar:9.0.62]
	at org.apache.catalina.core.StandardWrapper.load(StandardWrapper.java:1010) ~[catalina.jar:9.0.62]
	at org.apache.catalina.core.StandardContext.loadOnStartup(StandardContext.java:4957) ~[catalina.jar:9.0.62]
	at org.apache.catalina.core.StandardContext.startInternal(StandardContext.java:5264) ~[catalina.jar:9.0.62]
	at org.apache.catalina.util.LifecycleBase.start(LifecycleBase.java:183) ~[catalina.jar:9.0.62]
	at org.apache.catalina.core.ContainerBase.addChildInternal(ContainerBase.java:726) ~[catalina.jar:9.0.62]
	at org.apache.catalina.core.ContainerBase.addChild(ContainerBase.java:698) ~[catalina.jar:9.0.62]
	at org.apache.catalina.core.StandardHost.addChild(StandardHost.java:696) ~[catalina.jar:9.0.62]
	at org.apache.catalina.startup.HostConfig.manageApp(HostConfig.java:1783) ~[catalina.jar:9.0.62]
	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Native Method) ~[na:na]
	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62) ~[na:na]
	at java.base/jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43) ~[na:na]
	at java.base/java.lang.reflect.Method.invoke(Method.java:566) ~[na:na]
	at org.apache.tomcat.util.modeler.BaseModelMBean.invoke(BaseModelMBean.java:293) ~[tomcat-coyote.jar:9.0.62]
	at java.management/com.sun.jmx.interceptor.DefaultMBeanServerInterceptor.invoke(DefaultMBeanServerInterceptor.java:809) ~[na:na]
	at java.management/com.sun.jmx.mbeanserver.JmxMBeanServer.invoke(JmxMBeanServer.java:801) ~[na:na]
	at org.apache.catalina.mbeans.MBeanFactory.createStandardContext(MBeanFactory.java:460) ~[catalina.jar:9.0.62]
	at org.apache.catalina.mbeans.MBeanFactory.createStandardContext(MBeanFactory.java:408) ~[catalina.jar:9.0.62]
	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Native Method) ~[na:na]
	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62) ~[na:na]
	at java.base/jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43) ~[na:na]
	at java.base/java.lang.reflect.Method.invoke(Method.java:566) ~[na:na]
	at org.apache.tomcat.util.modeler.BaseModelMBean.invoke(BaseModelMBean.java:293) ~[tomcat-coyote.jar:9.0.62]
	at java.management/com.sun.jmx.interceptor.DefaultMBeanServerInterceptor.invoke(DefaultMBeanServerInterceptor.java:809) ~[na:na]
	at java.management/com.sun.jmx.mbeanserver.JmxMBeanServer.invoke(JmxMBeanServer.java:801) ~[na:na]
	at java.management/com.sun.jmx.remote.security.MBeanServerAccessController.invoke(MBeanServerAccessController.java:468) ~[na:na]
	at java.management.rmi/javax.management.remote.rmi.RMIConnectionImpl.doOperation(RMIConnectionImpl.java:1466) ~[na:na]
	at java.management.rmi/javax.management.remote.rmi.RMIConnectionImpl$PrivilegedOperation.run(RMIConnectionImpl.java:1307) ~[na:na]
	at java.base/java.security.AccessController.doPrivileged(Native Method) ~[na:na]
	at java.management.rmi/javax.management.remote.rmi.RMIConnectionImpl.doPrivilegedOperation(RMIConnectionImpl.java:1406) ~[na:na]
	at java.management.rmi/javax.management.remote.rmi.RMIConnectionImpl.invoke(RMIConnectionImpl.java:827) ~[na:na]
	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Native Method) ~[na:na]
	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62) ~[na:na]
	at java.base/jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43) ~[na:na]
	at java.base/java.lang.reflect.Method.invoke(Method.java:566) ~[na:na]
	at java.rmi/sun.rmi.server.UnicastServerRef.dispatch(UnicastServerRef.java:359) ~[na:na]
	at java.rmi/sun.rmi.transport.Transport$1.run(Transport.java:200) ~[na:na]
	at java.rmi/sun.rmi.transport.Transport$1.run(Transport.java:197) ~[na:na]
	at java.base/java.security.AccessController.doPrivileged(Native Method) ~[na:na]
	at java.rmi/sun.rmi.transport.Transport.serviceCall(Transport.java:196) ~[na:na]
	at java.rmi/sun.rmi.transport.tcp.TCPTransport.handleMessages(TCPTransport.java:562) ~[na:na]
	at java.rmi/sun.rmi.transport.tcp.TCPTransport$ConnectionHandler.run0(TCPTransport.java:796) ~[na:na]
	at java.rmi/sun.rmi.transport.tcp.TCPTransport$ConnectionHandler.lambda$run$0(TCPTransport.java:677) ~[na:na]
	at java.base/java.security.AccessController.doPrivileged(Native Method) ~[na:na]
	at java.rmi/sun.rmi.transport.tcp.TCPTransport$ConnectionHandler.run(TCPTransport.java:676) ~[na:na]
	at java.base/java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1128) ~[na:na]
	at java.base/java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:628) ~[na:na]
	at java.base/java.lang.Thread.run(Thread.java:834) ~[na:na]
Caused by: java.lang.NoClassDefFoundError: javax/xml/bind/JAXBException
	at java.base/java.lang.Class.forName0(Native Method) ~[na:na]
	at java.base/java.lang.Class.forName(Class.java:398) ~[na:na]
	at org.jboss.logging.Logger.getMessageLogger(Logger.java:2248) ~[jboss-logging-3.1.1.GA.jar:3.1.1.GA]
	at org.jboss.logging.Logger.getMessageLogger(Logger.java:2214) ~[jboss-logging-3.1.1.GA.jar:3.1.1.GA]
	at org.hibernate.validator.internal.util.logging.LoggerFactory.make(LoggerFactory.java:29) ~[hibernate-validator-5.0.1.Final.jar:5.0.1.Final]
	at org.hibernate.validator.internal.util.Version.<clinit>(Version.java:27) ~[hibernate-validator-5.0.1.Final.jar:5.0.1.Final]
	at org.hibernate.validator.internal.engine.ConfigurationImpl.<clinit>(ConfigurationImpl.java:65) ~[hibernate-validator-5.0.1.Final.jar:5.0.1.Final]
	at org.hibernate.validator.HibernateValidator.createGenericConfiguration(HibernateValidator.java:41) ~[hibernate-validator-5.0.1.Final.jar:5.0.1.Final]
	at javax.validation.Validation$GenericBootstrapImpl.configure(Validation.java:276) ~[validation-api-1.1.0.Final.jar:na]
	at org.springframework.validation.beanvalidation.LocalValidatorFactoryBean.afterPropertiesSet(LocalValidatorFactoryBean.java:223) ~[spring-context-4.0.6.RELEASE.jar:4.0.6.RELEASE]
	at org.springframework.validation.beanvalidation.OptionalValidatorFactoryBean.afterPropertiesSet(OptionalValidatorFactoryBean.java:40) ~[spring-context-4.0.6.RELEASE.jar:4.0.6.RELEASE]
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.invokeInitMethods(AbstractAutowireCapableBeanFactory.java:1612) ~[spring-beans-4.0.6.RELEASE.jar:4.0.6.RELEASE]
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.initializeBean(AbstractAutowireCapableBeanFactory.java:1549) ~[spring-beans-4.0.6.RELEASE.jar:4.0.6.RELEASE]
	... 65 common frames omitted
Caused by: java.lang.ClassNotFoundException: javax.xml.bind.JAXBException
	at org.apache.catalina.loader.WebappClassLoaderBase.loadClass(WebappClassLoaderBase.java:1412) ~[catalina.jar:9.0.62]
	at org.apache.catalina.loader.WebappClassLoaderBase.loadClass(WebappClassLoaderBase.java:1220) ~[catalina.jar:9.0.62]
	... 78 common frames omitted
```

## 2. 해결방법
`pom.xml` 파일에 dependency를 추가하면 오류가 해결된다. 오류 해결이 되지 않으면, 다른 버전을 적용한다.

* [hibernate-entitymanager](https://mvnrepository.com/artifact/org.hibernate/hibernate-entitymanager)

* [hibernate-validator](https://mvnrepository.com/artifact/org.hibernate/hibernate-validator)

```xml
<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-entitymanager</artifactId>
    <version>3.4.0.GA</version>
</dependency>

<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-validator</artifactId>
    <version>4.0.2.GA</version>
</dependency>
```

## [출처 및 참고]
* [https://junghn.tistory.com/entry/SPRING-%EC%98%A4%EB%A5%98-%ED%95%B4%EA%B2%B0-%EB%B0%A9%EB%B2%95-Error-creating-bean-with-name-orgspringframeworkvalidationbeanvalidationOptionalValidatorFactoryBean0](https://junghn.tistory.com/entry/SPRING-%EC%98%A4%EB%A5%98-%ED%95%B4%EA%B2%B0-%EB%B0%A9%EB%B2%95-Error-creating-bean-with-name-orgspringframeworkvalidationbeanvalidationOptionalValidatorFactoryBean0)
