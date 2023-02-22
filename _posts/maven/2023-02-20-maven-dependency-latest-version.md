---
title: Maven 종속성 최신 버전 사용
author: dejavuhyo
date: 2023-02-20 20:55:00 +0900
categories: [DevOps, Maven]
tags: [maven-dependency, maven, dependency-latest, maven-latest-version, maven-latest, 메이븐-종속성, 메이븐-최신-버전, 종속성-최신-버전, 메이븐-종속성, 메이븐, 최신-버전]
---

## 1. Maven 종속성 최신 버전
Maven 종속성을 수동으로 업그레이드하는 것은 특히 자주 릴리스 되는 라이브러리가 많은 프로젝트에서 항상 지루한 작업이다.

종속성을 자동으로 업그레이드하고, 모든 것이 여전히 제대로 작동하는지 테스트하고, 결과를 커밋하거나 롤백하는(어느 쪽이든 적절한 경우) 지속적인 통합 파이프라인을 구현할 때 매우 유용할 수 있다.

## 2. Maven 버전 범위 구문
Maven2 시절에 개발자는 수동 개입 없이 아티팩트가 업그레이드되는 버전 범위를 지정할 수 있었다.

이 구문은 여전히 유효하며 여러 프로젝트에서 사용된다.

![syntax](/assets/img/2023-02-20-maven-dependency-latest-version/syntax.png)

그런데도 가능한 경우 Versions Maven Plugin을 선호하여 이를 피해야 한다. 외부에서 구체적인 버전을 발전시키는 것이 Maven이 자체적으로 전체 작업을 처리하도록 하는 것보다 확실히 더 많은 제어를 제공하기 때문이다.

### 1) 사용되지 않는 구문
Maven2는 또한 결과를 얻기 위해 `LATEST` 및 `RELEASE`라는 두 가지 특별한 메타버전 값을 제공했다.

`LATEST`는 가능한 최신 버전을 찾고 `RELEASE`는 SNAPSHOT이 아닌 최신 버전을 목표로 한다.

실제로 일반 종속성 해결에는 여전히 절대적으로 유효하다.

그러나 이 레거시 업그레이드 방법은 CI에 재현성이 필요한 예측 불가능성을 야기했다. 따라서 플러그인 종속성 해결을 위해 더 이상 사용되지 않는다.

