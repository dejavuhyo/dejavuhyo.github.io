---
title: 자바 멀티스레드
author: dejavuhyo
date: 2020-12-24 09:30:00 +0900
categories: [Language, Java]
tags: [multi-thread, thread, java-multi-thread, java-thread, 멀티-스레드, 스레드, 자바-스레드, 자바-멀티-스레드]
---

## 1. 스레드 개념
하나의 프로세스 내부에서 독립적으로 실행되는 하나의 작업 단위를 말하며, 세부적으로는 운영체제에 의해 관리되는 하나의 작업 혹은 태스크를 의미한다.

* JVM에 의해 하나의 프로세스가 발생하고 main() 안의 실행문 들이 하나의 스레드이다.

* main() 이외의 또 다른 스레드를 만들려면 Thread 클래스를 상속하거나 Runnable 인터페이스를 구현한다.

* 다중 스레드 작업 시에는 각 스레드끼리 정보를 주고받을 수 있어 처리 과정의 오류를 줄일 수 있다.

* 프로세스끼리는 정보를 주고받을 수 없다.

## 2. 멀티스레드 개념
여러 스레드를 동시에 실행시키는 응용프로그램을 작성하는 기법이다.

* 장점
  - 메모리 공유로 인한 시스템 자원 소모가 줄어든다.
  - 동시에 두 가지 이상의 활동을 하는 것이 가능해진다.

* 단점
  - 서로 자원을 소모하다가 충돌이 일어날 가능성이 존재한다.
  - 코딩이 난해해져 버그 생성확률이 높아진다.

## 3. 스레드의 생명 주기

### 1) Runnable(준비상태)
스레드가 실행되기 위한 준비단계이다. CPU를 점유하고 있지 않으며 실행(Running 상태)을 하기 위해 대기하고 있는 상태이다. 코딩 상에서 start() 메소드를 호출하면 run() 메소드에 설정된 스레드가 Runnable 상태로 진입한다. "Ready" 상태라고도 한다.

### 2) Running(실행상태)
CPU를 점유하여 실행하고 있는 상태이며 run() 메서드는 JVM만이 호출 가능하다. Runnable(준비상태)에 있는 여러 스레드 중 우선순위를 가진 스레드가 결정되면 JVM이 자동으로 run() 메소드를 호출하여 스레드가 Running 상태로 진입한다.

### 3) Dead(종료상태)
Running 상태에서 스레드가 모두 실행되고 난 후 완료 상태이다. "Done" 상태라고도 한다.

### 4) Blocked(지연상태)
CPU를 점유권을 상실한 상태이다. 특정 메서드를 실행 시켜 Runnable(준비상태)로 전환한다.
wait() 메소드에 의해 Blocked 상태가 된 스레드는 notify() 메소드가 호출되면 Runnable 상태로 된다. sleep(시간) 메소드에 의해 Blocked 상태가 된 스레드는 지정된 시간이 지나면 Runnable 상태로 된다.

## 4. Thread 적용

### 1) Single Thread(Thread 클래스 상속)

```java
public class SingleThread extends Thread {

    private int[] temp;

    public SingleThread(String threadname) {
        super(threadname);
        temp = new int[10];

        for (int i = 0; i < temp.length; i++) {
            temp[i] = i;
        }
    }

    public void run() {
        for (int i : temp) {
            try {
                Thread.sleep(1000);

            } catch (InterruptedException ie) {
                ie.printStackTrace();
            }
            System.out.println("스레드 이름: " + currentThread().getName());
            System.out.println("temp value: " + i);
        }
    }

    public static void main(String[] args) {
        SingleThread singleThread = new SingleThread("first");
        singleThread.run();
    }
}
```

### 2) Single Thread (Runnable 인터페이스 상속)

```java
public class SingleThread2 implements Runnable {

    private int[] temp;

    public SingleThread2() {
        temp = new int[10];

        for (int i = 0; i < temp.length; i++) {
            temp[i] = i;
        }
    }

    @Override
    public void run() {
        for (int i : temp) {
            try {
                Thread.sleep(1000);

            } catch (InterruptedException ie) {
                ie.printStackTrace();
            }
            System.out.println("스레드 이름: " + Thread.currentThread().getName());
            System.out.println("temp value: " + i);
        }
    }

    public static void main(String[] args) {
        SingleThread2 singleThread2 = new SingleThread2();
        Thread thread = new Thread(singleThread2, "first");
        thread.start();
    }
}
```

### 3) Mutil Thread

```java
public class MutiThread implements Runnable {

    private final String threadName;

    public MutiThread(String threadName) {
        this.threadName = threadName;
    }

    public void run() {
        for (int i = 1; i < 100; i++) {
            System.out.println(String.format("스레드 이름: " + threadName + ", 실행 횟수: " + i));
        }
    }

    public static void main(String[] args) {
        MutiThread MutiThread1 = new MutiThread("Thread1");
        MutiThread MutiThread2 = new MutiThread("Thread2");
        MutiThread MutiThread3 = new MutiThread("Thread3");

        Thread thread1 = new Thread(MutiThread1);
        Thread thread2 = new Thread(MutiThread2);
        Thread thread3 = new Thread(MutiThread3);

        thread1.start();
        thread2.start();
        thread3.start();
    }
}
```

### 4) Mutil Thread(Thread Pool)
동적으로 프로그램에 접속한 사람 수 만큼 스레드를 생성하게 되면 메모리를 모두 사용해 스레드를 생성한다. 스레드 풀을 이용해서 스레드를 생성할 때 최대 개수를 지정한다.

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;

public class MutiThreadPool implements Runnable {

    private final String threadName;

    public MutiThreadPool(String threadName) {
        this.threadName = threadName;
    }

