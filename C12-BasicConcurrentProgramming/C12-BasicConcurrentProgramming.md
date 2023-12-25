# 并发编程基础

java中线程相关的类图如下图所示，可以看到java原生提供了`Thread`类可供我们的自定义类去继承从而进行线程相关的操作，也提供了`Runnable`接口去支持我们的自定义线程类


<img width="420" alt="image" src="https://github.com/steaksunflower0107/JavaLearingAsGopher-BasePart/assets/112318617/27f2db0c-3feb-4207-be50-b3d9c90b6d60">


## 创建线程

我们可以通过两种方法创建自定义线程类:

- 继承`Thread`类，重写`run`方法

  以下示例创建了一个线程类，用于每隔1s打印一次"sleeping for a while...":

  ```java
  class CustomThread extends Thread {
      @Override
      public void run() {
          while (true) {
              try {
                  System.out.println("sleeping for a while...");
                  Thread.sleep(1000);
              } catch (InterruptedException e) {
                  throw new RuntimeException(e);
              }
          }
      }
  }
  ```

  通过`Thread`中的`start()`方法，我们即可以启动一个线程使其开始工作:

  ```java
  public static void main(String[] args) {
      CustomThread customThread = new CustomThread();
      // 启动线程
      customThread.start();
  }
  ```

  值得指出的是，`run()`方法并不会开启一个新线程

  执行后自定义的`CustomThread`类的线程会和main线程**异步**的开始执行，在使用上有些类似go中使用`go`关键字开一个goroutine，都是异步的

- 由于java是单继承继承，对于指明父类的类希望成为一个线程类，可以实现`Runnable`接口，实现`run`方法，需要注意的是该情况下**未实现start()方法**，需要借助Thread的代理:

  ```java
  class CustomThread extends SuperClass implements Runnable {
      @Override
      public void run() {
          while (true) {
              try {
                  System.out.println("sleeping for a while...");
                  Thread.sleep(1000);
              } catch (InterruptedException e) {
                  throw new RuntimeException(e);
              }
          }
      }
  }
  ```

  ```java
  public static void main(String[] args) {
      // 启动线程
      // 需要注意的是，由于是实现接口，因此自定义线程类并没有start方法，我们可以通过构造Thread对象来调用start方法
      // 这里应用了设计模式之一的"代理模式"
      Thread thread = new Thread(new CustomThread());
      thread.start();
  
      System.out.println("I'm running");
  }
  ```

实例: 创建两个自定义线程类对象交替打印:

```java
public class RunnableClass {
    public static void main(String[] args) {
        CustomThread01 customThread01 = new CustomThread01();
        CustomThread02 customThread02 = new CustomThread02();

        customThread01.start();
        customThread02.start();

        System.out.println("I'm running");
    }
}

class CustomThread01 extends Thread {
    @Override
    public void run() {
        int times = 10;
        while (times != 0) {
            try {
                System.out.println("hello, world!");
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
            times--;
        }
    }
}

class CustomThread02 extends Thread {
    @Override
    public void run() {
        int times = 5;
        while (times != 0) {
            try {
                System.out.println("hi");
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
            times--;
        }
    }
}
```

实例: 多个线程抢车票

```java
public class RunnableClass {
    public static void main(String[] args) {
        TicketSell ticketSell01 = new TicketSell();
        new Thread(ticketSell01).start();
        new Thread(ticketSell01).start();
        new Thread(ticketSell01).start();
    }
}

class TicketSell implements Runnable {
    private static int ticketNum = 20;

    @Override
    public void run() {
        while (true) {
            if (ticketNum <= 0) {
                System.out.println(Thread.currentThread().getName() + "find all tickets has been sole out");
                return;
            }
            try {
                Thread.sleep(500);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            System.out.println(Thread.currentThread().getName() + "售出一张票，目前剩余: " + --ticketNum);
        }
    }
}
```

但是如果你阅读了该代码，会发现其中未做任何并发处理，三个线程异步的去抢占同一资源，无法避免的会出现**超卖**的问题，该问题的解决在以下篇幅中**线程同步**的内容会有所启示

## Thread类常用方法

