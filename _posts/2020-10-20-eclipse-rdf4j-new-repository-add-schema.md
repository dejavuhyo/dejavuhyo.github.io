---
title: Eclipse rdf4j Repository 생성 및 Schema 추가
author: Hyosik
date: 2020-10-20 13:00:00
categories: [Database, GraphDatabase]
tags: [rdf4j, eclipse_rdf4j, rdf4j_new_repository, triple_repository, rdf4j_add_schema, rdf4j_add_rdf, rdf4j_add_triple]
---

## 1. Repository 생성

![img001](/assets/img/2020-10-20-eclipse-rdf4j-new-repository-add-schema/img001.png)

![img002](/assets/img/2020-10-20-eclipse-rdf4j-new-repository-add-schema/img002.png)

![img003](/assets/img/2020-10-20-eclipse-rdf4j-new-repository-add-schema/img003.png)

## 2. 온톨로지 스키마 준비

* [Wine 온톨로지](https://www.w3.org/TR/2004/REC-owl-guide-20040210/wine.rdf)를 다운 받아 레파지토리에 추가 하였다.

## 3. 온톨로지 스키마 추가

### 1) rdf4j Add

![img004](/assets/img/2020-10-20-eclipse-rdf4j-new-repository-add-schema/img004.png)

### 2) API

```java
import org.eclipse.rdf4j.RDF4JException;
import org.eclipse.rdf4j.repository.Repository;
import org.eclipse.rdf4j.repository.RepositoryConnection;
import org.eclipse.rdf4j.repository.http.HTTPRepository;
import org.eclipse.rdf4j.rio.RDFFormat;

import java.io.File;
import java.io.IOException;

public class AddFile {

	public static void main(String[] args) {
		
		long startTime = System.currentTimeMillis();

		String rdf4jServer = "http://localhost:8080/rdf4j-server/";
		String repositoryID = "WineRepository";
		Repository repo = new HTTPRepository(rdf4jServer, repositoryID);

		RepositoryConnection con = null;

		File file = new File("D://Ontology/wine.rdf");
		String baseURI = "http://www.w3.org/TR/2003/PR-owl-guide-20031209/wine#";

		try {
			con = repo.getConnection();
			con.add(file, baseURI, RDFFormat.RDFXML);
		} catch (RDF4JException e) {
			e.printStackTrace();
		} catch (IOException e) {
			e.printStackTrace();
		} finally {
			con.close();
			repo.shutDown();
		}
		
		long endTime = System.currentTimeMillis();
		System.out.println("### Run Time : " + (endTime - startTime) / 1000.0 + "sec");
		System.out.println("### Finish");
	}
}
```

![img005](/assets/img/2020-10-20-eclipse-rdf4j-new-repository-add-schema/img005.png)

## 4. 결과 확인

### 1) rdf4j Query

![img006](/assets/img/2020-10-20-eclipse-rdf4j-new-repository-add-schema/img006.png)

![img007](/assets/img/2020-10-20-eclipse-rdf4j-new-repository-add-schema/img007.png)

### 2) API

```java
import org.eclipse.rdf4j.repository.Repository;
import org.eclipse.rdf4j.repository.http.HTTPRepository;
import org.eclipse.rdf4j.repository.RepositoryConnection;
import org.eclipse.rdf4j.model.Value;
import org.eclipse.rdf4j.query.TupleQuery;
import org.eclipse.rdf4j.query.TupleQueryResult;
import org.eclipse.rdf4j.query.BindingSet;
import org.eclipse.rdf4j.query.QueryLanguage;

public class SelectQuery {

	public static void main(String[] args) {

		String rdf4jServer = "http://localhost:8080/rdf4j-server/";
		String repositoryID = "WineRepository";
		Repository repo = new HTTPRepository(rdf4jServer, repositoryID);

		try (RepositoryConnection con = repo.getConnection()) {
			String queryString = "SELECT ?s ?p ?o WHERE { ?s ?p ?o }";
			TupleQuery tupleQuery = con.prepareTupleQuery(QueryLanguage.SPARQL, queryString);

			try (TupleQueryResult result = tupleQuery.evaluate()) {

				while (result.hasNext()) {
					BindingSet bindingSet = result.next();

					// System.out.println(bindingSet);

					Value s = bindingSet.getValue("s");
					Value p = bindingSet.getValue("p");
					Value o = bindingSet.getValue("o");
					//
					System.out.println("=====");
					System.out.println(s);
					System.out.println(p);
					System.out.println(o);
					System.out.println("=====");
				}
			} catch (Exception e) {
				e.printStackTrace();
			} finally {
				con.close();
			}
		} catch (Exception e) {
			e.printStackTrace();
		} finally {
			repo.shutDown();
		}
	}
}
```
