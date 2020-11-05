---
title: Eclipse rdf4j 설치 및 실행
author: Hyosik
date: 2020-01-22 17:00:00
categories: [Database, GraphDatabase]
tags: [rdf4j, eclipse_rdf4j, rdf4j_install, rdf4j_setting, rdf4j_설치, rdf4j_설정, triple_store, triple_repository]
---

## 1. Eclipse rdf4j란?
Eclipse RDF4J (이전의 OpenRDF Sesame)는 RDF 데이터를 저장, 조회 및 분석하기 위한 오픈 소스 프레임워크이다. 네덜란드 소프트웨어 회사 Aduna가 1999년부터 2002년까지 실행한 시맨틱 웹 프로젝트인 "On-To-Knowledge"의 일부이다.

in-memory triplestore와 on-disk triplestore가 개별 서블릿 패키지와 함께 포함되어 있는데, permanent server에서 triplestores의 manage와 access를 제공하는데 사용된다.

RDF4J Rio (RDF 입력 / 출력) 패키지에는 Java 기반 RDF 파서 및 작성을 위한 간단한 API가 포함되어 있다. RDF4J는 SPARQL 및 SeRQL의 두 가지 쿼리 언어를 지원한다.

![rdf4j-logo-orange-114](/assets/img/2020-01-22-eclipse-rdf4j/rdf4j-logo-orange-114.png)

Eclipse rdf4j is a powerful Java framework for processing and handling RDF data. This includes creating, parsing, scalable storage, reasoning and querying with RDF and Linked Data. It offers an easy-to-use API that can be connected to all leading RDF database solutions. It allows you to connect with SPARQL endpoints and create applications that leverage the power of linked data and Semantic Web.

## 2. Core Database
rdf4j는 즉시 사용할 수 있는 일련의 데이터베이스를 제공한다.

### 1) Memory Store

* Transactional RDF database using main memory with optional persistent sync to disk.
* It is fast with excellent performance for small datasets.
* It scales with amount of RAM available.

### 2) Native Store

* Ttransactional RDF database using direct disk IO for persistence.
* It is a more scalable solution than the memory store, with a smaller memory footprint, and also offers better consistency and durability.
* It is currently aimed at medium-sized datasets in the order of 100 million triples.

### 3) ElasticsearchStore

* Experimental RDF database that uses Elasticsearch for storage.
* This is useful if you are already using Elasticsearch for other things in your project and you want to add some small scale graph data.
* A good usecase is if you need reference data or an ontology for your application.
* The built-in read cache makes it a good choice for data that updates infrequently, though for most usecases the NativeStore will be considerably faster.

이러한 핵심 데이터베이스 외에도 rdf4j는 다양한 기능 확장을 제공한다. 이러한 확장 기능은 향상된 전체 텍스트 검색, RDFS 추론, SHACL/SPIN을 사용한 규칙 기반 추론 및 지리 공간 쿼리 지원과 같은 기능을 확장한다.

## 3. rdf4j 설치 및 실행
rdf4j는 war 파일로 배포되기 때문에 apache-tomcat의 webapps 폴더에 war 파일만 넣으면 rdf4j 실행이 가능하다.

### 1) apache-tomcat 다운로드

* [Apache Tomcat](http://tomcat.apache.org)

### 2) rdf4j 다운로드

* [Eclipse rdf4j](https://rdf4j.org/download)

![img001](/assets/img/2020-01-22-eclipse-rdf4j/img001.png)

### 3) rdf4j-server.war 및 rdf4j-workbench.war 파일을 apache-tomcat의 webapps 폴더로 이동

![img002](/assets/img/2020-01-22-eclipse-rdf4j/img002.png)

### 4) apacht-tomcat 실행

* webapps 폴더에 rdf4j-server 및 rdf4j-workbench 폴더가 생성됨

![img003](/assets/img/2020-01-22-eclipse-rdf4j/img003.png)

### 5) rdf4j-server 및 rdf4j-workbench 접속

* <http://localhost:8080/rdf4j-server>
* <http://localhost:8080/rdf4j-workbench>

![img004](/assets/img/2020-01-22-eclipse-rdf4j/img004.png)

![img005](/assets/img/2020-01-22-eclipse-rdf4j/img005.png)

## 4. Application directory configuration
The Rdf4j Server and Workbench store configuration files and repository data in a single directory (with subdirectories). On Windows machines, this directory is `%APPDATA%\Rdf4j\` by default, where `%APPDATA%` is the application data directory of the user that runs the application. For example, in case the application runs under the ‘LocalService’ user account on Windows XP, the directory is `C:\Documents and Settings\LocalService\Application Data\Rdf4j\`. On Linux/UNIX, the default location is `$HOME/.Rdf4j/`, for example `/home/tomcat/.rdf4j/`. We will refer to this data directory as `[Rdf4j_DATA]` in the rest of this manual.

The location of this data directory can be reconfigured using the Java system property `org.eclipse.rdf4j.appdata.basedir`. When you are using Tomcat as the servlet container then you can set this property using the `JAVA_OPTS` parameter, for example:

```bat
set JAVA_OPTS=-Dorg.eclipse.rdf4j.appdata.basedir=\path\to\other\dir\ (on Windows)
```

```sh
export JAVA_OPTS='-Dorg.eclipse.rdf4j.appdata.basedir=/path/to/other/dir/' (on Linux/UNIX)
```

If you are using Apache Tomcat as a Windows Service you should use the Windows Services configuration tool to set this property. Other users can either edit the Tomcat startup script or set the property some other way.

One easy way to find out what the directory is in a running instance of the Rdf4j Server, is to go to `http://localhost:8080/rdf4j-server/home/overview.view` in your browser and click on ‘System’ in the navigation menu on the left. The data directory will be listed as one of the configuration settings of the current server.

## 출처 및 참고
* <https://rdf4j.org>
* <https://en.wikipedia.org/wiki/RDF4J>