| 方法名                      | 描述                                         |
| :-------------------------- | :------------------------------------------- |
| `start()`                   | 启动线程，使线程进入可运行状态               |
| `run()`                     | 线程的主体方法，定义线程的执行逻辑           |
| `join()`                    | 等待该线程终止                               |
| `sleep(long millis)`        | 让当前线程休眠指定的毫秒数                   |
| `yield()`                   | 暂停当前正在执行的线程，并允许其他线程执行   |
| `interrupt()`               | 中断线程                                     |
| `isInterrupted()`           | 判断线程是否被中断                           |
| `currentThread()`           | 返回当前正在执行的线程对象                   |
| `isAlive()`                 | 判断线程是否处于活动状态                     |
| `setName(String name)`      | 设置线程的名称                               |
| `getName()`                 | 获取线程的名称                               |
| `setPriority(int priority)` | 设置线程的优先级                             |
| `getPriority()`             | 获取线程的优先级                             |
| `setDaemon(boolean on)`     | 将线程设置为守护线程                         |
| `isDaemon()`                | 判断线程是否为守护线程                       |
| `yield()`                   | 提示线程调度器当前线程愿意放弃对处理器的使用 |
| `getState()`                | 获取线程的状态                               |

以下是一个实现**同步**的例子，应用`join()`方法，设置一个场景: 共有两个站点，具体实现的逻辑是，设置一个主站点（main线程)以及一个副站点(自定义线程)，当主站点抢到了5张票之后，就停止抢票直到副站点完成自己的所有的抢票任务之后，主站点再继续抢票

```java
public class RunnableClass {
    public static void main(String[] args) throws InterruptedException {
        TicketSell ticketSell = new TicketSell();
        ticketSell.start();

        for (int i = 1; i <= 10; i++) {
            Thread.sleep(1000);
            System.out.println("main station has got " + i + " tickets");

            if (i == 5) {
                System.out.println("----main station stop getting ticket----");
                // 等待ticketSell线程先执行完毕
                ticketSell.join();
                System.out.println("----main station start to get ticket again----");
            }
        }
    }
}

class TicketSell extends Thread {
    @Override
    public void run() {
        for (int i = 1; i <= 10; i++) {
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("vice station has got " + i + " tickets");
        }
    }
}
```

输出

```shell
vice station has got 1 tickets
main station has got 1 tickets
vice station has got 2 tickets
main station has got 2 tickets
vice station has got 3 tickets
main station has got 3 tickets
vice station has got 4 tickets
main station has got 4 tickets
main station has got 5 tickets
vice station has got 5 tickets
----main station stop getting ticket----
vice station has got 6 tickets
vice station has got 7 tickets
vice station has got 8 tickets
vice station has got 9 tickets
vice station has got 10 tickets
----main station start to get ticket again----
main station has got 6 tickets
main station has got 7 tickets
main station has got 8 tickets
main station has got 9 tickets
main station has got 10 tickets
```

## 线程的生命周期

在java中，线程的状态由`Thread.State`枚举类表示。以下是java线程的几种常见状态：

| 状态 (State)    | 描述                                                         |
| :-------------- | :----------------------------------------------------------- |
| `NEW`           | 线程被创建但尚未启动。                                       |
| `RUNNABLE`      | 线程正在 Java 虚拟机中执行，可能正在等待 CPU 时间片来执行，也可能正在等待其他资源。 |
| `BLOCKED`       | 线程被阻塞并等待监视器锁（synchronized 块或方法）的释放，即阻塞态。 |
| `WAITING`       | 线程正在无限期等待另一个线程执行特定操作（使用 `wait()` 方法）的通知。 |
| `TIMED_WAITING` | 线程正在等待另一个线程执行特定操作，但只等待一段指定的时间（使用 `wait(long timeout)`、`sleep(long millis)`、`join(long millis)` 等方法）。 |
| `TERMINATED`    | 线程已经执行完毕或被提前终止。                               |

<img width="651" alt="image" src="https://github.com/steaksunflower0107/JavaLearingAsGopher-BasePart/assets/112318617/1ed04997-ed3c-4461-82b1-54b3727c29de">


该类中所枚举的状态和我们在操作系统中所学习到的**线程的状态**是紧密贴合的，不过在java中底层上整合了内核态和用户态，由此屏蔽了在编码时的一些细节

线程状态探究实例:

