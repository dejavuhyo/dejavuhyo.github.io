---
title: Java Multi Dimensional ArrayList
author: dejavuhyo
date: 2023-02-24 10:00:00 +0900
categories: [Language, Java]
tags: [java-multi-dimensional-arraylist, arraylist, multi-dimensional-arraylist, dimensional-arraylist, 다차원-arraylist, 자바-list]
---

## 1. 2차원 ArrayList
0에서 2까지 번호가 매겨진 3개의 꼭지점이 있는 그래프를 표현하려고 한다고 가정한다. 또한 그래프에 `(0, 1)`, `(1, 2)` 및 `(2, 0)` 3개의 가장자리가 있다고 가정한다. 꼭지점의 가장자리를 나타낸다.

ArrayList의 ArrayList를 생성하고 채워서 2차원 ArrayList의 가장자리를 나타낼 수 있다.

먼저 새로운 2-D ArrayList를 생성한다.

```java
int vertexCount = 3;
ArrayList<ArrayList<Integer>> graph = new ArrayList<>(vertexCount);
```

다음으로 ArrayList의 각 요소를 다른 ArrayList로 초기화한다.

```java
for(int i=0; i < vertexCount; i++) {
    graph.add(new ArrayList());
}
```

마지막으로 모든 가장자리 `(0, 1)`, `(1, 2)` 및 `(2, 0)`을 2-D ArrayList에 추가할 수 있다.

```java
graph.get(0).add(1);
graph.get(1).add(2);
graph.get(2).add(0);
```

그래프가 유향 그래프가 아니라고 가정한다. 따라서 2-D ArrayList에 가장자리 `(1, 0)`, `(2, 1)` 및 `(0, 2)`도 추가해야 한다.

```java
graph.get(1).add(0);
graph.get(2).add(1);
graph.get(0).add(2);
```

그런 다음 전체 그래프를 반복하기 위해 이중 for 루프를 사용할 수 있다.

```java
int vertexCount = graph.size();
for (int i = 0; i < vertexCount; i++) {
    int edgeCount = graph.get(i).size();
    for (int j = 0; j < edgeCount; j++) {
        Integer startVertex = i;
        Integer endVertex = graph.get(i).get(j);
        System.out.printf("Vertex %d is connected to vertex %d%n", startVertex, endVertex);
    }
}
```

## 2. 3차원 ArrayList
3차원 공간을 표현하고 싶다고 가정한다. 따라서 이 3차원 공간의 각 점은 X, Y, Z의 세 좌표로 표시된다.

그 외에도 각 점에 빨간색, 녹색, 파란색 또는 노란색의 색상이 있다고 상상한다. 이제 각 점(X, Y, Z)과 해당 색상을 3차원 ArrayList로 표현할 수 있다.

단순화를 위해 `(2 x 2 x 2)` 3D 공간을 생성한다고 가정한다. 8개의 점이 있다. `(0, 0, 0)`, `(0, 0, 1)`, `(0, 1, 0)`, `(0, 1, 1)`, `(1, 0, 0)`, `(1, 0 , 1)`, `(1, 1, 0)` 및 `(1, 1, 1)`.

먼저 변수와 3-D ArrayList를 초기화한다.

```java
int x_axis_length = 2;
int y_axis_length = 2;
int z_axis_length = 2;	
ArrayList<ArrayList<ArrayList<String>>> space = new ArrayList<>(x_axis_length);
```

그런 다음 `ArrayList<ArrayList<String>>`을 사용하여 ArrayList의 각 요소를 초기화한다.

```java
for (int i = 0; i < x_axis_length; i++) {
    space.add(new ArrayList<ArrayList<String>>(y_axis_length));
    for (int j = 0; j < y_axis_length; j++) {
        space.get(i).add(new ArrayList<String>(z_axis_length));
    }
}
```

이제 공간의 점에 색상을 추가할 수 있다. 포인트 `(0, 0, 0)` 및 `(0, 0, 1)`에 빨간색을 추가한다.

```java
space.get(0).get(0).add(0,"Red");
space.get(0).get(0).add(1,"Red");
```

그런 다음 포인트 `(0, 1, 0)` 및 `(0, 1, 1)`에 대해 파란색을 설정한다.

```java
space.get(0).get(1).add(0,"Blue");
space.get(0).get(1).add(1,"Blue");
```

마찬가지로 다른 색상을 위해 공간의 점을 계속 채울 수 있다.

좌표(i, j, k)가 있는 점의 색상 정보는 다음 3-D ArrayList 요소에 저장된다.

```java
space.get(i).get(j).get(k)
```

이 예제에서 본 것처럼 공간 변수는 ArrayList이다. 또한 이 ArrayList의 각 요소는 2차원 ArrayList이다.

공간 ArrayList의 요소 인덱스는 X 좌표를 나타내는 반면 해당 인덱스에 있는 각 2-D ArrayList는 (Y, Z) 좌표를 나타낸다.

## [출처 및 참고]
* [https://www.baeldung.com/java-multi-dimensional-arraylist#arraylistofarraylist-1](https://www.baeldung.com/java-multi-dimensional-arraylist#arraylistofarraylist-1)
