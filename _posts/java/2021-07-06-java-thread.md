---
title: Java Thread
date: 2021-07-06 06:00:00 +0900
categories: [Language, Java]
tags: [thread, java-thread, 스레드, 자바-스레드]
---

## 1. 스레드(Thread)란
스레드(thread)는 어떠한 프로그램 내에서, 특히 프로세스 내에서 실행되는 흐름의 단위를 말한다.

일반적으로 한 프로그램은 하나의 스레드를 가지고 있지만, 프로그램 환경에 따라 둘 이상의 스레드를 동시에 실행할 수 있다. 이러한 실행 방식을 멀티스레드(multithread)라고 한다.

![process-thread](/assets/img/2021-07-06-java-thread/process-thread.png)

## 2. 프로세스와 스레드 비교
멀티프로세스와 멀티스레드는 양쪽 모두 여러 흐름이 동시에 진행된다는 공통점을 가지고 있다. 하지만 멀티프로세스에서 각 프로세스는 독립적으로 실행되며 각각 별개의 메모리를 차지하고 있는 것과 달리 멀티스레드는 프로세스 내의 메모리를 공유해 사용할 수 있다. 또한 프로세스 간의 전환 속도보다 스레드 간의 전환 속도가 빠르다.

멀티스레드의 다른 장점은 CPU가 여러 개일 경우에 각각의 CPU가 스레드 하나씩을 담당하는 방법으로 속도를 높일 수 있다는 것이다. 이러한 시스템에서는 여러 스레드가 실제 시간상으로 동시에 수행될 수 있기 때문이다.

멀티스레드의 단점에는 각각의 스레드 중 어떤 것이 먼저 실행될지 그 순서를 알 수 없다는 것이 있다. 예를 들어, 두 스레드가 특정 공유 변수 i의 값을 1 증가시키는 명령을 실행할 때, 다음과 같은 방식으로 수행될 수 있다.

## 3. 스레드 종류
스레드를 지원하는 주체에 따라 2가지로 나눌 수 있다.

### 1) 사용자 레벨 스레드 (User-Level Thread)
사용자 스레드는 커널 영역의 상위에서 지원되며 일반적으로 사용자 레벨의 라이브러리를 통해 구현되며, 라이브러리는 스레드의 생성 및 스케줄링 등에 관한 관리 기능을 제공한다.

동일한 메모리 영역에서 스레드가 생성 및 관리되므로 속도가 빠른 장점이 있지만, 여러 개의 사용자 스레드 중 하나의 스레드가 시스템 호출 등으로 중단되면 나머지 모든 스레드 역시 중단되는 단점이 있다.

이는 커널이 프로세스 내부의 스레드를 인식하지 못하며 해당 프로세스를 대기 상태로 전환시키기 때문이다.

### 2) 커널 레벨 스레드 (Kernel-Level Thread)
커널 스레드는 운영체제가 지원하는 스레드 기능으로 구현되며, 커널이 스레드의 생성 및 스케줄링 등을 관리한다.

스레드가 시스템 호출 등으로 중단되더라도, 커널은 프로세스 내의 다른 스레드를 중단시키지 않고 계속 실행시켜준다

다중처리기 환경에서 커널은 여러 개의 스레드를 각각 다른 처리기에 할당할 수 있다. 다만, 사용자 스레드에 비해 생성 및 관리하는 것이 느리다.

## 4. Single Thread

### 1) Thread 클래스 상속

```java
public class SingleThread extends Thread {
    int seq;

    public SingleThread(int seq) {
        this.seq = seq;
    }

    public void run() {
        System.out.println(this.seq + " thread start.");
        try {
            Thread.sleep(1000);
        } catch (Exception e) {

        }
        System.out.println(this.seq + " thread end.");
    }

    public static void main(String[] args) {
        for (int i = 0; i < 10; i++) {
            Thread t = new SingleThread(i);
            t.start();
        }
        System.out.println("main end.");
    }
}
```

### 2) Runnable 인터페이스 상속

```java
import java.util.ArrayList;

public class SingleThreadRunnable implements Runnable {
    int seq;

    public SingleThreadRunnable(int seq) {
        this.seq = seq;
    }

    public void run() {
        System.out.println(this.seq + " thread start.");
        try {
            Thread.sleep(1000);
        } catch (Exception e) {
        }
        System.out.println(this.seq + " thread end.");
    }

    public static void main(String[] args) {
        ArrayList<Thread> threads = new ArrayList<Thread>();
        for (int i = 0; i < 10; i++) {
            Thread t = new Thread(new SingleThreadRunnable(i));
            t.start();
            threads.add(t);
        }

        for (int i = 0; i < threads.size(); i++) {
            Thread t = threads.get(i);
            try {
                t.join();
            } catch (Exception e) {
            }
        }
        System.out.println("main end.");
    }
}
```

## 5. Mutil Thread

* Run

```java
public class Run {

    public static void main(String[] args) {
        MutilThread mutilThread1 = new MutilThread();
        MutilThread mutilThread2 = new MutilThread();
        Thread thread1 = new Thread(mutilThread1, "A");
        Thread thread2 = new Thread(mutilThread2, "B");

        thread1.start();
        thread2.start();

        Thread.currentThread().getName();
    }
}
```

* MutilThread

```java
public class MutilThread implements Runnable {

    int TestNum = 0;

    @Override
    public /*synchronized 하나가 끝나야 실행됨*/ void run() {
        // TODO Auto-generated method stub
        for (int i = 0; i < 10; i++) {
            if (Thread.currentThread().getName().equals("A")) {
                System.out.println("=======================");
                TestNum++;
            }
            System.out.println("ThreadName =" + Thread.currentThread().getName() + "TestNum =" + TestNum);

            try {
                Thread.sleep(500);
            } catch (InterruptedException e) {
                // TODO Auto-generated catch block
                e.printStackTrace();
            }
        }
    }
}
```

## [출처 및 참고]
* <https://ko.wikipedia.org/wiki/%EC%8A%A4%EB%A0%88%EB%93%9C_(%EC%BB%B4%ED%93%A8%ED%8C%85)>
* <https://wikidocs.net/230>
* <https://coding-factory.tistory.com/279>