```java
public class RunnableClass {
    public static void main(String[] args) throws InterruptedException {
        ThreadDemo threadDemo = new ThreadDemo();
        // 创建线程时的状态
        System.out.println(threadDemo.getName() + "'s state is " + threadDemo.getState() + " now");
        threadDemo.start();

        while (threadDemo.getState() != Thread.State.TERMINATED) {
            // 线程运行时的状态
            System.out.println(threadDemo.getName() + "'s state is " + threadDemo.getState() + " now");
            Thread.sleep(1000);
        }

        System.out.println(threadDemo.getName() + "'s state is " + threadDemo.getState() + " now");
    }
}

class ThreadDemo extends Thread {
    @Override
    public void run() {
        for (int i = 0; i < 3; i++) {
            System.out.println(i + "times");
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

输出:

```shell
Thread-0's state is NEW now
Thread-0's state is RUNNABLE now
0times
Thread-0's state is TIMED_WAITING now
1times
Thread-0's state is TIMED_WAITING now
2times
Thread-0's state is TIMED_WAITING now
Thread-0's state is TERMINATED now
```

## 线程同步

java提供了一种封装好的实现同步的方法 - **synchronized**，可以方便的对某块内存进行加锁

`synchronized`有两种应用场景

1. 在方法签名中声明，使得该方法变为一个同步方法

   ```java
   public synchronized void method () {
   		// 被加锁的逻辑
   }
   ```

   比如，在之前的**车票超卖**的例子中，我们就可以通过在方法签名中加入`synchronized`来轻松的解决问题

   发生超卖的根本原因是在同一时刻有多个线程执行了`run()`方法，故因给其上锁:

   ```java
   @Override
   public synchronized void run() {
       while (true) {
           if (ticketNum <= 0) {
               System.out.println(Thread.currentThread().getName() + "find all tickets has been sole out");
               return;
           }
   
           try {
               Thread.sleep(500);
           } catch (InterruptedException e) {
               e.printStackTrace();
           }
   
           System.out.println(Thread.currentThread().getName() + "售出一张票，目前剩余: " + ticketNum);
           ticketNum--;
       }
   }
   ```

   是在编码上实现的同步的最简易的方法之一

2. 声明某个代码块:

   ```java
   synchronized (对象) {
   		// 被加锁的代码块逻辑
   }
   ```

   比如对于之前的run方法，我们可以写成:

   ```java
   @Override
   public void run() {
       synchronized (this){
           while (true) {
               if (ticketNum <= 0) {
                   System.out.println(Thread.currentThread().getName() + "find all tickets has been sole out");
                   return;
               }
               try {
                   Thread.sleep(500);
               } catch (InterruptedException e) {
                   e.printStackTrace();
               }
   
               System.out.println(Thread.currentThread().getName() + "售出一张票，目前剩余: " + --ticketNum);
           }
       }
   }
   ```

### 互斥锁

在java中有**对象互斥锁**的概念，即java中的所有对象都有一个可以被认为成互斥锁的**标记**，以用于保证线程安全等操作

`synchronized`的实现正是**基于对象互斥锁实现的**，其默认锁住的是**当前对象**，也就是`this`

对于静态的方法，锁住的是**当前类**，需要注意的是此时`synchornize()`需要传入的对象是**class对象**

```java
public static void method() {
    // 传入的是class对象，每个被加载的类，在jvm中都会有一个class对象与之对应
    synchronized (TicketSell.class) {
        System.out.println("method has been visited");
    }
}
```

应用互斥锁就有死锁的概率发生，以下是一个应用互斥锁发生死锁的例子:

```java
public class RunnableClass {
    public static void main(String[] args) {
        DeadLockDemo deadLockDemo01 = new DeadLockDemo(true);
        deadLockDemo01.setName("thread01");
        DeadLockDemo deadLockDemo02 = new DeadLockDemo(false);
        deadLockDemo02.setName("thread02");

        deadLockDemo01.start();
        deadLockDemo02.start();

    }
}

class DeadLockDemo extends Thread {
    static Object object01 = new Object();
    static Object object02 = new Object();
    boolean flag;

    public DeadLockDemo(boolean flag) {
        this.flag = flag;
    }


    @Override
    public void run() {
        if (flag) {
            synchronized (object01) {
                System.out.println(Thread.currentThread().getName() + "got object01");
                synchronized (object02) {
                    System.out.println(Thread.currentThread().getName()  + "got object02");
                }
            }
        } else {
            synchronized (object02) {
                System.out.println(Thread.currentThread().getName()  + "got object02");
                synchronized (object01) {
                    System.out.println(Thread.currentThread().getName() + "got object01");
                }
            }
        }
    }
}
```

输出:

```shell
thread02got object02
thread01got object01
```

发生死锁，两个线程都因无法得到资源而不能继续进行
