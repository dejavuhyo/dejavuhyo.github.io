---
title: JPA Repository methods
author: dejavuhyo
date: 2022-12-09 19:30:00 +0900
categories: [DevOps, JPA/Hibernate]
tags: [jpa, jpa-repository, jpa-method, repository-method, jpa-repository-method, jpa-crud, spring-data-jpa, spring-jpa, jpa-레파지토리, jpa-레파지토리-메서드, jpa-메서드]
---

## 1. Repository methods
CRUD 작업을 구현하기 위한 Repository의 기본 코드는 다음과 같다.

* Repository.java 예시

```java
import org.springframework.data.repository.CrudRepository;
import org.springframework.stereotype.Repository;
import com.tutorialspoint.entity.Employee;

@Repository
public interface EmployeeRepository extends CrudRepository<Employee, Integer>  {
}
```

## 2. Method & Description
Repository에는 기본적으로 다음 메서드가 포함되어 있다.

| Method | Description |
|:-----:|:-----:|
| `count(): long` | 사용 가능한 엔티티 수를 반환한다. |
| `delete(Employee entity): void` | 엔티티를 삭제한다. |
| `deleteAll():void` | 모든 엔티티를 삭제한다. |
| `deleteAll(Iterable<extends Employee> entities):void` | 인수로 전달된 엔티티를 삭제한다. |
| `deleteAll(Iterable<extends Integer> ids):void` | 인수로 전달된 ID를 사용하여 식별된 엔티티를 삭제한다. |
| `existsById(Integer id):boolean` | ID를 사용하여 엔티티가 있는지 확인한다. |
| `findAll():Iterable<Employee>` | 모든 엔티티를 반환한다. |
| `findAllByIds(Iterable<Integer> ids):Iterable<Employee>` | 인수로 전달된 ID를 사용하여 식별된 모든 엔티티를 반환한다. |
| `findById(Integer id):Optional<Employee>` | ID를 사용하여 식별된 엔티티를 반환한다. |
| `save(Employee entity): Employee` | 엔티티를 저장하고 업데이트된 엔티티를 반환한다. |
| `saveAll(Iterable<Employee> entities): Iterable<Employee>` | 전달된 모든 엔티티를 저장하고 업데이트된 엔티티를 반환한다. |

## [출처 및 참고]
* <https://www.tutorialspoint.com/spring_boot_jpa/spring_boot_jpa_repository_methods.htm>