## 3. 버전 Maven 플러그인
[Versions Maven Plugin](https://www.mojohaus.org/versions/versions-maven-plugin/index.html)은 오늘날 버전 관리를 처리하는 사실상의 표준 방법이다.

원격 리포지토리 간의 상위 수준 비교에서 `SNAPSHOT` 버전에 대한 하위 수준의 타임스탬프 잠금에 이르기까지 방대한 목표 목록을 통해 종속성과 관련된 프로젝트의 모든 측면을 처리할 수 있다.

### 1) 테스트 케이스
테스트 케이를 정의한다.

* 하드 코딩된 버전이 포함된 3개의 RELEASE

* 속성 버전이 있는 하나의 RELEASE

* 하나의 스냅샷

```xml
<dependencies>
    <dependency>
        <groupId>commons-io</groupId>
        <artifactId>commons-io</artifactId>
        <version>2.3</version>
    </dependency>
    <dependency>
        <groupId>org.apache.commons</groupId>
        <artifactId>commons-collections4</artifactId>
        <version>4.0</version>
    </dependency>
    <dependency>
        <groupId>org.apache.commons</groupId>
        <artifactId>commons-lang3</artifactId>
        <version>3.0</version>
    </dependency>
    <dependency>
        <groupId>org.apache.commons</groupId>
        <artifactId>commons-compress</artifactId>
        <version>${commons-compress-version}</version>
    </dependency>
    <dependency>
        <groupId>commons-beanutils</groupId>
        <artifactId>commons-beanutils</artifactId>
        <version>1.9.1-SNAPSHOT</version>
    </dependency>
</dependencies>

<properties>
    <commons-compress-version>1.15</commons-compress-version>
</properties>
```

마지막으로 플러그인을 정의할 때 프로세스에서 아티팩트를 제외한다.

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>versions-maven-plugin</artifactId>
            <version>2.7</version>
            <configuration>
                <excludes>
                    <exclude>org.apache.commons:commons-collections4</exclude>
                </excludes>
            </configuration>
        </plugin>
    </plugins>
</build>
```

## 4. 사용 가능한 업데이트 표시
우선, 프로젝트를 업데이트할 수 있는지 여부와 방법을 알기 위해 작업에 적합한 도구는 다음과 같다.

```shell
mvn versions:display-dependency-updates
```

![display-dependency-updates](/assets/img/2023-02-20-maven-dependency-latest-version/display-dependency-updates.png)

프로세스에는 모든 `RELEASE` 버전이 포함되었다. 구성의 제외는 검색 프로세스가 아니라 업데이트 프로세스를 참조하기 때문에 `commons-collections4`도 포함했다.

반대로 `SNAPSHOT`은 자동 업데이트가 안전하지 않은 개발 버전이기 때문에 무시했다.

## 5. 종속성 업데이트
업데이트를 처음 실행할 때 플러그인은 `pom.xml.versionsBackup`이라는 이름의 `pom.xml` 백업을 생성한다.

모든 반복이 `pom.xml`을 변경하는 동안 백업 파일은 사용자가 전체 프로세스를 커밋(`mvnversions:commit`을 통해 )하거나 되돌리거나(`mvnversions:revert`를 통해) 순간까지 프로젝트의 원래 상태를 보존한다.

### 1) SNAPSHOT을 RELEASE로 변환
프로젝트에 `SNAPSHOT`(아직 개발 중인 버전)이 포함되는 경우가 있다.

우리는 `version:use-releases`를 사용하여 해당 `RELEASE`가 게시되었는지 확인하고 동시에 `SNAPSHOT`을 해당 `RELEASE`로 변환할 수 있다.

```shell
mvn versions:use-releases
```

![use-releases](/assets/img/2023-02-20-maven-dependency-latest-version/use-releases.png)

### 2) 다음 릴리스로 업데이트
`Versions:use-next-releases`를 사용하여 `SNAPSHOT`이 아닌 모든 종속성을 가장 가까운 버전으로 포팅할 수 있다.

```shell
mvn versions:use-next-releases
```

![use-next-releases](/assets/img/2023-02-20-maven-dependency-latest-version/use-next-releases.png)

플러그인이 더 이상 `SNAPSHOT`이 아닌 `commons-io`, `commons-lang3`, 심지어 `commons-beanutils`까지 다음 버전으로 업데이트되었음을 분명히 알 수 있다.

가장 중요한 것은 플러그인 구성에서 제외된 `commons-collections4`와 속성을 통해 동적으로 지정된 버전 번호가 있는 `commons-compress`를 무시했다는 것이다.

### 3) 최신 RELEASE로 업데이트
모든 `non-SNAPSHOT` 종속성을 최신 릴리스로 업데이트하는 것은 동일한 방식으로 작동하며 단순히 목표를 `versions:use-latest-releases`로 변경한다.

```shell
mvn versions:use-latest-releases
```

![use-latest-releases](/assets/img/2023-02-20-maven-dependency-latest-version/use-latest-releases.png)

## 6. 원하지 않는 버전 필터링
특정 버전을 무시하려는 경우 외부 파일에서 규칙을 동적으로 로드하도록 플러그인 구성을 조정할 수 있다.

```xml
<plugin>
    <groupId>org.codehaus.mojo</groupId>
    <artifactId>versions-maven-plugin</artifactId>
    <version>2.7</version>
    <configuration>
        <rulesUri>http://www.mycompany.com/maven-version-rules.xml</rulesUri>
    </configuration>
</plugin>
```

가장 주목할 만한 점은 `<rulesUri>`가 로컬 파일을 참조할 수도 있다는 것이다.

```xml
<rulesUri>file:///home/andrea/maven-version-rules.xml</rulesUri>
```

### 1) 전역적으로 버전 무시
특정 정규 표현식과 일치하는 버전을 무시하도록 규칙 파일을 구성할 수 있다.

```xml
<ruleset comparisonMethod="maven"
  xmlns="http://mojo.codehaus.org/versions-maven-plugin/rule/2.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://mojo.codehaus.org/versions-maven-plugin/rule/2.0.0 
  http://mojo.codehaus.org/versions-maven-plugin/xsd/rule-2.0.0.xsd">
    <ignoreVersions>
        <ignoreVersion type="regex">.*-beta</ignoreVersion>
    </ignoreVersions>
</ruleset>
```

### 2) 규칙별 버전 무시
요구 사항이 더 구체적일 경우 대신 규칙 집합을 만들 수 있다.

```xml
<ruleset comparisonMethod="maven"
  xmlns="http://mojo.codehaus.org/versions-maven-plugin/rule/2.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://mojo.codehaus.org/versions-maven-plugin/rule/2.0.0 
    http://mojo.codehaus.org/versions-maven-plugin/xsd/rule-2.0.0.xsd">
    <rules>
        <rule groupId="com.mycompany.maven" comparisonMethod="maven">
            <ignoreVersions>
                <ignoreVersion type="regex">.*-RELEASE</ignoreVersion>
                <ignoreVersion>2.1.0</ignoreVersion>
            </ignoreVersions>
        </rule>
    </rules>
</ruleset>
```

## [출처 및 참고]
* [https://www.baeldung.com/maven-dependency-latest-version](https://www.baeldung.com/maven-dependency-latest-version)