    public void run() {
        for (int i = 1; i < 10; i++) {
            System.out.println(String.format("스레드 이름: " + threadName + ", 실행 횟수: " + i));
        }
    }

    public static void main(String[] args) {
        MutiThreadPool mutiThreadPool1 = new MutiThreadPool("Thread1");
        MutiThreadPool mutiThreadPool2 = new MutiThreadPool("Thread2");
        MutiThreadPool mutiThreadPool3 = new MutiThreadPool("Thread3");
        MutiThreadPool mutiThreadPool4 = new MutiThreadPool("Thread4");
        MutiThreadPool mutiThreadPool5 = new MutiThreadPool("Thread5");

        ExecutorService executorService = Executors.newFixedThreadPool(3);
        executorService.execute(mutiThreadPool1);
        executorService.execute(mutiThreadPool2);
        executorService.execute(mutiThreadPool3);
        executorService.execute(mutiThreadPool4);
        executorService.execute(mutiThreadPool5);

        executorService.shutdown();

        try {
            if (!executorService.awaitTermination(5, TimeUnit.MINUTES)) {
                executorService.shutdownNow();
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
            executorService.shutdownNow();
        }
    }
}
```

스레드 풀을 3개로 제한하고 Runnable 객체를 4개 이상 만든다면, 3개의 Runnable 객체가 먼저 실행되고 실행이 끝난 뒤 다음 Runnable 객체가 실행된다.

### 5) Mutil Thread(Thread Safe)
멀티스레드로 동작하는 프로그램에서 개발자가 의도한 대로 동작하는 것을 가리킨다. 전혀 다른 일을 처리할 때는 발생상황이 없으나 공유자원을 참조할 때 반드시 처리해야 한다.

```java
import java.text.DateFormat;
import java.text.SimpleDateFormat;
import java.util.Calendar;
import java.util.Date;

public class ThreadSafe {

    public static void main(String[] args) {

        //안전하지 않은 객체
        DateFormat unsafeDateFormat = new SimpleDateFormat("yyyy-MM-dd");

        Calendar calendar1 = Calendar.getInstance();
        calendar1.set(1984, Calendar.SEPTEMBER, 8); // 1984-09-08
        Date date1 = calendar1.getTime();

        Calendar calendar2 = Calendar.getInstance();
        calendar2.set(2020, Calendar.DECEMBER, 24); // 2020-12-24
        Date date2 = calendar2.getTime();

        Thread thread1 = new Thread(() -> {
            for (int i = 0; i < 100; i++) {
                try {
                    String result = unsafeDateFormat.format(date1);
                    System.out.println("Thread1: " + result);
                } catch (Exception e) {
                    e.printStackTrace();
                    break;
                }
            }
        });

        Thread thread2 = new Thread(() -> {
            for (int i = 0; i < 100; i++) {
                try {
                    String result = unsafeDateFormat.format(date2);
                    System.out.println("Thread2: " + result);
                } catch (Exception e) {
                    e.printStackTrace();
                    break;
                }
            }
        });

        thread1.start();
        thread2.start();
    }
}
```

결과를 보면 Thread1은 1984-09-08만 100번 나오고 Thread2는 2020-12-24 만 100번 나와야 한다. 그러나 1984-09-08과 2020-12-24가 섞여나온다.

이유는 SimpleDateFormat 클래스를 동시에 사용하려고 했기 때문이다.

date1의 1984-09-08를 출력하고 있는데, 갑자기 date2도 SimpleDateFormat 클래스를 이용해서 2020-12-24을 출력하려고 하기 때문이다.

* 해결 방법: synchronized 사용

```java
import java.text.DateFormat;
import java.text.SimpleDateFormat;
import java.util.Calendar;
import java.util.Date;

public class ThreadSafeSynchronized {

    public static void main(String[] args) {

        //안전하지 않은 객체
        DateFormat unsafeDateFormat = new SimpleDateFormat("yyyy-MM-dd");

        Calendar calendar1 = Calendar.getInstance();
        calendar1.set(1984, Calendar.SEPTEMBER, 8); // 1984-09-08
        Date date1 = calendar1.getTime();

        Calendar calendar2 = Calendar.getInstance();
        calendar2.set(2020, Calendar.DECEMBER, 24); // 2020-12-24
        Date date2 = calendar2.getTime();

        Thread thread1 = new Thread(() -> {
            for (int i = 0; i < 100; i++) {
                try {
                    String result;
                    synchronized (unsafeDateFormat) {
                        result = unsafeDateFormat.format(date1);
                    }
                    System.out.println("Thread1: " + result);

                } catch (Exception e) {
                    e.printStackTrace();
                    break;
                }
            }
        });

        Thread thread2 = new Thread(() -> {
            for (int i = 0; i < 100; i++) {
                try {
                    String result;
                    synchronized (unsafeDateFormat) {
                        result = unsafeDateFormat.format(date2);
                    }
                    System.out.println("Thread2: " + result);
                } catch (Exception e) {
                    e.printStackTrace();
                    break;
                }
            }
        });

        thread1.start();
        thread2.start();
    }
}
```

synchronized를 통해서 해당 인수(unsafeDateFormat)에 락을 걸어서 스레드끼리 동시에 사용하지 못하게 막은 것이다. 다른 스레드에서 unsafeDateFormat 객체를 사용하려고 할 때, 현재 사용 중인지를 확인한다. 사용 중일 경우 기다리는 시간이 있기 때문에 기존의 멀티스레드 방법보다 속도는 느리지만 정확하게 실행된다.

## [출처 및 참고]
* [https://coding-factory.tistory.com/279](https://coding-factory.tistory.com/279)
* [https://jeong-pro.tistory.com/71](https://jeong-pro.tistory.com/71)
