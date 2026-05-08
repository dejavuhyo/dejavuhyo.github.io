---
title: Apache Fuseki API
author: dejavuhyo
date: 2026-05-08 15:30:00 +0900
categories: [Database, GraphDatabase]
tags: [apache-jena, jena, jena-fuseki, fuseki, graphdb, fuseki-api, jena-api, jena-fuseki-api]
---

## 1. Maven 의존성 추가

```xml
<dependency>
    <groupId>org.apache.jena</groupId>
    <artifactId>apache-jena-libs</artifactId>
    <type>pom</type>
    <version>6.0.0</version>
</dependency>
```

## 2. Indexer
온톨로지 파일(OWL, RDF, TTL 등)을 색인(Index)하거나 서버에 업로드(Insert)하는 방법이 있다.

Jena의 DatasetAccessor(GSP: Graph Store Protocol)를 사용하는 방식과 SPARQL UPDATE 쿼리를 사용하는 방식이 있다.

아래는 온톨로지 파일 전체를 데이터셋에 색인할 때 가장 빠르고 효율적인 방법이다.

```java
import org.apache.jena.rdf.model.Model;
import org.apache.jena.riot.RDFDataMgr;
import org.apache.jena.sparql.exec.http.GSP;

public class Indexer {
    public static void main(String[] args) {
        // GSP(Graph Store Protocol) 엔드포인트는 /data를 사용
        String serviceEndpoint = "http://localhost:3030/my_dataset/data";

        // 모델 읽기
        Model model = RDFDataMgr.loadModel("ontology.ttl");

        // GSP 서비스를 사용하여 전송
        GSP.service(serviceEndpoint)
                .defaultGraph()
                .POST(model.getGraph()); // POST는 추가, PUT은 덮어쓰기

        System.out.println("색인 성공");
    }
}
```

## 3. SELECT
`QueryExecution.service()` 빌더 형식을 사용한다.

```java
import org.apache.jena.query.QueryExecution;
import org.apache.jena.query.QuerySolution;
import org.apache.jena.query.ResultSet;
import org.apache.jena.rdf.model.RDFNode;

public class Select {
    public static void main(String[] args) {
        // Fuseki 서버 주소
        String serviceEndpoint = "http://localhost:3030/my_dataset/sparql";

        // SPARQL
        String queryString = """
                PREFIX ex: <http://example.org/>
                SELECT ?subject ?label
                WHERE {
                  ?subject rdfs:label ?label
                }
                LIMIT 20
                """;

        try (QueryExecution qexec = QueryExecution.service(serviceEndpoint)
                .query(queryString)
                // Fuseki 서버에 아이디/패스워드 설정한 경우
//                .httpClient(java.net.http.HttpClient.newBuilder()
//                        .authenticator(new java.net.Authenticator() {
//                            protected java.net.PasswordAuthentication getPasswordAuthentication() {
//                                return new java.net.PasswordAuthentication("admin", "password".toCharArray());
//                            }
//                        }).build())
                .build()) {

            // SELECT 결과 가져오기
            ResultSet results = qexec.execSelect();

            // 결과 루프
            while (results.hasNext()) {
                QuerySolution qs = results.nextSolution();

                // 리소스(URI)와 리터럴(값) 구분해서 가져오기
                RDFNode subject = qs.get("subject");
                RDFNode label = qs.get("label");

                System.out.println("subject: " + subject + " | label: " + label);
            }
        } catch (Exception e) {
            System.err.println("Fuseki 연결 실패: " + e.getMessage());
        }
    }
}
```

## 4. ParameterizedSparqlString
SQL의 `PreparedStatement`와 유사하게, 쿼리 템플릿에 변수를 안전하게 주입하여 SPARQL Injection 공격을 방지하고, URI나 리터럴의 복잡한 구문(따옴표 처리 등)을 라이브러리가 자동으로 해결해 준다.

```java
import org.apache.jena.query.ParameterizedSparqlString;
import org.apache.jena.query.QueryExecution;
import org.apache.jena.query.ResultSet;
import org.apache.jena.query.ResultSetFormatter;

public class ParameterizedSparqlExample {
    public static void main(String[] args) {
        // Fuseki 서버 주소
        String fusekiService = "http://localhost:3030/my_dataset/sparql";

        // 쿼리 템플릿
        ParameterizedSparqlString pss = new ParameterizedSparqlString();
        pss.setCommandText("""
                PREFIX ex: <http://example.org/>
                SELECT ?name ?age
                WHERE {
                  ?person ex:id ?id ;
                          ex:name ?name ;
                          ex:age ?age .
                }
                """);

        // 파라미터 바인딩
        pss.setLiteral("id", 12345);

        // 특정 타입의 리터럴이나 URI를 주입할 때
//         pss.setIri("person", "http://example.org/user/john");

        // 실행
        try (QueryExecution qexec = QueryExecution.service(fusekiService)
                .query(pss.asQuery()) // ParameterizedSparqlString을 Query 객체로 변환
                .build()) {

            ResultSet results = qexec.execSelect();
            ResultSetFormatter.out(System.out, results);
        }
    }
}
```

