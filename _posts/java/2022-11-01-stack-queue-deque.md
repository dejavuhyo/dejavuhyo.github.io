---
title: 스택 (Stack), 큐 (Queue), 덱 (Deque)
author: dejavuhyo
date: 2022-11-01 09:00:00 +0900
categories: [Language, Java]
tags: [stack, queue, deque, stack-queue-deque, linear-queue, circular-queue, 스택, 큐, 덱, 스택-큐-덱, 선형-큐, 원형-큐]
---

## 1. 스택 (Stack)
스택은 한쪽 끝에서만 자료를 넣고 빼는 작업이 이루어지는 자료구조이다. LIFO (Last In First Out) 방식으로 동작하며 가장 최근에 스택에 삽입된 자료의 위치를 top이라 한다.

![stack](/assets/img/2022-11-01-stack-queue-deque/stack.png)

스택의 stack.push는 top에 새로운 데이터를 추가하고, stack.pop은 가장 최근에 삽입된 데이터가 스택에서 삭제된다.

스택의 맨 위 요소 top에만 접근할 수 있기 때문에 top이 아닌 위치의 데이터에 대한 접근, 삽입, 삭제는 모두 불가능하다.

스택이 비어있을 때 stack.pop을 시도하는 것을 stack underflow라 하고 스택의 크기가 비어있을 때 stack.push를 시도할 때 stack overflow가 발생한다.

### 1) 시간 복잡도
top 위치의 데이터에 바로 접근할 수 있기 때문에 데이터 삽입, 삭제의 시간 복잡도는 O(1)이다.

### 2) 장단점

* top을 통해 접근하기 때문에 데이터 접근, 삽입, 삭제가 빠르다.

* top 위치 이외의 데이터에 접근할 수 없으므로 탐색할 수 없다. 탐색하려면 모든 데이터를 꺼내면서 진행해야 한다.

### 3) 활용

* 웹 브라우저 방문 기록(뒤로가기)

* 역순 문자열 만들기

* 실행 취소

* 재귀 알고리즘

* DFS 알고리즘

* 작업 실행 취소와 같은 역추적 작업이 필요할 때

* 괄호 검사, 후위 연산법, 문자열 역순 출력 등

## 2. 큐 (Queue)
큐(Queue)는 스택(Stack)과 다르게 먼저 들어온 것이 먼저 나가는 FIFO(First in First Out) 방식으로 "선입선출" 구조이다.

![queue](/assets/img/2022-11-01-stack-queue-deque/queue.png)

정해진 한곳(top)을 통해서 삽입, 삭제가 이루어지는 스택과 달리 큐는 한쪽 끝에서 삽입작업이, 다른 쪽 끝에서는 삭제 작업이 이루어진다.

삭제 연산이 수행되는 곳을 프론트(front), 삽입 연산이 이루어지는 곳을 리어(rear)라고 하며, 작업이 나뉘어 이루어진다.

이때, 큐의 리어에서 이루어지는 삽입 연산을 인큐(enQueue), 프런트에서 이루어지는 삭제 연산을 디큐(dnQueue)라고 부른다. 데이터를 삭제하기 전에는 큐가 empty 한지, 큐에 데이터를 추가하려 할 때는 큐가 full 인지 확인 후 진행해야 한다.

### 1) 선형 큐 (Linear Queue)

![linear-queue](/assets/img/2022-11-01-stack-queue-deque/linear-queue.png)

선형 배열을 사용하여 구현된 큐로, 삽입을 위해서는 계속해서 요소들을 이동시켜야 한다.

front, rear는 증가만 하는 방식으로, 실제로는 front 앞쪽에 공간이 있더라도 삽입할 수 없는 경우가 발생할 수 있다.

### 2) 원형 큐 (Circular Queue)

![circular-queue](/assets/img/2022-11-01-stack-queue-deque/circular-queue.png)

선형 큐의 단점을 보완하였다. front는 맨 첫 번째 요소 바로 앞을 가리키고 rear는 마지막 요소를 가리킨다.

큐 empty 상태는 `front == rear`이고, 큐 full 상태는 `front == (rear+1) % MAX_QUEUE_SIZE`이다. 공백 상태와 포화 상태를 구분하기 위해 하나의 공간을 비워둔다.

### 3) 시간 복잡도
큐 또한 front, rear의 위치로 데이터 삽입 삭제가 바로 이루어지기 때문에 원소 삽입, 삭제의 시간 복잡도는 O(1)이다.

### 4) 장단점

* 데이터 접근, 삽입, 삭제가 빠르다.

* 큐 역시 스택과 마찬가지로 중간에 있는 데이터에 대한 접근이 불가능하다.

### 5) 활용

* 은행 업무

* 콜센터 고객 대기시간

* 프로세스 관리

* 데이터를 입력된 순서대로 처리해야 할 때

* BFS 알고리즘

* 프로세스 관리

* 대기 순서 관리

## 3. 덱 (Deque)
Deque는 Double - Ended Queue의 줄임말이다. 한쪽에서만 삽입, 다른 한쪽에서만 삭제할 수 있었던 큐와 달리 양쪽 front, rear에서 삽입 삭제가 모두 가능한 큐이다.

![queue](/assets/img/2022-11-01-stack-queue-deque/queue.png)

연속적인 메모리를 기반으로 하는 시퀀스 컨테이너이고 선언 이후 크기를 줄이거나 늘릴 수 있는 가변적 크기를 갖는다.

또한 중간에 데이터가 삽입될 때 다른 요소들을 앞, 뒤로 밀 수 있다. vector보다는 좋은 성능을 가지지만 앞, 뒤에서의 삽입 삭제 성능에 비해 중간에 삽입 삭제하는 것은 성능이 좋지 않다.

### 1) 시간 복잡도
삽입, 삭제 연산은 O(1)의 시간 복잡도를 가지고, 스택/큐와 달리 index를 통해 요소들에 탐색할 수 있으므로 O(1)의 시간 복잡도를 가진다.

### 2) 장단점

* 데이터의 삽입 삭제가 빠르고 앞, 뒤에서 삽입 삭제가 모두 가능하다.

* 가변적 크기이다.

* index를 통해 임의 원소에 바로 접근할 수 있다.

* 새로운 원소 삽입 시, 메모리를 재 할당하고 복사하지 않고 새로운 단위의 메모리 블록을 할당하여 
삽입한다.

* 중간에서의 삽입 삭제가 어렵다.

* 스택, 큐에 비해 비교적 구현이 어렵다.

## [출처 및 참고]
* <https://velog.io/@gibeom98/%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0-%EC%8A%A4%ED%83%9D-STACK-%ED%81%90QUEUE>
* <https://velog.io/@nnnyeong/%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0-%EC%8A%A4%ED%83%9D-Stack-%ED%81%90-Queue-%EB%8D%B1-Deque>
