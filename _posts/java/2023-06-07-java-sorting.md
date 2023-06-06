---
title: Java Sorting
author: dejavuhyo
date: 2023-06-07 08:35:00 +0900
categories: [Language, Java]
tags: [java-sorting, sorting, 자바-정렬, 정렬]
---

## 1. Array로 정렬하기
[Arrays.sort()](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Arrays.html#sort(byte%5B%5D)) 메서드를 사용하여 정수 배열을 정렬한다.

`@Before jUnit` 메서드에서 다음 int 배열을 정의한다.

```java
@Before
public void initVariables () {
    toSort = new int[] 
      { 5, 1, 89, 255, 7, 88, 200, 123, 66 }; 
    sortedInts = new int[] 
      {1, 5, 7, 66, 88, 89, 123, 200, 255};
    sortedRangeInts = new int[] 
      {5, 1, 89, 7, 88, 200, 255, 123, 66};
    ...
}
```

### 1) 완전한 Array 정렬
간단한 `Array.sort()` API를 사용한다.

```java
@Test
public void givenIntArray_whenUsingSort_thenSortedArray() {
    Arrays.sort(toSort);

    assertTrue(Arrays.equals(toSort, sortedInts));
}
```

정렬되지 않은 배열이 완전히 정렬되었다.

```text
[1, 5, 7, 66, 88, 89, 123, 200, 255]
```

[공식 JavaDoc](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Arrays.html#sort(int%5B%5D))에서 언급했듯이 `Arrays.sort`는 프리미티브에서 이중 피벗 Quicksort를 사용한다. `O(n log(n))` 성능을 제공하며 일반적으로 기존(1피벗) Quicksort 구현보다 빠르다. 그러나 Array of Objects에 대한 mergesort 알고리즘의 안정적이고 적응적이며 반복적인 구현을 사용한다.

### 2) Array의 일부 정렬
`Arrays.sort`에는 정렬 API가 하나 더 있다.

```java
Arrays.sort(int[] a, int fromIndex, int toIndex)
```

이렇게 하면 두 인덱스 사이에서 배열의 일부만 정렬된다.

```java
@Test
public void givenIntArray_whenUsingRangeSort_thenRangeSortedArray() {
    Arrays.sort(toSort, 3, 7);
 
    assertTrue(Arrays.equals(toSort, sortedRangeInts));
}
```

정렬은 다음 하위 배열 요소에서만 수행된다(toIndex는 배타적임).

```text
[255, 7, 88, 200]
```

기본 배열을 포함하여 결과적으로 정렬된 하위 배열은 다음과 같다.

```text
[5, 1, 89, 7, 88, 200, 255, 123, 66]
```

### 3) Java 8 Arrays.sort vs Arrays.parallelSort
Java 8에는 `Arrays.sort()` API와 유사한 시그니처가 있는 새로운 API인 parallelSort가 제공된다.

```java
@Test 
public void givenIntArray_whenUsingParallelSort_thenArraySorted() {
    Arrays.parallelSort(toSort);
 
    assertTrue(Arrays.equals(toSort, sortedInts));
}
```

`parallelSort()` 이면에서 배열을 다른 하위 배열로 나눈다(parallelSort 알고리즘의 세분성에 따라). 각 하위 배열은 서로 다른 스레드에서 `Arrays.sort()`로 정렬되므로 병렬 방식으로 정렬이 실행되고 최종적으로 정렬된 배열로 병합된다.

ForJoin 공통 풀은 이러한 병렬 작업을 실행한 다음 결과를 병합하는 데 사용된다.

`Arrays.parallelSort`의 결과는 물론 `Array.sort`와 같다. 멀티 스레딩을 활용하는 문제이다.

마지막으로 `Arrays.parallelSort`에도 유사한 API `Arrays.sort` 변형이 있다.

```java
Arrays.parallelSort (int [] a, int fromIndex, int toIndex);
```

## 2. List 정렬
[java.utils.Collections](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Collections.html)에서 [Collections.sort()](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Collections.html#sort(java.util.List)) API를 사용하여 정수 목록을 정렬한다.

```java
@Test
public void givenList_whenUsingSort_thenSortedList() {
    List<Integer> toSortList = Ints.asList(toSort);
    Collections.sort(toSortList);

    assertTrue(Arrays.equals(toSortList.toArray(), 
    ArrayUtils.toObject(sortedInts)));
}
```

정렬 전 List에는 다음 요소가 포함된다.

```text
[5, 1, 89, 255, 7, 88, 200, 123, 66]
```

정렬 후

```text
[1, 5, 7, 66, 88, 89, 123, 200, 255]
```

`Collections.Sort` 용 [Oracle JavaDoc](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Collections.html#sort(java.util.List))에서 언급한 것처럼 수정된 병합 정렬을 사용하고 보장된 `n log(n)` 성능을 제공한다.

## 3. Set 정렬
[Collections.sort()](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Collections.html#sort(java.util.List))를 사용하여 LinkedHashSet을 정렬한다.

삽입 순서를 유지하기 때문에 LinkedHashSet을 사용하고 있다.

Collections에서 정렬 API를 사용하기 위해 먼저 **set을 list로 래핑한다.**

```java
@Test
public void givenSet_whenUsingSort_thenSortedSet() {
    Set<Integer> integersSet = new LinkedHashSet<>(Ints.asList(toSort));
    Set<Integer> descSortedIntegersSet = new LinkedHashSet<>(
      Arrays.asList(new Integer[] 
        {255, 200, 123, 89, 88, 66, 7, 5, 1}));
        
    List<Integer> list = new ArrayList<Integer>(integersSet);
    Collections.sort(Comparator.reverseOrder());
    integersSet = new LinkedHashSet<>(list);
        
    assertTrue(Arrays.equals(
      integersSet.toArray(), descSortedIntegersSet.toArray()));
}
```

`Comparator.reverseOrder()` 메서드는 자연 순서에 의해 부과된 순서를 반대로 바꾼다.

## 4. Map 정렬
키와 값을 기준으로 맵을 정렬하는 방법이다.

정렬할 맵을 정의한다.

```java
@Before
public void initVariables () {
    ....
    HashMap<Integer, String> map = new HashMap<>();
    map.put(55, "John");
    map.put(22, "Apple");
    map.put(66, "Earl");
    map.put(77, "Pearl");
    map.put(12, "George");
    map.put(6, "Rocky");
    ....
}
```

### 1) 키로 Map 정렬
HashMap에서 키와 값 항목을 추출 하고 이 예제의 키 값을 기준으로 정렬한다.

```java
@Test
public void givenMap_whenSortingByKeys_thenSortedMap() {
    Integer[] sortedKeys = new Integer[] { 6, 12, 22, 55, 66, 77 };

    List<Map.Entry<Integer, String>> entries 
      = new ArrayList<>(map.entrySet());
    Collections.sort(entries, new Comparator<Entry<Integer, String>>() {
        @Override
        public int compare(
          Entry<Integer, String> o1, Entry<Integer, String> o2) {
            return o1.getKey().compareTo(o2.getKey());
        }
    });
    Map<Integer, String> sortedMap = new LinkedHashMap<>();
    for (Map.Entry<Integer, String> entry : entries) {
        sortedMap.put(entry.getKey(), entry.getValue());
    }
        
    assertTrue(Arrays.equals(sortedMap.keySet().toArray(), sortedKeys));
}
```

키를 기준으로 정렬된 항목을 복사하는 동안 LinkedHashMap을 사용한 방법에 유의한다(HashSet이 키 순서를 보장하지 않기 때문).

정렬 전 맵

```text
[Key: 66 , Value: Earl] 
[Key: 22 , Value: Apple] 
[Key: 6 , Value: Rocky] 
[Key: 55 , Value: John] 
[Key: 12 , Value: George] 
[Key: 77 , Value: Pearl]
```

키 별로 정렬한 후의 맵

```text
[Key: 6 , Value: Rocky] 
[Key: 12 , Value: George] 
[Key: 22 , Value: Apple] 
[Key: 55 , Value: John] 
[Key: 66 , Value: Earl] 
[Key: 77 , Value: Pearl]
```

### 2) 값을 기준으로 Map 정렬
HashMap 값을 기준으로 정렬하기 위해 HashMap 항목의 값을 비교한다.

```java
@Test
public void givenMap_whenSortingByValues_thenSortedMap() {
    String[] sortedValues = new String[] 
      { "Apple", "Earl", "George", "John", "Pearl", "Rocky" };

    List<Map.Entry<Integer, String>> entries 
      = new ArrayList<>(map.entrySet());
    Collections.sort(entries, new Comparator<Entry<Integer, String>>() {
        @Override
        public int compare(
          Entry<Integer, String> o1, Entry<Integer, String> o2) {
            return o1.getValue().compareTo(o2.getValue());
        }
    });
    Map<Integer, String> sortedMap = new LinkedHashMap<>();
    for (Map.Entry<Integer, String> entry : entries) {
        sortedMap.put(entry.getKey(), entry.getValue());
    }
        
    assertTrue(Arrays.equals(sortedMap.values().toArray(), sortedValues));
}
```

정렬 전 맵

```text
[Key: 66 , Value: Earl] 
[Key: 22 , Value: Apple] 
[Key: 6 , Value: Rocky] 
[Key: 55 , Value: John] 
[Key: 12 , Value: George] 
[Key: 77 , Value: Pearl]
```

값으로 정렬한 후의 맵

```text
[Key: 22 , Value: Apple] 
[Key: 66 , Value: Earl] 
[Key: 12 , Value: George] 
[Key: 55 , Value: John] 
[Key: 77 , Value: Pearl] 
[Key: 6 , Value: Rocky]
```

## 5. 사용자 지정 개체 정렬
사용자 지정 개체로 작업한다.

```java
public class Employee implements Comparable {
    private String name;
    private int age;
    private double salary;

    public Employee(String name, int age, double salary) {
        ...
    }

    // standard getters, setters and toString
}
```

예제를 정렬하기 위해 다음 직원 배열을 사용한다.

```java
@Before
public void initVariables () {
    ....    
    employees = new Employee[] { 
      new Employee("John", 23, 5000), new Employee("Steve", 26, 6000), 
      new Employee("Frank", 33, 7000), new Employee("Earl", 43, 10000), 
      new Employee("Jessica", 23, 4000), new Employee("Pearl", 33, 6000)};
    
    employeesSorted = new Employee[] {
      new Employee("Earl", 43, 10000), new Employee("Frank", 33, 70000),
      new Employee("Jessica", 23, 4000), new Employee("John", 23, 5000), 
      new Employee("Pearl", 33, 4000), new Employee("Steve", 26, 6000)};
    
    employeesSortedByAge = new Employee[] { 
      new Employee("John", 23, 5000), new Employee("Jessica", 23, 4000), 
      new Employee("Steve", 26, 6000), new Employee("Frank", 33, 70000), 
      new Employee("Pearl", 33, 4000), new Employee("Earl", 43, 10000)};
}
```

다음 중 하나에서 사용자 지정 개체의 배열 또는 컬렉션을 정렬할 수 있다.

* 자연스러운 순서(Comparable Interface 사용)

* Comparator Interface에서 제공하는 순서

### 1) Comparable 사용
Java의 자연 순서는 주어진 배열이나 컬렉션에서 프리미티브 또는 객체가 순서대로 정렬되어야 하는 순서를 의미한다.

[java.util.Arrays](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Arrays.html) 및 [java.util.Collections](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Collections.html)에는 모두 `sort()` 메서드가 있으며 자연 순서가 **equals의 의미와 일치해야 하는 것이 좋다.**

이 예에서는 이름이 같은 직원을 같은 것으로 간주한다.

```java
@Test
public void givenEmpArray_SortEmpArray_thenSortedArrayinNaturalOrder() {
    Arrays.sort(employees);

    assertTrue(Arrays.equals(employees, employeesSorted));
}
```

현재 개체와 인수로 전달된 개체를 비교하기 위한 `compareTo()` 메서드가 있는 Comparable 인터페이스를 구현하여 요소의 자연 순서를 정의할 수 있다.

이를 명확하게 이해하기 위해 Comparable Interface를 구현하는 Employee 클래스 예이다.

```java
public class Employee implements Comparable {
    ...

    @Override
    public boolean equals(Object obj) {
        return ((Employee) obj).getName().equals(getName());
    }

    @Override
    public int compareTo(Object o) {
        Employee e = (Employee) o;
        return getName().compareTo(e.getName());
    }
}
```

일반적으로 비교 논리는 compareTo 메서드로 작성된다. 여기에서 직원 필드의 직원 순서 또는 이름을 비교하고 있다. 두 직원의 이름이 같으면 동일하다.

이제 `Arrays.sort(employees);` 위의 코드에서 호출되었으므로 이제 연령별로 직원을 정렬하는 논리와 순서가 무엇인지 알 수 있다.

```text
[("Earl", 43, 10000),("Frank", 33, 70000), ("Jessica", 23, 4000), ("John", 23, 5000),("Pearl", 33, 4000), ("Steve", 26, 6000)]
```

배열이 직원 이름별로 정렬된 것을 볼 수 있다. 이제 직원 클래스에 대한 자연스러운 순서가 되었다.

### 2) Comparator 사용
이제 익명 내부 클래스를 즉시 `Arrays.sort()` API에 전달하는 Comparator 인터페이스 구현을 사용하여 요소를 정렬한다.

```java
@Test
public void givenIntegerArray_whenUsingSort_thenSortedArray() {
    Integer [] integers = ArrayUtils.toObject(toSort);
    Arrays.sort(integers, new Comparator<Integer>() {
        @Override
        public int compare(Integer a, Integer b) {
            return Integer.compare(a, b);
        }
    });
 
    assertTrue(Arrays.equals(integers, ArrayUtils.toObject(sortedInts)));
}
```

이제 급여를 기준으로 직원을 정렬 하고 다른 비교기 구현을 전달한다.

```java
Arrays.sort(employees, new Comparator<Employee>() {
    @Override
    public int compare(Employee o1, Employee o2) {
       return Double.compare(o1.getSalary(), o2.getSalary());
    }
 });
```

급여를 기준으로 정렬된 직원 배열은 다음과 같다.

```text
[(Jessica,23,4000.0), (John,23,5000.0), (Pearl,33,6000.0), (Steve,26,6000.0), (Frank,33,7000.0), (Earl,43,10000.0)]
```

`Collections.sort()`를 유사한 방식으로 사용하여 배열에 대해 자연 또는 사용자 정의 순서로 목록 및 객체 집합을 정렬할 수 있다.

## 6. 람다로 정렬
Java 8부터 Lambdas를 사용하여 Comparator Functional Interface를 구현할 수 있다.

```java
Comparator<Integer> c  = new Comparator<>() {
    @Override
    public int compare(Integer a, Integer b) {
        return Integer.compare(a, b);
    }
}
```

동등한 구현으로 Lambda 표현식을 사용한다.

```java
Comparator<Integer> c = (a, b) -> Integer.compare(a, b);
```

테스트이다.

```java
@Test
public void givenArray_whenUsingSortWithLambdas_thenSortedArray() {
    Integer [] integersToSort = ArrayUtils.toObject(toSort);
    Arrays.sort(integersToSort, (a, b) -> {
        return Integer.compare(a, b);
    });
 
    assertTrue(Arrays.equals(integersToSort, 
      ArrayUtils.toObject(sortedInts)));
}
```

여기에 훨씬 더 깨끗하고 간결한 논리가 있다.

## 7. Comparator.comparing과 Comparator.thenComparing 사용
Java 8에는 정렬에 유용한 두 가지 새로운 API인 Comparator 인터페이스의 `comparing()` 및 `thenComparing()`이 함께 제공된다.

이들은 Comparator의 여러 조건을 연결하는데 매우 편리하다.

Employee를 나이와 이름으로 비교하려는 시나리오이다.

```java
@Test
public void givenArrayObjects_whenUsingComparing_thenSortedArrayObjects() {
    List<Employee> employeesList = Arrays.asList(employees);
    employees.sort(Comparator.comparing(Employee::getAge));

    assertTrue(Arrays.toString(employees.toArray())
      .equals(sortedArrayString));
}
```

이 예에서 `Employee::getAge`는 비교 기능이 있는 기능 인터페이스를 구현하는 Comparator 인터페이스의 정렬 키이다.

정렬 후 직원 배열은 다음과 같다.

```text
[(John,23,5000.0), (Jessica,23,4000.0), (Steve,26,6000.0), (Frank,33,7000.0), (Pearl,33,6000.0), (Earl,43,10000.0)]
```

여기서 직원은 연령을 기준으로 정렬된다.

John과 Jessica가 같은 나이임을 알 수 있다. 즉, 주문 논리가 이제 그들의 이름을 고려해야 한다는 것을 의미한다. `thenComparing()`으로 이를 달성할 수 있다.

```java
... 
employees.sort(Comparator.comparing(Employee::getAge)
  .thenComparing(Employee::getName)); 
...
```

위의 코드 스니펫으로 정렬한 후 직원 배열의 요소는 다음과 같이 정렬된다.

```text
[(Jessica,23,4000.0), 
 (John,23,5000.0), 
 (Steve,26,6000.0), 
 (Frank,33,7000.0), 
 (Pearl,33,6000.0), 
 (Earl,43,10000.0)
]
```

따라서 `comparing()` 및 `thenComparing()`은 확실히 더 복잡한 정렬 시나리오를 훨씬 더 깔끔하게 구현한다.

## [출처 및 참고]
* [https://www.baeldung.com/java-sorting](https://www.baeldung.com/java-sorting)
