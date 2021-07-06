---
title: Java Thread Join
date: 2021-07-07 06:00:00 +0900
categories: [Language, Java]
tags: [thread-join, java-thread-join, 스레드-조인, 자바-스레드-조인, 스레드-상태제어]
---

## 1. Thread Join
Thread가 종료될 때까지 기다릴 때 Thread.join()을 사용한다.

예를 들어, Thread A는 Thread B에게 어떤 작업을 실행시키고 완료될 때까지 기다려야 할 때가 있다. 이럴 때 join()을 호출하면 A는 B가 종료될 때까지 기다린다.

## 2. 예제

* Main

```java
public class Main {

    public static void main(String[] args) {

        System.out.println("Main Start");

        ThreadTask threadTask = new ThreadTask();
        Thread thread = new Thread(threadTask);
        thread.start();

        // thread join
        try {
            thread.join();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        System.out.println("Main End");
    }
}
```

* ThreadTask

```java
public class ThreadTask implements Runnable {

    @Override
    public void run() {
        System.out.println("Thread 1");
        thread2();
    }

    public void thread2() {
        System.out.println("Thread 2");
        thread3();
    }

    public void thread3() {
        System.out.println("Thread 3");
    }
}
```

* Thread Join 미사용 결과

```text
Main Start
Main End
Thread 1
Thread 2
Thread 3
```

* Thread Join 사용 결과

```text
Main Start
Thread 1
Thread 2
Thread 3
Main End
```

## [출처 및 참고]
* <https://dpdpwl.tistory.com/13>