## 5. Update
데이터를 수정(INSERT, DELETE, UPDATE)할 때 사용하는 API는 `UpdateExecution`이다. `QueryExecution`과 마찬가지로 빌더(Builder) 패턴을 사용하여 직관적이고 표준적인 HTTP 통신을 지원한다.

```java
import org.apache.jena.rdf.model.Model;
import org.apache.jena.riot.RDFDataMgr;
import org.apache.jena.sparql.exec.http.GSP;

public class Update {
    public static void main(String[] args) {
        // GSP 엔드포인트는 /data를 사용
        String gspEndpoint = "http://localhost:3030/my_dataset/data";

        // 파일 읽기
        Model model = RDFDataMgr.loadModel("ontology.ttl");

        // GSP 빌더 사용 (Jena 6 정석)
        GSP.service(gspEndpoint)
                .defaultGraph()
                .PUT(model.getGraph()); // POST는 추가, PUT은 덮어쓰기

        System.out.println("색인 완료");
    }
}
```

## 6. Delete

### 1) 조건부 삭제 (SPARQL DELETE)
특정 패턴에 맞는 데이터를 삭제할 때 사용한다.

```java
import org.apache.jena.update.UpdateExecution;
import org.apache.jena.update.UpdateFactory;

public class Delete {
    public static void main(String[] args) {
        // GSP 주소는 /update 엔드포인트를 사용
        String updateEndpoint = "http://localhost:3030/my_dataset/update";

        // 조건부 삭제 쿼리 (특정 URI와 관련된 모든 데이터 삭제 예시)
        String deleteQuery = """
                PREFIX ex: <http://example.org/ns#>
                DELETE WHERE {
                  ex:subject1 ?p ?o .
                }
                """;

        try {
            UpdateExecution.service(updateEndpoint)
                    .update(UpdateFactory.create(deleteQuery))
                    .build()
                    .execute();

            System.out.println("삭제 성공");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

### 2) 그래프 전체 삭제 (GSP DELETE)
쿼리문 작성 없이 HTTP DELETE 메서드를 사용하여 특정 그래프(또는 기본 그래프)의 모든 데이터를 한 번에 삭제하는 방식이다.

```java
import org.apache.jena.sparql.exec.http.GSP;

public class GspDelete {
    public static void main(String[] args) {
        // GSP 주소는 /data 엔드포인트를 사용
        String gspEndpoint = "http://localhost:3030/my_dataset/data";

        try {
            // 기본 그래프(Default Graph)의 모든 내용을 삭제
            GSP.service(gspEndpoint)
                    .defaultGraph()
                    .DELETE();

            // 특정 Named Graph를 삭제
//            GSP.service(gspEndpoint)
//               .graphName("http://example.org/myGraph")
//               .DELETE();

            System.out.println("전체 그래프 삭제 완료");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

## 7.Export Data
Dataset Store Protocol을 사용하여 데이터셋 전체(기본 그래프 + 모든 네임드 그래프)을 export 한다.

```java
import org.apache.jena.query.Dataset;
import org.apache.jena.query.DatasetFactory;
import org.apache.jena.riot.RDFDataMgr;
import org.apache.jena.riot.RDFFormat;
import org.apache.jena.sparql.core.DatasetGraph;
import org.apache.jena.sparql.exec.http.DSP;

import java.io.FileOutputStream;

public class ExportAll {
    public static void main(String[] args) {
        // Dataset Store Protocol (DSP) 엔드포인트
        // Fuseki에서 데이터셋 전체를 다룰 때는 보통 /data 또는 /get 엔드포인트를 사용
        String dspEndpoint = "http://localhost:3030/my_dataset/data";

        try {
            // 전체 데이터셋(Named Graphs 포함) 가져오기
            // DatasetGraph 반환
            DatasetGraph datasetGraph = DSP.service(dspEndpoint).GET();

            // DatasetGraph를 Dataset으로 변환
            Dataset dataset = DatasetFactory.wrap(datasetGraph);

            // 로컬 파일로 저장 (Named Graphs를 유지하기 위해 TriG 형식 사용)
            // TriG나 N-Quads는 여러 그래프를 한 파일에 저장할 수 있음
            try (FileOutputStream out = new FileOutputStream("export_dataset.trig")) {
                RDFDataMgr.write(out, dataset, RDFFormat.TRIG);
                System.out.println("전체 데이터셋(Named Graphs 포함) 내보내기 성공!");
                System.out.println("결과 파일: export_dataset.trig");
            }
        } catch (Exception e) {
            System.err.println("데이터 내보내기 실패: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

## [출처 및 참고]
* [https://jena.apache.org/tutorials/sparql.html](https://jena.apache.org/tutorials/sparql.html)
* [https://jena.apache.org/documentation/query/index.html](https://jena.apache.org/documentation/query/index.html)
