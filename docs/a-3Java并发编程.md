# Java 并发编程

## 什么是进程？是什么线程？ * * *

线程是CPU任务调度和执行的基本单位，进程是操作系统(除CPU外)资源分配的基本单位。

进程是程序的一次执行过程，是系统运行的基本单位。线程是一个比进程更小的执行单位，一个进程可以包含多个线程。

引入线程后，提高了系统的并发性。

## 进程和线程的关系？（区别） ***

**定义**：线程是处理器任务调度和执行的基本单位；进程是操作系统资源分配的基本单位。
**包含关系**：一个进程可以包含多个线程。
**从Java虚拟机的角度来理解**：Java虚拟机的运行时数据区包含堆、方法区、虚拟机栈、本地方法栈、程序计数器。

**各个进程之间是相互独立的，每个进程会包含多个线程**，每个进程所包含的多个线程并不是相互独立的，这个线程会共享进程的堆和方法区，但这些线程不会共享虚拟机栈、本地方法栈、程序计数器。即每个进程所包含的多个线程共享进程的堆和方法区，并且具备私有的虚拟机栈、本地方法栈、程序计数器，如图所示，假设某个进程包含三个线程。

<img src="https://cdn.jsdelivr.net/gh/hxznh/images@main/image-20220813175659970.png" alt="image-20220813175659970" style="zoom: 67%;" />

由上面可知以下进程和线程在以下几个方面的区别：

**内存分配：**进程之间的地址空间和资源是相互独立的，同一个进程之间的线程会共享线程的地址空间和资源（堆和方法区）。
**资源开销**：每个进程具备各自的数据空间，**进程之间的切换会有较大的开销**。属于同一进程的线程会共享堆和方法区，同时具备私有的虚拟机栈、本地方法栈、程序计数器，**线程之间的切换资源开销较小**。

<img src="https://cdn.jsdelivr.net/gh/hxznh/images@main/image-20221021145855226.png" alt="image-20221021145855226" style="zoom: 33%;" />

## 线程的属性

- 线程是CPU调度的基本单位
- 多CPU计算机中，各个线程可占用不同的CPU
- 每个线程都有一个线程ID、线程控制块（TCB)
- 线程也有就绪、阻塞、运行三种基本状态
- 线程几乎不拥有系统资源
- 同一进程的不同线程间共享进程的资源
- 由于共享内存地址空间，同一进程中的线程间通信甚至无需系统干预
- 同一进程中的线程切换，不会引起进程切换，不同进程中的线程切换，会引起进程切换
- 切换同进程内的线程，系统开销很小，切换进程，系统开销较大

## 进程和线程间通信方式

进程：https://blog.csdn.net/zy1992As/article/details/131894445

线程: https://blog.csdn.net/Ascend1977/article/details/130932379

## 协程

协程。一般称协程为**轻量级线程**。对于操作系统来说，内核只“认识”进程和线程，对于协程的存在，内核是不知道的，需要用户负责协程的创建、调度和销毁，因此，协程又被称为用户级线程。以下简要的介绍几点协程的性质：

- 协程是一种**用户态的轻量级线程**，协程的调度完全由用户控制。协程拥**有自己的寄存器上下文和栈**。协程调度切换时，将寄存器上下文和栈保存到其他地方（一般是自定义的数据结构中），在切回来的时候，恢复先前保存的寄存器上下文和栈，直接操作栈则基本没有内核切换的开销，可以不加锁的访问全局变量，所以上下文的切换非常快。
- 对操作系统而言，线程是最小的执行单元，进程是最小的资源管理单元。无论是进程还是线程，都是由操作系统所管理的。
- 协程不是被操作系统内核所管理的，而是**完全由程序所控制**，也就是**在用户态执行**。这样带来的好处是性能大幅度的提升，因为不会像线程切换那样消耗资源。
- 协程**既不是进程也不是线程**，协程仅仅是一个特殊的函数，协程它进程和进程不是一个维度的。
- 一个进程可以包含多个线程，一个线程可以包含多个协程。
- 一个线程内的多个协程虽然可以切换，但是多个协程是串行执行的，**只能在一个线程内运行**，**没法利用 CPU 多核能力**。
- 协程与进程一样，切换是**存在上下文切换问题**的。

## 并行和并发的区别？ *

并行：单位时间多个处理器同时处理多个任务。
并发：一个处理器处理多个任务，按时间片轮流处理多个任务。

## 多线程的优缺点（为什么使用多线程、多线程会引发什么问题） **

> 优点

当一个线程进入等待状态或者阻塞时，CPU可以先去执行其他线程，提高CPU的利用率。

> 缺点

- 上下文切换：频繁的上下文切换会影响多线程的执行速度。
- 死锁
- 资源限制：在进行并发编程时，程序的执行速度受限于计算机的硬件或软件资源。在并发编程中，程序执行变快的原因是将程序中串行执行的部分变成并发执行，如果因为资源限制，并发执行的部分仍在串行执行，程序执行将会变得更慢，因为程序并发需要上下文切换和资源调度。

## 线程的上下文切换 *

即便是单核的处理器也会支持多线程，处理器会给每个线程分配CPU时间片来实现这个机制。时间片是CPU分配给每个线程的执行时间，一般来说时间片非常的短，所以处理器会不停地切换线程。

CPU会通过时间片分配算法来循环执行任务，当前任务执行完一个时间片后会切换到下一个任务，但切换前会保存上一个任务的状态，因为下次切换回这个任务时还要加载这个任务的状态继续执行，从任务保存到在加载的过程就是一次上下文切换。

## Java中守护线程和用户线程的区别？ *

任何线程都可以设置为守护线程和用户线程，通过方法 `Thread.setDaemon(bool on)` 设置， true 则是将该线程设置为守护线程， false 则是将该线程设置为用户线程。同时， Thread.setDaemon() 必须在 Thread.start() 之前调用，否则运行时会抛出异常。

用户线程：平时使用到的线程均为用户线程。

守护线程：用来服务用户线程的线程，例如垃圾回收线程。

守护线程和用户线程的区别主要在于 Java 虚拟机是否存活。

用户线程：当任何一个用户线程未结束，Java虚拟机是不会结束的。

守护线程：如果只剩守护线程未结束，Java虚拟机结束。

## 线程死锁是如何产生的，如何避免 ***

> 这块内容很重要，面试时也可能让手写死锁的代码示例。

死锁：由于两个或两个以上的线程相互竞争对方的资源，而同时不释放自己的资源，导致所有线程同时被阻塞。

死锁产生的条件：

- 互斥条件：一个资源在同一时刻只由一个线程占用。
- 请求与保持条件：一个线程在请求被占资源时发生阻塞，并对已获得的资源保持不放。
- 循环等待条件：发生死锁时，所有的线程会形成一个死循环，一直阻塞。
- 不剥夺条件：线程已获得的资源在未使用完不能被其他线程剥夺，只能由自己使用完释放资源。

避免死锁的方法主要是**破坏死锁产生的条件**。

- 破坏互斥条件：这个条件无法进行破坏，锁的作用就是使他们互斥。
- 破坏请求与保持条件：一次性申请所有的资源。
- 破坏循环等待条件：按相同顺序来申请资源。
- 破坏不剥夺条件：线程在申请不到所需资源时，主动放弃所持有的资源。

## 死锁不加锁有哪些解决方案

1. 原子操作：使用原子操作类型(如 AtomicInteger)，可以保证其对共享变量的更新是一个原子操作，不需要加锁。
2. 读写锁：使用读写锁可以允许多个线程同时读取共享数据，但只允许一个线程写入。
3. 对象本身的同步：可以通过对象的同步方法或代码块(synchronized)来访问共享数据，这样可以确保只有一个线程可以同时访问。
4. 不可变对象：使用不可变对象(如 String)可以保证其在多线程环境下不会被修改，不需要加锁。
5. 协作式多任务：使用协作式多任务可以在不加锁的情况下实现多任务处理。

> 无锁编程

CAS

## 用Java实现死锁，并给出避免死锁的解决方案

```java
class DeadLockDemo {
    private static Object resource1 = new Object();
    private static Object resource2 = new Object();
    public static void main(String[] args) {
        new Thread(() -> {
            synchronized (resource1) {
                System.out.println(Thread.currentThread() + "get resource1");
                try {
                    Thread.sleep(1000); // 线程休眠，保证线程2先获得资源2
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(Thread.currentThread() + "waiting get resource2");
                synchronized (resource2) {
                    System.out.println(Thread.currentThread() + "get resource2");
                }
            }
        }, "线程 1").start();
        new Thread(() -> {
            synchronized (resource2) {
                System.out.println(Thread.currentThread() + "get resource2");
                try {
                    Thread.sleep(1000); //线程休眠，保证线程1先获得资源1
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } System.out.println(Thread.currentThread() + "waiting get resource1");
                synchronized (resource1) {
                    System.out.println(Thread.currentThread() + "get resource1");
                }
            }
        }, "线程 2").start();
    }
}
```

```java
Thread[线程 1,5,main]get resource1
Thread[线程 2,5,main]waiting get resource1
Thread[线程 1,5,main]waiting get resource2
```

上面代码产生死锁的原因主要是线程1获取到了资源1，线程2获取到了资源2，线程1继续获取资源2而产生阻塞，线程2继续获取资源1而产生阻塞。解决该问题最简单的方式就是两个线程按顺序获取资源，线程1和线程2都先获取资源1再获取资源2，无论哪个线程先获取到资源1，另一个线程都会因无法获取线程1产生阻塞，等到先获取到资源1的线程释放资源1，另一个线程获取资源1，这样两个线程可以轮流获取资源1和资源2。代码如下：

```java
private static Object resource1 = new Object();
private static Object resource2 = new Object();
public static void main(String[] args) {
    new Thread(() -> {
        synchronized (resource1) {
            System.out.println(Thread.currentThread() + "get resource1");
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            } 
            System.out.println(Thread.currentThread() + "waiting get resource2");
            synchronized (resource2) {
                System.out.println(Thread.currentThread() + "get resource2");
            }
        }
    }, "线程 1").start();
    new Thread(() -> {
        synchronized (resource1) {
            System.out.println(Thread.currentThread() + "get resource1");
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            } 
            System.out.println(Thread.currentThread() + "waiting get resource2");
            synchronized (resource2) {
                System.out.println(Thread.currentThread() + "get resource2");
            }
        }
    }, "线程 2").start();
}
```

## Java中的死锁、活锁、饥饿有什么区别？ *

**活锁**：任务或者执行者没有被阻塞，由于某些条件没有被满足，导致线程一直重复尝试、失败、尝试、失败。例如，线程1和线程2都需要获取一个资源，但他们同时让其他线程先获取该资源，两个线程一直谦让，最后都无法获取

> 活锁和死锁的区别：

- 活锁是在不断地尝试、死锁是在一直等待。
- 活锁有可能自行解开、死锁无法自行解开。

**饥饿**：一个或者多个线程因为种种原因无法获得所需要的资源， 导致一直无法执行的状态。

以打印机打印文件为例，当有多个线程需要打印文件，系统按照短文件优先的策略进行打印，但当短文件的打印任务一直不间断地出现，那长文件的打印任务会被一直推迟，导致饥饿。活锁就是在忙式等待条件下发生的饥饿，忙式等待就是不进入等待状态的等待。

> 产生饥饿的原因：

- 高优先级的线程占用了低优先级线程的CPU时间
- 线程被永久堵塞在一个等待进入同步块的状态，因为其他线程总是能在它之前持续地对该同步块进行访问。
- 线程在等待一个本身也处于永久等待完成的对象(比如调用这个对象的 wait() 方法)，因为其他线程总是被持续地获得唤醒。

> 死锁、饥饿的区别

饥饿可自行解开，死锁不行。

## 线程的生命周期和状态 * * *

线程状态的划分并不唯一，但是都大同小异，这里参考《Java并发编程的艺术》，主要有以下几种状态：

| 状态         |                                                              |
| ------------ | ------------------------------------------------------------ |
| NEW          | 初始状态，注意此时还未调用方法                               |
| RUNNABLE     | 运行状态，包含就绪和运行中两种状态                           |
| BLOCKED      | 阻塞状态                                                     |
| WAITING      | 等待状态                                                     |
| TIME_WAITING | 超时等待状态，和等待状态不同的是，它可以在指定的时间自行返回 |
| TERMINATED   | 终止状态，线程运行结束                                       |

线程转化过程如下：

<img src="https://cdn.jsdelivr.net/gh/hxznh/images@main/image-20230809140015325.png" alt="image-20230809140015325" style="zoom:67%;" />

## 创建线程一共有哪几种方法？ ***

- 继承 Thread 类创建线程
- 实现 Runnable 接口创建线程
- 使用 Callable 和 Future 创建线程
- 使用线程池例如用 Executor 框架

**继承Thread类创建线程**，首先继承Thread类，重写 run() 方法，在 main() 函数中调用子类实实例的 start() 方法。

```java
public class ThreadDemo extends Thread {
    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName() + " run()方法正在执行");
    }
}
public class TheadTest {
    public static void main(String[] args) {
        ThreadDemo threadDemo = new ThreadDemo();
        threadDemo.start();
        System.out.println(Thread.currentThread().getName() + " main()方法执行结束");
    }
}
```

```java
main main()方法执行结束
Thread-0 run()方法正在执行
```

实现Runnable接口创建线程：首先创建实现 Runnable 接口的类 RunnableDemo ，重写 run() 方法；
创建类 RunnableDemo 的实例对象 runnableDemo ，以 runnableDemo 作为参数创建 Thread 对象，调用 Thread 对象的 start() 方法 。

```java
public class RunnableDemo implements Runnable {
    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName() + " run()方法执行中");
    }
}
```

```java
public class RunnableTest {
    public static void main(String[] args) {
        RunnableDemo runnableDemo = new RunnableDemo ();
        Thread thread = new Thread(runnableDemo);
        thread.start();
        System.out.println(Thread.currentThread().getName() + " main()方法执行完成");
    }
}
```

输出结果：

```java
main main()方法执行完成
Thread-0 run()方法执行中
```

使用Callable和Future创建线程：

1. 创建Callable接口的实现类 CallableDemo ，重写 call() 方法。
2. 以类 CallableDemo 的实例化对象作为参数创建 FutureTask 对象。
3. 以 FutureTask 对象作为参数创建 Thread 对象。
4. 调用 Thread 对象的 start() 方法。

```java
class CallableDemo implements Callable<Integer> {
    @Override
    public Integer call() {
        System.out.println(Thread.currentThread().getName() + " call()方法执行中");
        return 0;
    }
}
class CallableTest {
    public static void main(String[] args) throws ExecutionException,
    InterruptedException {
        FutureTask<Integer> futureTask = new FutureTask<Integer>(new CallableDemo());
        Thread thread = new Thread(futureTask);
        thread.start();
        System.out.println("返回结果 " + futureTask.get());
        System.out.println(Thread.currentThread().getName() + " main()方法执行完成");
    }
}
```

输出结果

```java
Thread-0 call()方法执行中
返回结果 0
main main()方法执行完成
```

使用线程池例如用Executor框架： Executors 可提供四种线程池，分别为：

- newCachedThreadPool 创建一个可缓存线程池，如果线程池长度超过处理需要，可灵活回收空闲线程，若无可回收，则新建线程, 适合大量短而多任务的业务场景。
- newFixedThreadPool 创建一个定长线程池，可控制线程最大并发数，超出的线程会在队列中等待。
- newScheduledThreadPool 创建一个定时线程池，支持定时及周期性任务执行。
- newSingleThreadExecutor 创建一个单线程化的线程池，它只会用唯一的工作线程来执行任务，保证所有任务按照指定顺序执行。

下面以创建一个定长线程池为例进行说明，

```java
class ThreadDemo extends Thread {
    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName() + "正在执行");
    }
} 
class TestFixedThreadPool {
    public static void main(String[] args) {
        //创建一个可重用固定线程数的线程池
        ExecutorService pool = Executors.newFixedThreadPool(2);
        //创建实现了Runnable接口对象，Thread对象当然也实现了Runnable接口
        Thread t1 = new ThreadDemo();
        Thread t2 = new ThreadDemo();
        Thread t3 = new ThreadDemo();
        Thread t4 = new ThreadDemo();
        Thread t5 = new ThreadDemo();
        //将线程放入池中进行执行
        pool.execute(t1);
        pool.execute(t2);
        pool.execute(t3);
        pool.execute(t4);
        pool.execute(t5);
        //关闭线程池
        pool.shutdown();
    } 
}
```

```java
pool-1-thread-2正在执行
pool-1-thread-1正在执行
pool-1-thread-1正在执行
pool-1-thread-2正在执行
pool-1-thread-1正在执行
```

> 阿里巴巴开发规约

![image-20220816175126251](a-3Java%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B.assets/image-20220816175126251.png)

## runnable 和 callable 有什么区别？ * * *

> 相同点

- 两者都是接口
- 两者都需要调用 Thread.start 启动线程

> 不同点

- callable的核心是 call() 方法，允许返回值， runnable 的核心是 run() 方法，没有返回值
- call() 方法可以抛出异常，但是 run() 方法不行
- callable 和 runnable 都可以应用于 executors，thread 类只支持 runnable

## Future

### 应用场景

 在并发编程中，我们经常用到非阻塞的模型，在之前的多线程的三种实现中，不管是继承thread类还是实现runnable接口，都无法保证获取到之前的执行结果。通过实现Callback接口，并用Future可以来接收多线程的执行结果。

Future表示一个可能还没有完成的异步任务的结果，针对这个结果可以添加Callback以便在任务执行成功或失败后作出相应的操作。

举个例子：比如去吃早点时，点了包子和凉菜，包子需要等3分钟，凉菜只需1分钟，如果是串行的一个执行，在吃上早点的时候需要等待4分钟，但是因为你在等包子的时候，可以同时准备凉菜，所以在准备凉菜的过程中，可以同时准备包子，这样只需要等待3分钟。那Future这种模式就是后面这种执行模式。

> 类图结构

Future接口定义了主要的5个接口方法，有RunnableFuture和SchedualFuture继承这个接口，以及CompleteFuture和ForkJoinTask继承这个接口。

<img src="https://cdn.jsdelivr.net/gh/hxznh/images@main/image-20221014201610198.png" alt="image-20221014201610198" style="zoom: 50%;" />

**RunnableFuture**

这个接口同时继承Future接口和Runnable接口，在成功执行run（）方法后，可以通过Future访问执行结果。这个接口都实现类是FutureTask,一个可取消的异步计算，这个类提供了Future的基本实现，后面我们的demo也是用这个类实现，它实现了启动和取消一个计算，查询这个计算是否已完成，恢复计算结果。计算的结果只能在计算已经完成的情况下恢复。如果计算没有完成，get方法会阻塞，一旦计算完成，这个计算将不能被重启和取消，除非调用runAndReset方法。

FutureTask能用来包装一个Callable或Runnable对象，因为它实现了Runnable接口，而且它能被传递到Executor进行执行。为了提供单例类，这个类在创建自定义的工作类时提供了protected构造函数。

**SchedualFuture**

这个接口表示一个延时的行为可以被取消。通常一个安排好的future是定时任务SchedualedExecutorService的结果

**CompleteFuture**

一个Future类是显示的完成，而且能被用作一个完成等级，通过它的完成触发支持的依赖函数和行为。当两个或多个线程要执行完成或取消操作时，只有一个能够成功。

**ForkJoinTask**

基于任务的抽象类，可以通过ForkJoinPool来执行。一个ForkJoinTask是类似于线程实体，但是相对于线程实体是轻量级的。大量的任务和子任务会被ForkJoinPool池中的真实线程挂起来，以某些使用限制为代价。

> 主要方法

```java
boolean cancel(boolean mayInterruptIfRunning);

boolean isCancelled();

V get() throws InterruptedException, ExecutionException;

V get(long timeout, TimeUnit unit)
    throws InterruptedException, ExecutionException, TimeoutException;
```

get（）方法可以当任务结束后返回一个结果，如果调用时，工作还没有结束，则会阻塞线程，直到任务执行完毕

get（long timeout,TimeUnit unit）最多等待timeout的时间就会返回结果

cancel（boolean  mayInterruptIfRunning）方法可以用来停止一个任务，如果任务可以停止（通过mayInterruptIfRunning来进行判断），则可以返回true,如果任务已经完成或者已经停止，或者这个任务无法停止，则会返回false.

isDone（）方法判断当前方法是否完成

isCancel（）方法判断当前方法是否取消

### Future示例demo

需求场景：等早餐过程中，包子需要3秒，凉菜需要1秒，普通的多线程需要四秒才能完成。先等凉菜，再等包子，因为等凉菜时，普通多线程启动start()方法，执行run()中具体方法时，没有返回结果，所以如果要等有返回结果，必须是要1秒结束后才知道结果。

> 普通多线程

```java
public class BumThread extends Thread{
    @Override
    public void run() {
        try {
            Thread.sleep(1000*3);
            System.out.println("包子准备完毕");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

}

> FutureTask 版本

```java
public static void main(String[] args) throws InterruptedException, ExecutionException {
	long start = System.currentTimeMillis();

	// 等凉菜 
	Callable ca1 = new Callable(){
 
		@Override
		public String call() throws Exception {
			try {
				Thread.sleep(1000);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
			return "凉菜准备完毕";
		}
	};
	FutureTask<String> ft1 = new FutureTask<String>(ca1);
	new Thread(ft1).start();

	// 等包子 -- 必须要等待返回的结果，所以要调用join方法
	Callable ca2 = new Callable(){
 
			@Override
			public Object call() throws Exception {
				try {
					Thread.sleep(1000*3);
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
				return "包子准备完毕";
		}
	};
	FutureTask<String> ft2 = new FutureTask<String>(ca2);
	new Thread(ft2).start();

	System.out.println(ft1.get());
	System.out.println(ft2.get());

	long end = System.currentTimeMillis();
	System.out.println("准备完毕时间："+(end-start));
}
```

## 线程的run()和start()有什么区别？ ***

- 线程是通过 Thread 对象所对应的方法 run() 来完成其操作的，而线程的启动是通过 start() 方法执行的。
- run() 方法可以重复调用， start() 方法只能调用一次

## 为什么调用start()方法时会执行run()方法，而不直接执行run()方法？ ***

start() 方法来启动线程，真正实现了多线程运行，这时无需等待 run() 方法体代码执行完毕而直接继续执行下面的代码。通过调用Thread类的 start() 方法来启动一个线程，这时此线程处于就绪（可运行）状态，并没有运行，一旦得到cpu时间片，就开始执行 run() 方法，这里方法 run() 称为线程体，它包含了要执行的这个线程的内容， run() 方法运行结束，此线程随即终止。

run() 方法只是类的一个普通方法而已，如果直接调用 run 方法，程序中依然只有主线程这一个线程，其程序执行路径还是只有一条，还是要顺序执行，还是要等待 run() 方法体执行完毕后才可继续执行下面的代码，这样就没有达到写线程的目的。

调用 start() 方法可以开启一个线程，而 run() 方法只是thread类中的一个普通方法，直接调用run() 方法还是在主线程中执行的。

## 线程同步和线程调度相关的方法问题

### 线程同步以及线程调度相关的方法有哪些？ * * *

- wait() ：使一个线程处于等待（阻塞）状态，并且释放所持有的对象的锁；
- sleep() ：使当前线程进入指定毫秒数的休眠，暂停执行，需要处理 InterruptedException 。
- notify() ：唤醒一个处于等待状态的线程，当然在调用此方法的时候，并不能确切的唤醒某一个等待状态的线程，而是由 JVM 确定唤醒哪个线程，而且与优先级无关。
- notifyAll() ：唤醒所有处于等待状态的线程，该方法并不是将对象的锁给所有线程，而是让它们竞争，只有获得锁的线程才能进入就绪状态。
- join() ：与 sleep() 方法一样，是一个可中断的方法，在一个线程中调用另一个线程的 join()方法，会使得当前的线程挂起，直到执行 join() 方法的线程结束。例如在B线程中调用A线程的join() 方法，B线程进入阻塞状态，直到A线程结束或者到达指定的时间。
- yield() ：提醒调度器愿意放弃当前的CPU资源，使得当前线程从 RUNNING 状态切换到 RUNABLE 状态。

### 线程的sleep()方法和yield()方法有什么不同？ * * *

- sleep() 方法会使得当前线程暂停指定的时间，没有消耗CPU时间片。
- sleep() 使得线程进入到阻塞状态， yield() 只是对CPU进行提示，如果CPU没有忽略这个提示，会使得线程上下文的切换，进入到就绪状态。
- sleep() 一定会完成给定的休眠时间， yield() 不一定能完成。
- sleep() 需要抛出InterruptedException，而 yield() 方法无需抛出异常。

### sleep()方法和wait()方法的区别？ * * *

> 相同点

- wait() 方法和 sleep() 方法都可以使得线程进入到阻塞状态。
- wait() 和 sleep() 方法都是可中断方法，被中断后都会收到中断异常

> 不同点

- wait() 是Object的方法， sleep() 是Thread的方法。
- wait() 必须在同步方法中进行， sleep() 方法不需要。
  线程在同步方法中执行 sleep() 方法，不会释放monitor的锁，而 wait() 方法会释放monitor的锁。
- sleep() 方法在休眠指定时长之后会主动退出阻塞，而 wait() 方法在没有指定wait时间的情况下需要被其他线程中断才可以退出阻塞。

### wait()方法一般在循环块中使用还是if块中使用？ * * *

在JDK官方文档中==**明确要求了要在循环中使用**==，否则可能出现**虚假唤醒**的可能。官方文档中给出的代码示例如下：

```java
synchronized(obj){
    while(<condition does not hold>){
        obj.wait();
    } 
    //满足while中的条件后执行业务逻辑
}
```

如果讲 while 换成 if ，当线程被唤醒后，**可能 if() 中的条件已经不满足了**，出现虚假唤醒。

```java
synchronized(obj){
    if(<condition does not hold>){
        obj.wait();
    }
}
```

### 线程通信的方法有哪些？ ***

- 锁与同步
- wait() / notify() 或 notifyAll()
- 信号量
- 管道

### 为什么wait()、notify()、notifyAll()被定义在Object类中而不是在Thread类中？ **

因为这些方法在操作同步线程时，都必须要标识他们操作线程的锁，只有同一个锁上的被等待线程，可以被同一个锁上的 notify() 或 notifyAll() 唤醒，不可以对不同锁中的线程进行唤醒，也就是说等待和唤醒必须是同一锁。而锁可以是任意对象，所以可以被任意对象调用的方法是定义在 Object 类中。
如果把 wait()、notify()、notifyAll() 定义在Thread类中，则会出现一些难以解决的问题，例如:

- 如何让一个线程可以持有多把锁？
- 如何确定线程等待的是哪把锁？

既然是当前线程去等待某个对象的锁，则应通过操作对象来实现而不是操作线程，而Object类是所有对象的父类，所以将这三种方法定义在Object类中最合适。

### 为什么wait()，notify()和notifyAll()必须在同步方法或者同步块中被调用？ ***

因为 wait() 暂停的是持有锁的对象， notify() 或 notifyAll() 唤醒的是等待锁的对象。所以 wait()、notify()、notifyAll() 都需要线程持有锁的对象，进而需要在同步方法或者同步块中被调用。

### 为什么Thread类的sleep()和yield()方法是静态的？ *

sleep() 和 yield() 都是需要正在执行的线程调用的，那些本来就阻塞或者等待的线程调用这个方法是无意义的，所以这两个方法是静态的。

1. **假设**yield()为非静态方法，若thread2拿到锁，调用了thread1的yield()方法，意为让thread1暂时放弃当前占有的CPU时间片，与大家一起再进行时间片竞争。关键是thread1现在处于阻塞状态！它在等待thread2释放锁，它根本没有占用CPU时间片。所以**此时调用thread1的yield()方法是没有意义的！**
2. sleep()方法道理类似，**本来就是阻塞状态，所以没必要再阻塞**，即使“自然阻塞状态”比如是10ms，你设置了100ms，而“自然阻塞状态”持续时间是无法预测的，所以设定的休眠时间便失去了意义！
3. 而对于**单线程**而言，**即使没有锁的争夺，在一个线程执行的时候另外一个线程也处于“不占有CPU时间片的状态”**，什么时候再拿到时间片或者多长时间竞争一次CPU时间片都是无法预测的。所以在正在执行的线程里调用另外一个线程的yield()和sleep()方法依然是没有意义的。

### 如何停止一个正在运行的线程？ **

- 中断： Interrupt 方法中断线程

  ```java
  Thread.currentThread().interrupt();
  ```

- 使用 volatile boolean 标志位停止线程：在线程中设置一个 boolean 标志位，同时用 volatile 修饰保证可见性，在线程里不断地读取这个值，其他地方可以修改这个 boolean 值。

- 使用 stop() 方法停止线程，但该方法已经被废弃。因为这样线程不能在停止前保存数据，会出现数据完整性问题。

### 如何唤醒一个阻塞的线程？ **

如果线程是由于 wait() 、 sleep() 、 join() 、 yield() 等方法进入阻塞状态的，是可以进行唤醒的。如果线程是IO阻塞是无法进行唤醒的，因为IO是操作系统层面的，Java代码无法直接接触操作系统。

- wait() ：可用 notify() 或 notifyAll() 方法唤醒。
- sleep() ：调用该方法使得线程在指定时间内进入阻塞状态，等到指定时间过去，线程再次获取到CPU时间片进而被唤醒。
- join() ：当前线程A调用另一个线程B的 join() 方法，当前线程转A入阻塞状态，直到线程B运行结束，线程A才由阻塞状态转为可执行状态。
- yield() ：使得当前线程放弃CPU时间片，但随时可能再次得到CPU时间片进而激活。

### Java如何实现两个线程之间的通信和协作？ * *

- syncrhoized 加锁的线程的 Object 类的 wait() / notify() / notifyAll()
- ReentrantLock 类加锁的线程的 Condition 类的 await() / signal() / signalAll()
- 通过管道进行线程间通信：1）字节流；2）字符流 ，就是一个线程发送数据到输出管道，另一个线程从输入管道读数据。

### 同步方法和同步方法块哪个效果更好？ * *

同步块更好些，因为它锁定的范围更灵活些，只在需要锁住的代码块锁住相应的对象，而同步方法会锁住整个对象。

### 什么是线程同步？什么是线程互斥？他们是如何实现的？ * * *

- 线程的互斥是指某一个资源只能被一个访问者访问，具有唯一性和排他性。但访问者对资源访问的顺序是乱序的。
- 线程的同步是指在互斥的基础上使得访问者对资源进行有序访问。

> 线程同步的实现方法

- 同步方法
- 同步代码块
- wait() 和 notify()
- 使用volatile实现线程同步
- 使用重入锁实现线程同步
- 使用局部变量实现线程同步
- 使用阻塞队列实现线程同步

### 在Java程序中如何保证线程的运行安全？ ***

线程安全问题 主要体现在**原子性**、**可见性**和**有序性**。

- 原子性：一个或者多个操作在 CPU 执行的过程中不被中断的特性。线程切换带来的原子性问题。
- 可见性：一个线程对共享变量的修改，另外一个线程能够立刻看到。缓存导致的可见性问题。
- 有序性：程序执行的顺序按照代码的先后顺序执行。编译优化带来的有序性问题。

> 解决方法：

- 原子性问题：可用JDK Atomic 开头的原子类、 synchronized、LOCK 来解决
- 可见性问题：可用 synchronized、volatile、LOCK 来解决
- 有序性问题：可用 Happens-Before 规则来解决

### 线程类的构造方法、静态块是被哪个线程调用的？ *

线程类的构造方法、静态块是被 new 这个线程类所在的线程所调用的，而 run() 方法里面的代码才是被线程自身所调用的。

> 一个例子

假设 main() 函数中 new 了一个线程Thread1，那么Thread1的构造方法、静态块都是 main 线程调用的，Thread1中的 run() 方法是自己调用的。

假设在Thread1中 new 了一个线程Thread2，那么Thread2的构造方法、静态块都是Thread1线程调用的，Thread2中的 run() 方法是自己调用的。

### 一个线程运行时异常会发生什么? *

Java中的 Throwable 主要分为 Exception 和 Error 。 Exception 分为运行时异常和非运行时常。运行时异常可以不进行处理，代码也能通过编译，但运行时会报错。非运行时异常必须处理，否则代码无法通过编译。出现Error程序会直接停止运行

### 线程数量过多会造成什么影响？ *

- 线程的生命周期开销非常高，需要维护大量线程的生存，同时因频繁切换上下文而导致性能下降
- 消耗过多的CPU 资源
  如果可运行的线程数量多于可用处理器的数量，那么有线程将会被闲置。大量空闲的线程会占用许多内存，给垃圾回收器带来压力，而且大量的线程在竞争CPU 资源时还将产生其他性能的开销。
- 降低稳定性
  JVM 在可创建线程的数量上存在一个限制，这个限制值将随着平台的不同而不同，并且承受着多个因素制约，包括JVM的启动参数、Thread构造函数中请求栈的大小，以及底层操作系统对线程的限制等。如果破坏了这些限制，那么可能抛出OutOfMemoryError 异常。

## 三个线程T1、T2、T3，如何让他们按顺序执行？ ***

这是一道面试中常考的并发编程的代码题，与它相似的问题有：

- 三个线程T1、T2、T3轮流打印ABC，打印n次，如ABCABCABCABC.......
- 两个线程交替打印1-100的奇偶数
- N个线程循环打印1-100
- ......

其实这类问题本质上都是线程通信问题，思路基本上都是一个线程执行完毕，阻塞该线程，唤醒其他线
程，按顺序执行下一个线程。下面先来看最简单的，如何按顺序执行三个线程。

- synchronized+wait/notify

基本思路就是线程A、线程B、线程C三个线程同时启动，因为变量 num 的初始值为 0 ，所以线程B或线程C拿到锁后，进入 while() 循环，然后执行 wait() 方法，线程线程阻塞，释放锁。只有线程A拿到锁后，不进入 while() 循环，执行 num++ ，打印字符 A ，最后唤醒线程B和线程C。此时 num 值为1, 只有线程B拿到锁后，不被阻塞，执行 num++ ，打印字符 B ，最后唤醒线程A和线程C，后面以此类推。

```java
class Wait_Notify_ACB {
    private int num;
    private static final Object LOCK = new Object();
    private void printABC(String name, int targetNum) {
        // fori 重复打印多次
        synchronized (LOCK) {
            while (num % 3 != targetNum) { // 
                try {
                    LOCK.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            } 
            num++;
            System.out.print(name);
            LOCK.notifyAll();
        }
    } 
    public static void main(String[] args) {
        Wait_Notify_ACB wait_notify_acb = new Wait_Notify_ACB ();
        new Thread(() -> {
            wait_notify_acb.printABC("A", 0);
        }, "A").start();
        new Thread(() -> {
            wait_notify_acb.printABC("B", 1);
        }, "B").start();
        new Thread(() -> {
            wait_notify_acb.printABC("C", 2);
        }, "C").start();
    }
}
```

输出结果

```java
ABCABCABCABCABCABCABCABCABCABC
Process finished with exit code 0
```

下面看第二个问题，两个线程交替打印1-100的奇偶数，为了减少输出所占篇幅，这里将100 改成了 10。基本思路上面类似，线程odd先拿到锁——打印数字——唤醒线程even——阻塞线程odd，以此循
环。

```java
class Wait_Notify_Odd_Even{
    private Object monitor = new Object();
    private volatile int count;
    Wait_Notify_Odd_Even(int initCount) {
        this.count = initCount;
    } 
    private void printOddEven() {
        synchronized (monitor) {
            while (count < 10) {
                try {
                    System.out.print( Thread.currentThread().getName() + "：");
                    System.out.println(++count);
                    monitor.notifyAll();
                    monitor.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            } 
            //防止count=10后，while()循环不再执行，有子线程被阻塞未被唤醒，导致主线程不能退出
            monitor.notifyAll();
        }
    } 
    public static void main(String[] args) throws InterruptedException {
        Wait_Notify_Odd_Even waitNotifyOddEven = new Wait_Notify_Odd_Even(0);
        new Thread(waitNotifyOddEven::printOddEven, "odd").start();
        Thread.sleep(10);
        new Thread(waitNotifyOddEven::printOddEven, "even").start();
    }
}
```

运行结果：

```java
odd：1
even：2
odd：3
even：4
odd：5
even：6
odd：7
even：8
odd：9
even：10
```

再看第三个问题，N个线程循环打印1-100，其实仔细想想这个和三个线程循环打印ABC并没有什么本质区别，只需要加上判断是否到了打印数字的最大值的语句即可。假设N=3，为了能把输出结果完全显示，打印1-10，代码如下：

```java
class Wait_Notify_ACB {
    private int num;
    private static final Object LOCK = new Object();
    private int maxnum = 10;
    private void printABC(String name, int targetNum) {
        while (true) {
            synchronized (LOCK) {
                while (num % 3 != targetNum) { 
                    if(num >= maxnum){
                        break;
                    } 
                    try {
                        LOCK.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                } 
                if(num >= maxnum){
                    break;
                } 
                num++;
                System.out.println(Thread.currentThread().getName() + ": " +
                                   num);
                LOCK.notifyAll();
            }
        }
    }
    public static void main(String[] args) {
        Wait_Notify_ACB wait_notify_acb = new Wait_Notify_ACB ();
        new Thread(() -> {
            wait_notify_acb.printABC("thread1", 0);
        }, "thread1").start();
        new Thread(() -> {
            wait_notify_acb.printABC("thread2", 1);
        }, "thread2").start();
        new Thread(() -> {
            wait_notify_acb.printABC("thread3", 2);
        }, "thread3").start();
    }
} 
```

输出结果：

```java
thread1: 1
thread2: 2
thread3: 3
thread1: 4
thread2: 5
thread3: 6
thread1: 7
thread2: 8
thread3: 9
thread1: 10
```

面试官：大家都是用的synchronized+wait/notify，你能不能换个方法解决该问题？

> 还可以用join方法

下面介绍的方法只给出第一道题的代码了，否则太长了，相信大家可以举一反三

- join()

join() 方法：在A线程中调用了B线程的join()方法时，表示只有当B线程执行完毕时，A线程才能继续执行。基于这个原理，我们使得三个线程按顺序执行，然后循环多次即可。无论线程1、线程2、线程3哪个先执行，最后执行的顺序都是线程1——>线程2——>线程3。代码如下：

```java
class Join_ABC {
    public static void main(String[] args) throws InterruptedException {
        for (int i = 0; i < 10; i++) {
            Thread t0 = new Thread(new printABC(null),"A");
            Thread t1 = new Thread(new printABC(t1),"B");
            Thread t2 = new Thread(new printABC(t2),"C");
            t0.start();
            t1.start();
            t2.start();
            Thread.sleep(10); 
            // 这里是要保证只有t1、t2、t3为一组，进行执行才能保证t1->t2->t3的执行顺序。
        }
    } 

    static class printABC implements Runnable{
        private Thread beforeThread;
        public printABC(Thread beforeThread) {
            this.beforeThread = beforeThread;
        } 
        @Override
        public void run() {
            if(beforeThread!=null) {
                try {
                    beforeThread.join();
                    System.out.print(Thread.currentThread().getName());
                }catch(Exception e){
                    e.printStackTrace();
                }
            } else {
                System.out.print(Thread.currentThread().getName());
            }
        }
    }
}
```

还有LockSupport、CountDownLatch、AtomicInteger等等。

面试官：那如何实现三个线程循环打印ACB，其中A打印两次，B打印三次，C打印四次呢？
我：......
面试官：如何用两个线程交叉打印数字和字符呢？例如A1B2C3......Z26
我：......

可以思考下后面两个问题，原理都是相通的

## synchronized关键字  ***

### 什么是synchronized关键字？

在多线程的环境下，多个线程同时访问共享资源会出现一些问题，而synchronized关键字则是用来保证线程同步的。

### <span id='Java内存的可见性问题'>Java内存的可见性问题</span>

[volatile篇](#volatile)

在了解synchronized关键字的底层原理前，需要先简单了解下Java的内存模型，看看synchronized关键字是如何起作用的。

<img src="https://cdn.jsdelivr.net/gh/hxznh/images@main/image-20220814224755148.png" alt="image-20220814224755148" style="zoom:67%;" />

这里的本地内存并不是真实存在的，只是Java内存模型的一个抽象概念，它包含了控制器、运算器、缓存等。同时Java内存模型规定，线程对共享变量的操作必须在自己的本地内存中进行，不能直接在主内存中操作共享变量。这种内存模型会出现什么问题呢？

1. 线程A获取到共享变量X的值，此时本地内存A中没有X的值，所以加载主内存中的X值并缓存到本地内存A中，线程A修改X的值为1，并将X的值刷新到主内存中，这时主内存及本地内存中的X的值都为1。
2. 线程B需要获取共享变量X的值，此时本地内存B中没有X的值，加载主内存中的X值并缓存到本地内存B中，此时X的值为1。线程B修改X的值为2，并刷新到主内存中，此时主内存及本地内存B中的X值为2，本地内存A中的X值为1。
3. 线程A再次获取共享变量X的值，此时本地内存中存在X的值，所以直接从本地内存中A获取到了X为1的值，但此时主内存中X的值为2，到此出现了所谓内存不可见的问题。

该问题Java内存模型是通过synchronized关键字和volatile关键字就可以解决，那么synchronized关键字是如何解决的呢，其实进入synchronized块就是把**在synchronized块内使用到的变量从线程的本地内存中擦除**，这样在synchronized块中再次使用到该变量就不能从本地内存中获取了，需要从主内存中获取，解决了内存不可见问题。

### synchronized关键字三大特性是什么？

> 面试时经常拿synchronized关键字和volatile关键字的特性进行对比，synchronized关键字可以保证并发编程的三大特性：原子性、可见性、有序性，而volatile关键字只能保证可见性和有序性，不能保证原子性，也称为是轻量级的synchronized

- 原子性：一个或多个操作要么全部执行成功，要么全部执行失败。synchronized关键字可以保证只有一个线程拿到锁，访问共享资源。
- 可见性：当一个线程对共享变量进行修改后，其他线程可以立刻看到。执行synchronized时，会对应执行 lock 、unlock原子操作，保证可见性。
- 有序性：程序的执行顺序会按照代码的先后顺序执行

### synchronized关键字可以实现什么类型的锁？

- 悲观锁：synchronized关键字实现的是悲观锁，每次访问共享资源时都会上锁。
- 非公平锁：synchronized关键字实现的是非公平锁，即线程获取锁的顺序并不一定是按照线程阻塞的顺序。
- 可重入锁：synchronized关键字实现的是可重入锁，即已经获取锁的线程可以再次获取锁。
- 独占锁或者排他锁：synchronized关键字实现的是独占锁，即该锁只能被一个线程所持有，其他线程均被阻塞。

### synchronized关键字的使用方式

synchronized主要有三种使用方式：修饰普通同步方法、修饰静态同步方法、修饰同步方法块。
修饰普通同步方法（实例方法）

```java
class SyncTest implements Runnable {
    private static int i = 0; // 共享资源
    private synchronized void add() {
        i++;
    } 
    @Override
    public void run() {
        for (int j = 0; j < 10000; j++) {
            add();
        }
    } 
    public static void main(String[] args) throws Exception {
        SyncTest syncTest = new SyncTest();
        Thread t1 = new Thread(syncTest);
        Thread t2 = new Thread(syncTest);
        t1.start();
        t2.start();
        t1.join();
        t2.join();
        System.out.println(i);
    }
}
```

这是一个非常经典的例子，多个线程操作 i++ 会出现线程不安全问题，这段代码的结果很容易得到

```java
20000
```

```java
class SyncTest implements Runnable {
    private static int i = 0; //共享资源
    private synchronized void add() {
        i++;
    } 
    @Override
    public void run() {
        for (int j = 0; j < 10000; j++) {
            add();
        }
    } 
    public static void main(String[] args) throws Exception {
        // SyncTest syncTest = new SyncTest();
        Thread t1 = new Thread(new SyncTest());
        Thread t2 = new Thread(new SyncTest());
        t1.start();
        t2.start();
        t1.join();
        t2.join();
        System.out.println(i);
    }
}
```

这段代码的结果无法确定，但一定为 i <= 20000,且在大部分情况下远小于20000

第二个示例中的 add() 方法虽然也使用synchronized关键字修饰了，但是因为两次 new syncTest() 操作建立的是两个不同的对象，也就是说存在两个不同的对象锁，线程t1和t2使用的是不同的对象锁，所以不能保证线程安全。

那这种情况应该如何解决呢？因为每次创建的实例对象都是不同的，而类对象却只有一个，如果synchronized关键字作用于类对象，即用synchronized修饰静态方法，问题则迎刃而解

- 修饰静态方法

只需要在 add() 方法前用static修饰即可，即当synchronized作用于静态方法，锁就是当前的class对象。

```java
class SyncTest implements Runnable {
    private static int i = 0; // 共享资源
    private static synchronized void add() {
        i++;
    } 
    @Override
    public void run() {
        for (int j = 0; j < 10000; j++) {
            add();
        }
    } 
    public static void main(String[] args) throws Exception {
        // SyncTest syncTest = new syncTest();
        Thread t1 = new Thread(new syncTest());
        Thread t2 = new Thread(new syncTest());
        t1.start();
        t2.start();
        t1.join();
        t2.join();
        System.out.println(i);
    }
}
```

```java
20000
```

- 修饰同步代码代码块

如果某些情况下，整个方法体比较大，需要同步的代码只是一小部分，如果直接对整个方法体进行同步，会使得代码性能变差，这时只需要对一小部分代码进行同步即可。代码如下

```java
class SyncTest implements Runnable {
    static int i = 0; // 共享资源
    @Override
    public void run() {
        // 其他操作.......
        synchronized (this){ 
            // this表示当前对象实例，即传入的 syncTest
            // 这里还可以使用syncTest.class，表示class对象锁
            for (int j = 0; j < 10000; j++) {
                i++;
            }
        }
    } 
    public static void main(String[] args) throws Exception {
        SyncTest syncTest = new SyncTest();
        Thread t1 = new Thread(syncTest);
        Thread t2 = new Thread(syncTest);
        t1.start();
        t2.start();
        t1.join();
        t2.join();
        System.out.println(i);
    }
} 
```

```java
20000
```

### synchronized关键字的底层原理

> 这个问题也是面试比较高频的一个问题，也是比较难理解的，理解synchronized需要一定的Java虚拟机的知识。

在jdk1.6之前，synchronized被称为重量锁，在jdk1.6中，为了减少获得锁和释放锁带来的性能开销，引入了偏向锁和轻量级锁。下面先介绍jdk1.6之前的synchronized原理。

- 对象头

在HotSpot虚拟机中，Java对象在内存中的布局大致可以分为三部分：**对象头**、**实例数据**和**填充对齐**。因为synchronized用的锁是存在对象头里的，这里我们需要重点了解对象头。如果对象头是数组类型，则对象头由**Mark Word**、**Class MetadataAddress**和**Array length**组成，如果对象头非数组类型，对象头则由**Mark Word**和**Class MetadataAddress**组成。在32位虚拟机中，数组类型的Java对象头的组成如下表：

|         内容          |                  说明                  | 长度  |
| :-------------------: | :------------------------------------: | :---: |
|       Mark Word       | 存储对象的hashCode、分代年龄和锁标记位 | 32bit |
| Class MetadataAddress |        存储到对象类型数据的指针        | 32bit |
|     Array length      |               数组的长度               | 32bit |

这里我们需要重点掌握的是 **Mark Word**。

- **Mark Word**

在运行期间，Mark Word中存储的数据会随着锁标志位的变化而变化，在32位虚拟机中，不同状态下的组成如下：

其中线程ID表示持有偏向锁线程的ID，Epoch表示偏向锁的时间戳，偏向锁和轻量级锁是在jdk1.6中引入的。

- 重量级锁的底部实现原理：Monitor

在jdk1.6之前，synchronized只能实现重量级锁，Java虚拟机是基于Monitor对象来实现重量级锁的，所以首先来了解下Monitor，在Hotspot虚拟机中，Monitor是由ObjectMonitor实现的，其源码是用C++语言编写的，首先我们先下载Hotspot的源码，源码下载链接：http://hg.openjdk.java.net/jdk8/jdk8/hotspot，找到ObjectMonitor.hpp文件，路径是 `src/share/vm/runtime/objectMonitor.hpp` ，
这里只是简单介绍下其数据结构

```c++
ObjectMonitor() {
    _header = NULL;
    _count = 0; //锁的计数器，获取锁时count数值加1，释放锁时count值减1，直到 count 为 0
    _waiters = 0, //等待线程数
    _recursions = 0; //锁的重入次数
    _object = NULL;
    _owner = NULL; // 指向持有ObjectMonitor对象的线程地址
    _WaitSet = NULL; // 处于wait状态的线程，会被加入到_WaitSet
    _WaitSetLock = 0 ;
    _Responsible = NULL ;
    _succ = NULL ;
    _cxq = NULL ; // 阻塞在EntryList上的单向线程列表
    FreeNext = NULL ;
    _EntryList = NULL ; // 处于等待锁block状态的线程，会被加入到该列表
    _SpinFreq = 0 ;
    _SpinClock = 0 ;
    OwnerIsThread = 0 ;
}
```

其中 `_owner`、 `_WaitSet`和 `_EntryList` 字段比较重要，它们之间的转换关系如下图

<img src="https://cdn.jsdelivr.net/gh/hxznh/images@main/image-20220815154011130.png" alt="image-20220815154011130" style="zoom:67%;" />

从上图可以总结获取Monitor和释放Monitor的流程如下：

1. 当多个线程同时访问同步代码块时，首先会进入到EntryList中，然后通过CAS的方式尝试将Monitor中的owner字段设置为当前线程，同时count加1，若发现之前的owner的值就是指向当前线程的，recursions也需要加1。如果CAS尝试获取锁失败，则进入到EntryList中
2. 当获取锁的线程调用 wait() 方法，则会将owner设置为null，同时count减1，recursions减1，当前线程加入到WaitSet中，等待被唤醒。
3. 当前线程执行完同步代码块时，则会释放锁，count减1，recursions减1。当recursions的值为0时，说明线程已经释放了锁。

> 之前提到过一个常见面试题，为什么 wait() 、 notify() 等方法要在同步方法或同步代码块中来执行呢，这里就能找到原因，是因为 wait() 、 notify() 方法需要借助ObjectMonitor对象内部方法来完成。

- synchronized作用于同步代码块的实现原理

前面已经了解Monitor的实现细节，而Java虚拟机则是通过进入和退出Monitor对象来实现方法同步和代码块同步的。

这里为了更方便看程序字节码执行指令，先在IDEA中安装了一个 `jclasslib Bytecode viewer` 插件。

我们先来看这个synchronized作用于同步代码块的代码。

```java
public void run() {
    // 其他操作.......
    synchronized (this){
        // this表示当前对象实例，这里还可以使用syncTest.class，表示class对象锁
        for (int j = 0; j < 10000; j++) {
            i++;
        }
    }
}
```

查看代码字节码指令如下：

```java
1 dup
2 astore_1
3 monitorenter //进入同步代码块的指令
4 iconst_0
5 istore_2
6 iload_2
7 sipush 10000
10 if_icmpge 27 (+17)
13 getstatic #2 <com/company/syncTest.i>
16 iconst_1
17 iadd
18 putstatic #2 <com/company/syncTest.i>
21 iinc 2 by 1
24 goto 6 (-18)
27 aload_1
28 monitorexit //结束同步代码块的指令
29 goto 37 (+8)
32 astore_3
33 aload_1
34 monitorexit //遇到异常时执行的指令
35 aload_3
36 athrow
37 return
```

从上述字节码中可以看到同步代码块的实现是由monitorenter 和 monitorexit 指令完成的，其中monitorenter指令所在的位置是同步代码块开始的位置，第一个monitorexit 指令是用于正常结束同步代码块的指令，第二个monitorexit 指令是用于异常结束时所执行的释放Monitor指令。

- synchronized作用于同步方法原理

```java
private synchronized void add() {
    i++;
}
```

对应字节码：

```java
0 getstatic #2 <com/company/syncTest.i>
3 iconst_1
4 iadd
5 putstatic #2 <com/company/syncTest.i>
8 return
```

发现这个没有monitorenter 和 monitorexit 这两个指令了，而在查看该方法的class文件的结构信息时发现了Access flags后边的synchronized标识，该标识表明了该方法是一个同步方法。Java虚拟机通过该标识可以来辨别一个方法是否为同步方法，如果有该标识，线程将持有Monitor，在执行方法，最后释放Monitor。

<img src="https://cdn.jsdelivr.net/gh/hxznh/images@main/image-20220815164514357.png" alt="image-20220815164514357" style="zoom:67%;" />

> 原理大概就是这样，最后总结一下，面试中应该简洁地如何回答synchroized的底层原理这个问题。

答：Java虚拟机是通过进入和退出Monitor对象来实现代码块同步和方法同步的，代码块同步使用的是 monitorenter 和 monitorexit 指令实现的，而方法同步是通过Access flags后面的标识来确定该方法是否为同步方法。

### Jdk1.6为什么要对synchronized进行优化？

因为Java虚拟机是通过进入和退出Monitor对象来实现代码块同步和方法同步的，而Monitor是依靠底层操作系统的Mutex Lock来实现的，操作系统实现线程之间的切换需要从用户态转换到内核态，这个切换成本比较高，对性能影响较大。

### JDK1.6对synchronized做了哪些优化？

> 锁的升级

在JDK1.6中，为了减少获得锁和释放锁带来的性能消耗，引入了偏向锁和轻量级锁，锁的状态变成了四种，无锁状态，偏向锁状态、轻量级锁状态和重量级锁状态。锁的状态会随着竞争激烈逐渐升级，但通常情况下，锁的状态只能升级不能降级

- 无锁：对象一开始就是无锁状态。
- 偏向锁： 相当于给对象贴了一个标签 (将自己的线程 id 存入对象头中)，下次我再进来时，发现标签是我的，我就可以继续使用了。
- 自旋锁： 想象一下有一个厕所，里面有一个人在，你很想上但是只有一个坑位，所以你只能徘徊等待，等那个人出来以后，你就可以使用了 。 这个自旋是使用 cas 来保证原子性的，关于 cas 我这里就不再赘述了。
- 重量级锁： 直接向 cpu 去申请申请锁 ，其他的线程都进入队列中等待。

### Synchronized 内部三种锁的性能区别

|  锁名称  |                             优势                             |                             劣势                             |              使用场景              |
| :------: | :----------------------------------------------------------: | :----------------------------------------------------------: | :--------------------------------: |
|  偏向锁  | 加锁和解锁不需要CAS操作，没有额外的性能消耗，和执行非同步方法相比仅存在纳秒级的差距 |        如果线程间存在锁竞争，会带来额外的锁撤销的消耗        | 适用于只有一个线程访问同步快的场景 |
| 轻量级锁 |              竞争的线程不会阻塞，提高了响应速度              |    如线程成始终得不到锁竞争的线程，使用自旋会消耗CPU性能     | 追求响应时间，同步快执行速度非常快 |
| 重量级锁 |                线程竞争不用自旋，不会消耗CPU                 | 线程阻塞，响应时间缓慢，在多线程下，频繁的获取释放锁，会带来巨大的性能消耗 |   追求吞吐量，同步快执行速度较长   |

### 多线程中 synchronized 锁升级的原理是什么？

synchronized 是 JVM 层面的锁，是 Java 关键字，通过 monitor 对象来完成，synchronized 的实现涉及到锁的升级，具体为无锁、偏向锁、自旋锁、重量级锁
synchronized 锁升级原理：在锁对象的对象头里面有一个 threadid 字段，在第一次访问的时候 threadid 为空，jvm  让其持有偏向锁，并将 threadid 设置为其线程 id，再次进入的时候会先判断 threadid 是否与其线程 id  一致，如果一致则可以直接使用此对象，如果不一致，则升级偏向锁为轻量级锁（只要有另一个竞争线程就升级），通过自旋循环一定次数来获取锁，执行一定次数之后，如果还没有正常获取到要使用的对象，此时就会把锁从轻量级升级为重量级锁，此过程就构成了 synchronized 锁的升级。

锁的升级的目的：锁升级是为了减低了锁带来的性能消耗。在 Java 6 之后优化 synchronized 的实现方式，使用了偏向锁升级为轻量级锁再升级到重量级锁的方式，从而减低了锁带来的性能消耗。

### 锁升级是什么时候发生的

- 偏向锁：一个线程获取锁时会由无锁升级为偏向锁
- 自旋锁：当产生线程竞争时由偏向锁升级为自旋锁,想象一下 while(true) ;
- 重量级锁：当线程竞争到达一定数量或超过一定时间时，晋升为重量级锁

> 升级成重量锁条件

老版本：

- 第一个条件，自旋次数超过10次时直接升级为重量级，就是转圈赚了10次后直接升级重量级锁，jvm有个参数 preSpinLock，默认是10次，可以自己设置
- 第二个条件，如果自旋线程的数量超过了cpu的二分之一，也会直接升级为重量级锁；比如你的cpu锁核数是8个，但是有5个线程在那自旋等着占锁使用，8的二分之一是4，5个线程明显已经超过了4，所以这时候直接升级为重量级锁；

新版本：

- Adaptive CAS （自适应自旋），让hotSpot自己决定自旋几次在升级为重量级锁，就相当于自动化了；也就不用我们去管了。所以以后jvm调优就不需要调整自旋次数的参数；因为内部情况怎样只有jvm自己知道；



### Synchronized 与 ReentrantLock 的区别

**1.1 底层实现的区别**

- synchronized 是 [JVM](https://so.csdn.net/so/search?q=JVM&spm=1001.2101.3001.7020) 层面的锁，是 Java 关键字，通过 monitor 对象来完成，synchronized 的实现涉及到锁的升级，具体为无锁、偏向锁、自旋锁、重量级锁
- 而 ReentrantLock 是由 java api 去实现的。ReentrantLock 实现则是通过利用CAS自旋机制保证线程操作的原子性和 `volatile` 保证数据可见性以实现锁的功能。

**1.2 是否可手动释放**

- synchronized 不需要用户去手动释放锁，synchronized 代码执行完后系统会自动让线程释放对锁的占用。
- ReentrantLock 则需要用户去手动释放锁，如果没有手动释放锁，就可能导致死锁现象。一般通过 lock() 和 unlock() 方法配合 try / finally 语句块来完成，使用释放更加灵活。

**1.3 是否可中断**

- synchronized 是不可中断类型的锁，除非加锁的代码中出现异常或正常执行完成。
- ReentrantLock 则可以中断，可通过 trylock(long timeout,TimeUnit unit) 设置超时方法；或者将 lockInterruptibly() 放到代码块中，调用 interrupt 方法进行中断。

**1.4 是否公平锁**

- synchronized 为非公平锁。
- ReentrantLock 则即可以选公平锁也可以选非公平锁，通过构造方法 new ReentrantLock 时传入 boolean 值进行选择，为空默认 false 非公平锁，true 为公平锁。

**1.5 锁是否可绑定条件 Condition**

- synchronized 不能绑定。
- ReentrantLock 通过绑定 Condition 结合  await() / singal() 方法实现线程的精确唤醒，而不是像 synchronized 通过 Object 类的 wait() /  notify() / notifyAll() 方法要么随机唤醒一个线程要么唤醒全部线程。

**1.6 锁的对象**

- synchronzied 锁的是对象，锁是保存在对象头里面的，根据对象头数据来标识是否有线程获得锁 / 争抢锁。
- ReentrantLock 锁的是线程，根据进入的线程和 int 类型的 state 标识锁的获得 / 争抢。

原文链接：https://blog.csdn.net/liuwg1226/article/details/120164119

### synchronized 和 lock 的区别

- 1：synchronized 是 JVM 层面的锁，是 Java 关键字，通过 monitor 对象来完成，synchronized 的实现涉及到锁的升级，具体为无锁、偏向锁、自旋锁、重量级锁，而Lock是java.util.concurrent.Locks 包下的一个接口；
- 2：Synchronized 使用过后，会自动释放锁，而Lock需要手动上锁、手动释放锁。（在 finally 块中）
- 3：synchronized 关键字不能响应中断；Lock可以响应中断、可定时
- 4：synchronized关键字是非公平锁，即，不能保证等待锁的那些线程们的顺序，而Lock的子类ReentrantLock默认是非公平锁，但是可通过一个布尔参数的构造方法实例化出一个公平锁；
- 5：synchronized无法判断，是否已经获取到锁，而Lock通过tryLock()方法可以判断，是否已获取到锁；
- 6：Lock可以通过分别定义读写锁提高多个线程读操作的效率。
- 7：二者的底层实现不一样：synchronized是同步阻塞，采用的是悲观并发策略；Lock是同步非阻塞，采用的是乐观并发策略（底层基于volatile关键字和CAS算法实现）

## ReentrantLock

ReentrantLock 的使用也是非常简单的，与 Synchronized 的不同就是需要自己去手动释放锁，为了保证一定释放，所以通常都是和 try~finally 配合使用的。

```java
ReentrantLock lock = new ReentrantLock(/* fair */ true);
lock.lock();
try {
  
} finally {
    lock.unlock();
}
```

### 原理

ReentrantLock 意为可重入锁，说起 ReentrantLock 就不得不说 [AQS](#AQS) ，因为其底层就是使用 AQS 去实现的。

ReentrantLock 有两种模式，一种是公平锁，一种是非公平锁。

- 公平模式下等待线程入队列后会严格按照队列顺序去执行
- 非公平模式下等待线程入队列后有可能会出现插队情况

<img src="https://cdn.jsdelivr.net/gh/hxznh/images@main/image-20230314152222210.png" alt="image-20230314152222210" style="zoom:50%;" />

## <span id='volatile'>volatile关键字</span> ***  

### volatile的作用是什么？

volatile 是一个轻量级的 synchronized ，一般作用与变量，在多处理器开发的过程中保证了内存的可见性。相比于 synchronized 关键字， volatile 关键字的执行成本更低，效率更高。

### volatile的特性有哪些？

> 并发编程的三大特性为可见性、有序性和原子性。通常来讲 volatile 可以保证可见性和有序性。

- 可见性： volatile 可以保证不同线程对共享变量进行操作时的可见性。即当一个线程修改了共享变量时，另一个线程可以读取到共享变量被修改后的值。
- 有序性： volatile 会通过禁止指令重排序进而保证有序性。
- 原子性：对于单个的 volatile 修饰的变量的读写是可以保证原子性的，但对于 i++ 这种复合操作并不能保证原子性。也就是说 **volatile 不具备原子性**。

### Java内存的可见性问题

与 synchronized 一章中相同，[跳转](#Java内存的可见性问题)

### 为什么代码会重排序？

计算机在执行程序的过程中，编译器和处理器通常会对指令进行重排序，这样做的目的是为了提高性能。具体可以看下面这个例子。

```java
int a = 1;
int b = 2;
int a1 = a;
int b1 = b;
int a2 = a + a;
int b2 = b + b;
// ......
```

像这段代码，不断地交替读取a和b，会导致寄存器频繁交替存储a和b，使得代码性能下降

可对其进行如下重排序。

```java
int a = 1;
int b = 2;
int a1 = a;
int a2 = a + a;
int b1 = b;
int b2 = b + b;
......
```

按照这样地顺序执行代码便可以避免交替读取a和b，这就是重排序地意义。

指令重排序一般分为编译器优化重排、指令并行重排和内存系统重排三种。

- 编译器优化重排：编译器在不改变单线程程序语义的情况下，可以对语句的执行顺序进行重新排序。
- 指令并行重排：现代处理器多采用指令级并行技术来将多条指令重叠执行。对于不存在数据依赖的程序，处理器可以对机器指令的执行顺序进行重新排列。
- 内存系统重排：因为处理器使用缓存和读/写缓冲区，使得加载（load）和存储（store）看上去像是在乱序执行。
  注：简单解释下数据依赖性：如果两个操作访问了同一个变量，并且这两个操作有一个是写操作，这两个操作之间就会存在数据依赖性，例如：

```java
a = 1;
b = a;
```

如果对这两个操作的执行顺序进行重排序的话，那么结果就会出现问题。

> 其实，这三种指令重排说明了一个问题，就是指令重排在单线程下可以提高代码的性能，但在多线程下可以会出现一些问题。

### 重排序会引发什么问题？

前面已经说过了，在单线程程序中，重排序并不会影响程序的运行结果，而在多线程场景下就不一定了。可以看下面这个经典的例子，该示例出自《Java并发编程的艺术》。

```java
class ReorderExample{
    int a = 0;
    boolean flag = false;
    public void writer(){
        a = 1; // 操作1
        flag = true; // 操作2
    }
    public void reader(){
        if (flag){ // 操作3
            int i = a + a; // 操作4
        }
    }
}
```

假设线程1先执行 writer() 方法，随后线程2执行 reader() 方法，最后程序一定会得到正确的结果
吗？
答案是不一定的，如果代码按照下图的执行顺序执行代码则会出现问题

<img src="https://cdn.jsdelivr.net/gh/hxznh/images@main/image-20220815180847378.png" alt="image-20220815180847378" style="zoom:55%;" />

操作1和操作2进行了重排序，线程1先执行 flag=true ，然后线程2执行操作3和操作4，线程2执行操作4时不能正确读取到 a 的值，导致最终程序运行结果出问题。这也说明了在多线程代码中，重排序会破坏多线程程序的语义

### as-if-serial规则和happens-before规则的区别？

> 区别

- as-if-serial定义：无论编译器和处理器如何进行重排序，单线程程序的执行结果不会改变。
- happens-before定义：一个操作happens-before另一个操作，表示第一个的操作结果对第二个操作可见，并且第一个操作的执行顺序也在第二个操作之前。但这并不意味着Java虚拟机必须按照这个顺序来执行程序。如果重排序的后的执行结果与按happens-before关系执行的结果一致，Java虚拟机也会允许重排序的发生。

happens-before关系保证了同步的多线程程序的执行结果不被改变

as-if-serial保证了单线程内程序的执行结果不被改变。	

> 相同点

happens-before和as-if-serial的作用都是在不改变程序执行结果的前提下，提高程序执行的并行度。

### voliatile的实现原理？

> 前面已经讲述 volatile 具备可见性和有序性两大特性，所以 volatile 的实现原理也是围绕如何实现可见性和有序性展开的。

### volatile实现内存可见性原理

导致内存不可见的主要原因就是Java内存模型中的本地内存和主内存之间的值不一致所导致，例如上面所说线程A访问自己本地内存A的X值时，但此时主内存的X值已经被线程B所修改，所以线程A所访问到的值是一个脏数据。那如何解决这种问题呢？

volatile 可以保证内存可见性的关键是 volatile 的读/写实现了缓存一致性，缓存一致性的主要内容为：

- 每个处理器会通过嗅探总线上的数据来查看自己的数据是否过期，一旦处理器发现自己缓存对应的内存地址被修改，就会将当前处理器的缓存设为无效状态。此时，如果处理器需要获取这个数据需重新从主内存将其读取到本地内存。
- 当处理器写数据时，如果发现操作的是共享变量，会通知其他处理器将该变量的缓存设为无效状态。

> 那缓存一致性是如何实现的呢？

可以发现通过 volatile 修饰的变量，生成汇编指令时会比普通的变量多出一个 Lock 指令，这个 Lock指令就是 volatile 关键字可以保证内存可见性的关键，它主要有两个作用：

- 将当前处理器缓存的数据刷新到主内存。
- 刷新到主内存时会使得其他处理器缓存的该内存地址的数据无效。

### volatile实现有序性原理

> 前面提到重排序可以提高代码的执行效率，但在多线程程序中可以导致程序的运行结果不正确，那 volatile 是如何解决这一问题的呢？

为了实现 volatile 的内存语义，编译器在生成字节码时会通过插入**内存屏障**来禁止指令重排序。

**内存屏障**：内存屏障是一种CPU指令，它的作用是对该指令前和指令后的一些操作产生一定的约束，保证一些操作按顺序执行。

Java虚拟机插入内存屏障的策略
Java内存模型把内存屏障分为4类，如下表所示：

|      屏障类型       |         指令示例         | 说明                                                         |
| :-----------------: | :----------------------: | :----------------------------------------------------------- |
|  LoadLoad Barriers  |   Load1;LoadLoad;Load2   | 保证Load1数据的读取先于Load2及后续所有读取指令的执行         |
| StoreStore Barriers | Store1;StoreStore;Store2 | 保证Store1数据刷新到主内存先于Store2及后 续所有存储指令      |
| LoadStore Barriers  |  Load1;LoadStore;Store2  | 保证Load1数据的读取先于Store2及后续的所有 存储指令刷新到主内存 |
| StoreLoad Barriers  |  Store1;StoreLoad;Load2  | 保证Store1数据刷新到主内存先于Load2及后续 所有读取指令的执行 |

注：StoreLoad Barriers同时具备其他三个屏障的作用，它会使得该屏障之前的所有内存访问指令完成之后，才会执行该屏障之后的内存访问命令。

Java内存模型对编译器指定的 volatile 重排序规则为：

- 当第一个操作是 volatile 读时，无论第二个操作是什么都不能进行重排序。
- 当第二个操作是 volatile 写时，无论第一个操作是什么都不能进行重排序。
- 当第一个操作是 volatile 写，第二个操作为 volatile 读时，不能进行重排序。

根据 volatile 重排序规则，Java内存模型采取的是保守的屏障插入策略， volatile 写是在前面和后面分别插入内存屏障， volatile 读是在后面插入两个内存屏障，具体如下：

- volatile 读：在每个 volatile 读后面分别插入LoadLoad屏障及LoadStore屏障（根据volatile 重排序规则第一条），如下图所示

<img src="https://cdn.jsdelivr.net/gh/hxznh/images@main/image-20220815182757596.png" alt="image-20220815182757596" style="zoom:67%;" />

LoadLoad屏障的作用：禁止上面的所有普通读操作和上面的 volatile 读操作进行重排序。

LoadStore屏障的作用：禁止下面的普通写和上面的 volatile 读进行重排序。

- volatile 写：在每个 volatile 写前面插入一个StoreStore屏障（为满足 volatile 重排序规则第二条），在每个 volatile 写后面插入一个StoreLoad屏障（为满足 volatile 重排序规则第三条），如下图所示

<img src="https://cdn.jsdelivr.net/gh/hxznh/images@main/image-20220815183230209.png" alt="image-20220815183230209" style="zoom:80%;" />

StoreStore屏障的作用：禁止上面的普通写和下面的 volatile 写重排序
StoreLoad屏障的作用：防止上面的 volatile 写与下面可能出现的 volatile 读/写重排序。

### 编译器对内存屏障插入策略的优化

> 因为Java内存模型所采用的屏障插入策略比较保守，所以在实际的执行过程中，只要不改变volatile 读/写的内存语义，编译器通常会省略一些不必要的内存屏障。

代码如下：

```java
public class volatile BarrierDemo{
    int a;
    volatile int b = 1;
    volatile int c = 2;
    public void test(){
        int i = b; // volatile 读
        int j = c; // volatile 读
        a = i + j; // 普通写
    }
}
```

指令序列示意图如下：

<img src="https://cdn.jsdelivr.net/gh/hxznh/images@main/image-20220815183619182.png" alt="image-20220815183619182" style="zoom: 75%;" />

从上图可以看出，通过指令优化一共省略了两个内存屏障（虚线表示），省略第一个内存屏障 LoadStore的原因是最后的普通写不可能越过第二个 volatile 读，省略第二个内存屏障LoadLoad的原因是下面没有涉及到普通读的操作。

### volatile能使一个非原子操作变成一个原子操作吗？

volatile 只能保证**可见性**和**有序性**，==但可以保证64位的 long 型和 double 型变量的原子性。==

对于 **32位的虚拟机** 来说，每次原子读写都是32位的，会将 long 和 double 型变量拆分成两个32位的操作来执行，这样 long 和 double 型变量的读写就不能保证原子性了，而通过 volatile 修饰的long和 double型变量则可以保证其原子性。

### volatile、synchronized的区别？

- volatile 主要是保证内存的可见性，即变量在寄存器中的内存是不确定的，需要从主存中读取。synchronized 主要是解决多个线程访问资源的同步性。
- volatile 作用于变量， synchronized 作用于代码块或者方法。
- volatile 仅可以保证数据的可见性，不能保证数据的原子性。 synchronized 可以保证数据的可见性和原子性。
- volatile 不会造成线程的阻塞， synchronized 会造成线程的阻塞。

## ConcurrentHashMap ***

### 什么是ConcurrentHashMap？相比于HashMap和HashTable有什么优势？

CocurrentHashMap 可以看作**线程安全且高效**的 HashMap ，相比于 HashMap 具有线程安全的优势，相比于 HashTable 具有效率高的优势。

### java中ConcurrentHashMap是如何实现的？

这里经常会将jdk1.7中的 ConcurrentHashMap 和jdk1.8中的 ConcurrentHashMap 的实现方式进行对比。

- JDK1.7

在JDK1.7版本中， ConcurrentHashMap 的数据结构是由一个 Segment 数组和多个 HashEntry 数组组
成， Segment 存储的是链表数组的形式，如图所示。

<img src="https://cdn.jsdelivr.net/gh/hxznh/images@main/image-20220815202233843.png" alt="image-20220815202233843" style="zoom:67%;" />

从上图可以看出， ConcurrentHashMap 定位一个元素的过程需要两次Hash的过程，第一次Hash的目的是定位到Segment，第二次Hash的目的是定位到链表的头部。两次Hash所使用的时间比一次Hash的时间要长，但这样做可以在写操作时，只对元素所在的segment加锁，不会影响到其他segment，这样可以大大提高并发能力。

- JDK1.8

JDK1.8不再采用segment的结构，而是使用Node数组+链表/红黑树的数据结构来实现的（和 HashMap一样，链表节点个数大于8，链表会转换为红黑树）
如下图所示

<img src="../../../面试/面经总结.assets/image-20220815202501776.png" alt="image-20220815202501776" style="zoom:67%;" />

从上图可以看出，对于 ConcurrentHashMap 的实现，JDK1.8的实现方式可以降低锁的粒度，因为JDLK1.7所实现的 ConcurrentHashMap 的锁的粒度是基于Segment，而一个Segment包含多个HashEntry

### ConcurrentHashMap结构中变量使用volatile和final修饰有什么作用？

final 修饰变量可以保证变量不需要同步就可以被访问和共享， volatile 可以保证内存的可见性，配合CAS操作可以在不加锁的前提支持并发。

### ConcurrentHashMap有什么缺点？

因为 ConcurrentHashMap 在更新数据时只会锁住部分数据，并不会将整个表锁住，读取的时候也并不能保证读取到最近的更新，只能保证读取到已经顺利插入的数据。

### ConcurrentHashMap默认初始容量是多少？每次扩容为原来的几倍？

默认的初始容量为**16**，每次扩容为之前的**两倍**。

### ConCurrentHashMap 的key，value是否可以为null？为什么？HashMap中的key、value是否可以为null？

ConCurrentHashMap 中的 key 和 value 为 null 会出现空指针异常，而 HashMap 中的 key 和value 值是可以为 null 的。

原因如下： ConCurrentHashMap 是在多线程场景下使用的，如果 ConcurrentHashMap.get(key) 的值为 null ，那么无法判断到底是 key 对应的 value 的值为 null 还是不存在对应的 key 值。而在单线程场景下的 HashMap 中，可以使用 containsKey(key) 来判断到底是不存在这个 key 还是 key 对应的 value 的值为 null 。在多线程的情况下使用containsKey(key) 来做这个判断是存在问题的，因为在 containsKey(key) 和 ConcurrentHashMap.get(key) 两次调用的过程中， key 的值可能已经发生了改变。

### ConCurrentHashmap在JDK1.8中，什么情况下链表会转化为红黑树？

当链表长度大于8，Node数组数大于64时。

### ConcurrentHashMap在JDK1.7和JDK1.8版本中的区别？

- 实现结构上的不同，JDK1.7是基于Segment实现的，JDK1.8是基于Node数组+链表/红黑树实现的。

- 保证线程安全方面：JDK1.7采用了分段锁的机制，当一个线程占用锁时，会锁住一个Segment对象，不会影响其他Segment对象。JDK1.8则是采用了CAS和 synchronize 的方式来保证线程安全。

- 在存取数据方面：
  **JDK1.7中的 put() 方法**：

  1. 先计算出 key 的 hash 值，利用 hash 值对segment数组取余找到对应的segment对象。
  2. 尝试获取锁，失败则自旋直至成功，获取到锁，通过计算的 hash 值对hashentry数组进行取余，找到对应的entry对象。
  3. 遍历链表，查找对应的 key 值，如果找到则将旧的value直接覆盖，如果没有找到，则添加到链表中。（JDK1.7是插入到链表头部，JDK1.8是插入到链表尾部，这里可以思考一下为什么这样）

  **JDK1.8中的 put() 方法:**

  1. 计算 key 值的 hash 值，找到对应的 Node ，如果当前位置为空则可以直接写入数据。
  2. 利用CAS尝试写入，如果失败则自旋直至成功，如果都不满足，则利用 synchronized 锁写入数据。

> 为什么要将头插法改为尾插法

JDK1.7中扩容时，每个元素的rehash之后，都会插入到新数组对应索引的链表头，所以这就导致原链表顺序为A->B->C，扩容之后，rehash之后的链表可能为C->B->A，元素的顺序发生了变化。**在并发场景下，扩容时可能会出现循环链表的情况。而JDK1.8从头插入改成尾插入元素的顺序不变，避免出现循环链表的情况。**

### ConcurrentHashMap迭代器是强一致性还是弱一致性？

与HashMap不同的是， `ConcurrentHashMap` 迭代器是弱一致性。

这里解释一下弱一致性是什么意思，当 `ConcurrentHashMap` 的迭代器创建后，会遍历哈希表中的元素，在遍历的过程中，哈希表中的元素可能发生变化，如果这部分变化发生在已经遍历过的地方，迭代器则不会反映出来，如果这部分变化发生在未遍历过的地方，迭代器则会反映出来。换种说法就是 `put()` 方法将一个元素加入到底层数据结构后， `get()` 可能在某段时间内还看不到这个元素。

这样的设计主要是为 `ConcurrenthashMap` 的性能考虑，如果想做到强一致性，就要到处加锁，性能会下降很多。所以 `ConcurrentHashMap` 是支持在迭代过程中，向map中添加元素的，而 `HashMap` 这样操作则会抛出异常。

## ThreadLocal ***

### 什么是ThreadLocal？有哪些应用场景？

`ThreadLocal` 是 `JDK java.lang` 包下的一个类， `ThreadLocal` 为变量在每个线程中都创建了一个副本，那么每个线程可以访问自己内部的副本变量，并且不会和其他线程的局部变量冲突，实现了线程间的数据隔离。

ThreadLocal 的应用场景主要有以下几个方面：

- 保存线程上下文信息，在需要的地方可以获取
- 线程间数据隔离
- 数据库连接

https://blog.csdn.net/weixin_44421461/article/details/124811688

https://blog.csdn.net/Maxiao1204/article/details/119535205

### ThreadLocal原理和内存泄露？

> 要搞懂 ThreadLocal 的底层原理需要看下他的源码，太长了，有兴趣的同学可以自己看看相关资料，这里只是简单介绍下结构，因为 Threadlocal 内存泄露是个高频知识点，并且需要简单了解 ThreadLocal 结构。

ThreadLocal 的原理可以概括为下图：

<img src="https://cdn.jsdelivr.net/gh/hxznh/images@main/image-20220815220352108.png" alt="image-20220815220352108" style="zoom:67%;" />

从上图可以看出每个线程都有一个`ThreadLocalMap`，`ThreadLocalMap`中保存着所有的`ThreadLocal` ，而 `ThreadLocal` 本身只是一个引用本身并不保存值，值都是保存在 `ThreadLocalMap`中的，其中`ThreadLocal` 为 `ThreadLocalMap` 中的 key 。其中图中的虚线表示弱引用。
这里简单说下Java中的引用类型，Java的引用类型主要分为强引用、软引用、弱引用和虚引用。

- **强引用**：发生 gc 的时候不会被回收。
- **软引用**：有用但不是必须的对象，在发生内存溢出之前会被回收（很可能在老年区）。
- **弱引用**：有用但不是必须的对象，在下一次GC时会被回收（必定在伊甸园区）。
- **虚引用**：无法通过虚引用获得对象，虚引用的用途是在 gc 时返回一个通知。

> 为什么ThreadLocal会发生内存泄漏呢？

因为 ThreadLocal 中的 key 是弱引用，而 value 是强引用。当 ThreadLocal 没有被强引用时，在进行垃圾回收时， key 会被清理掉，而 value 不会被清理掉，这时如果不做任何处理， value 将永远不会被回收，产生内存泄漏。

> 如何解决ThreadLocal的内存泄漏？

其实在 `ThreadLocal` 在设计的时候已经考虑到了这种情况，在调用 set()、get()、remove() 等方法时就会清理掉 key 为 `null` 的记录，所以在使用完 `ThreadLocal` 后最好手动调用 remove() 方法。

#### 为什么要将key设计成ThreadLocal的弱引用？

如果 ThreadLocal 的 key 是强引用，**同样会发生内存泄漏**的。如果 ThreadLocal 的 key 是强引用，引用的`ThreadLocal` 的对象被回收了，但是 `ThreadLocalMap` 还持有 `ThreadLocal` 的强引用，如果没有手动删除，`ThreadLocal` 不会被回收，发生内存泄漏。

如果是弱引用的话，引用的 ThreadLocal 的对象被回收了，即使没有手动删除， ThreadLocal 也会被回收。 value 也会在 ThreadLocalMap 调用 set()、get()、remove() 的时候会被清除。

所以两种方案比较下来，还是 ThreadLoacl 的 key 为弱引用好一些。

#### 为什么不将value设计成ThreadLocal的弱引用？

不设置为弱引用，是因为**不清楚**这个`Value`除了`map`的引用还是否还存在**其他引用**，如果不存在其他引用，当`GC`的时候就会直接将这个Value干掉了，而此时我们的`ThreadLocal`还处于使用期间，就会造成Value为 `null`的**错误**，所以将其设置为强引用。

代码示例

```java
public static void main(String[] args) throws Exception {
    Map<WeakReference<Integer>, WeakReference<Integer>> map = new HashMap<>(16);
    WeakReference<Integer> key = new WeakReference<>(666);
    WeakReference<Integer> value = new WeakReference<>(777);
    map.put(key,value);
    System.out.println("put success");
    Thread.sleep(1000);
    System.gc();
    System.out.println("get " + map.get(key).get());
}
```

输出

```java
put success
get null
```

可以发现，如果value也是弱引用的话，就会在`GC`时被清理掉，导致获取到 `null`的情况

```java
ublic static void main(String[] args) throws Exception {
    Map<WeakReference<Integer>, WeakReference<Integer>> map = new HashMap<>(8);
    // 注意这里~
    WeakReference<Integer> key = new WeakReference<>(1);
    WeakReference<Integer> value = new WeakReference<>(2);
    map.put(key,value);
    System.out.println("put success");
    Thread.sleep(1000);
    System.gc();
    System.out.println("get " + map.get(key).get());
}
```

输出

```java
put success
get 2
```

但是这里为什么又可以输出2呢，因为在 cache 中维护了 `Integer` 的 -128 ~ 127 的强引用。

## 线程池 ***

### 什么是线程池？为什么使用线程池

线程池是一种多线程处理形式，处理过程中将任务提交到线程池，任务的执行交给线程池来管理。

> 为什么使用线程池？

- **降低资源消耗**，通过重复利用已创建的线程降低线程创建和销毁造成的消耗。
- **提高响应速度**，当任务到达时，任务可以不需要等到线程创建就立即执行。
- **提高线程的可管理性**，线程是稀缺资源，如果无限制地创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以统一分配。

### 创建线程池的几种方法

线程池的常用创建方式主要有两种，通过Executors工厂方法创建和通过new `ThreadPoolExecutor`方法创建。

- Executors工厂方法创建，在工具类 Executors 提供了一些静态的工厂方法

  1. `new SingleThreadExecutor` ：创建一个单线程的线程池。适用于多个任务顺序使用的场景
  2. `new FixedThreadPool` ：创建固定大小的线程池。 适用于任务量固定耗时长的任务。
  3. `new CachedThreadPool` ：创建一个可缓存的线程池。适合任务量大但耗时少的任务。
  4. `new ScheduledThreadPool` ：创建一个大小无限的线程池。适合固定周期的定时任务或重复任务。
- new `ThreadPoolExecutor` 方法创建： 通过 new ThreadPoolExecutor(int corePoolSize, int maximumPoolSize, long keepAliveTime, TimeUnit unit, BlockingQueue< Runnable> workQueue，ThreadFactory threadFactory,RejectedExecutionHandler handler) 自定义创建



> 阿里巴巴开发规约

![image-20220816175126251](a-3Java%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B.assets/image-20220816175126251.png)

### ThreadPoolExecutor构造函数的重要参数分析

三个比较重要的参数：

- corePoolSize ：核心线程数，定义了最小可以同时运行的线程数量。
- maximumPoolSize ：线程中允许存在的最大工作线程数量
- workQueue ：存放任务的阻塞队列。新来的任务会先判断当前运行的线程数是否到达核心线程数，如果到达的话，任务就会先放到阻塞队列。

其他参数：

- keepAliveTime ：当线程池中的数量大于核心线程数时，如果没有新的任务提交，核心线程外的线程不会立即销毁，而是会等到时间超过 keepAliveTime 时才会被销毁。
- unit ： keepAliveTime 参数的时间单位。
- threadFactory ：为线程池提供创建新线程的线程工厂。
- handler ：线程池任务队列超过 maxinumPoolSize 之后的拒绝策略

### ThreadPoolExecutor的饱和策略（拒绝策略）

当同时运行的线程数量达到最大线程数量并且阻塞队列也已经放满了任务时， `ThreadPoolExecutor` 会指定一些饱和策略。主要有以下四种类型：

- `AbortPolicy` ：该策略会直接抛出异常拒绝新任务
- `CallerRunsPolicy` ：当线程池无法处理当前任务时，会将该任务交由提交任务的线程来执行。
- `DiscardPolicy` ：直接丢弃新任务。
- `DiscardOleddestPolicy` ：丢弃最早的未处理的任务请求。

线程池的执行流程
创建线程池创建后提交任务的流程如下图所示

<img src="https://cdn.jsdelivr.net/gh/hxznh/images@main/image-20220816180248736.png" alt="image-20220816180248736" style="zoom: 67%;" />

### execute()方法和submit()方法的区别

这个地方首先要知道Runnable接口和Callable接口的区别，之前有写到过 execute() 和 submit() 的区别主要有两点：`execute()` 方法只能执行 Runnable 类型的任务。 submit() 方法可以执行 Runnable 和 Callable 类型的任务。`submit()` 方法可以返回持有计算结果的 Future 对象，同时还可以抛出异常，而 execute() 方法不可以。
换句话说就是， execute() 方法用于提交不需要返回值的任务， submit() 方法用于需要提交返回值的任务。

## <span id='CAS'>CAS</span>

### 什么是CAS?

CAS 即 `Compare And Swap`，翻译成中文即比较并替换。Java中可以通过CAS操作来保证原子性，原子性就是不可被中断的一些列操作或者一个操作，简单来说就是一系列操作，要么全部完成，要么失败，不能被中断。

CAS 操作包含三个操作数 ——  内存位置（V）、预期原值（A）和新值(B)。如果内存位置的值V与预期原值A相匹配，那么处理器会自动将该位置值V更新为新值B，否则，处理器不做任何操作，整个操作保证了原子性，即在对比V==A后、设置V=B之前不会有其他线程修改V的值。

当多个线程同时操作一个共享变量时，只有一个线程可以对变量进行成功更新，其他线程均会失败，但是失败并不会被挂起，进行再次尝试，也就是**自旋**。Java中的自旋锁就是利用CAS来实现的。

### CAS存在的问题

> ABA问题

在CAS的算法流程中，首先要先比较V的值和E的值，如果相等则进行更新。ABA问题是指，E表示的这个旧值本来是A，然后变成了B，后来又变成了A，但这时有线程来更新，发现E表示的值是A，则直接进行更新了，这样肯定是不对的，但又该怎么解决呢？

- 解决方式：ABA的解决方法也很简单，就是利用版本号。给变量加上一个版本号，每次变量更新的时候就把版本号加1，这样即使E的值从A—>B—>A，版本号也发生了变化，这样就解决了CAS出现的ABA问题。基于CAS的乐观锁也是这个实现原理。

> 循环时间过长导致开销太大

CAS自旋时间过长会给CPU带来非常大的开销

> 只能保证一个共享变量的原子操作

在操作一个共享变量时，可以通过CAS的方式保证操作的原子性，但如果对多个共享变量进行操作时，CAS则无法保证操作的原子性，这时候就需要用锁了。在看《Java并发编程的艺术》时，里面提到了一个办法可以参考一下，就是将多个共享变量合并成一个共享变量来操作。比如，有两个共享变量i=2,j=a ，合并成 ij=2a ，然后用CAS来操作 ij

### CAS的优点

在并发量不是很大时可以提高效率。

## Atomic 原子类

> 原子操作类是CAS在Java中的应用，从JDK1.5开始提供了 java.util.concurrent.atomic 包，这
> 个包中的原子操作类提供了一种用法简单、性能高效、线程安全地更新一个变量的方式。Atomic
> 包里的类基本都是使用 Unsafe 实现的包装类。

JUC包中的4种原子类

- 基本类型：使用原子的方式更新基本类型
  - AtomicInteger ：整形原子类
  - AtomicLong ：长整型原子类
  - AtomicBoolean ：布尔型原子类
- 数组类型：使用原子的方式更新数组里的某个元素
  - AtomicIntegerArray ：整形数组原子类
  - AtomicLongArray ：长整形数组原子类
  - AtomicReferenceArray ：引用类型数组原子类
- 引用类型：
  - AtomicReference ：引用类型原子类，存在ABA问题
  - AtomicStampedReference：原子更新带有版本号的引用类型。该类将整数值与引用关联起来，可用于原子的更新数据和数据的版本号，可以解决使用CAS进行原子更新时可能出现的ABA问题。
  - AtomicMarkableReference：原子更新带有标记位的引用类型
- 原子更新字段类
  - AtomicIntegerFieldUpdater：原子更新整型的字段的更新器。
  - AtomicLongFieldUpdater：原子更新长整型字段的更新器。
  - AtomicReferenceFieldUpdater：引用类型更新器原子类

## <span id='AQS'>AQS</span>

AQS这部分相对复杂一些，想要深入理解需要去阅读源码，篇幅有限，这里就不展开介绍了。

### 什么是AQS？

AQS的全称是`AbstractQueuedSynchronizer`，是一个用来构建锁和同步器的框架，像 ReentrantLock，Semaphore，FutureTask都是基于AQS实现的。

> AQS的原理

简单来说，AQS就是维护了一个共享资源，然后使用队列来保证线程排队获取资源的一个过程。
AQS的原理图如下：

<img src="https://cdn.jsdelivr.net/gh/hxznh/images@main/image-20220816210033213.png" alt="image-20220816210033213" style="zoom:67%;" />

AQS的工作流程：当被请求的共享资源空闲，则将请求资源的线程设为有效的工作线程，同时锁定共享资源。如果被请求的资源已经被占用了，AQS就用过队列实现了一套线程阻塞等待以及唤醒时锁分配的机制。

这个队列是通过CLH队列实现的，从上图可以看出，该队列是一个双向队列，有Node结点组成，每个 Node结点维护一个prev引用和next引用，这两个引用分别指向自己结点的前驱结点和后继结点，同时 AQS还维护两个指针Head和Tail，分别指向队列的头部和尾部。

从上图可以看出，AQS是维护了一个共享资源和一个FIFO的线程等待队列。

> CLH同步队列

CLH 是 *Craig、Landin 和 Hagersten* 三位作者的缩写

是一个FIFO双向队列，AQS依赖它来完成同步状态的管理，当前线程如果获取同步状态失败时，AQS则会将当前线程已经等待状态等信息构造成一个节点（Node）并将其加入到CLH同步队列，同时会阻塞当前线程，当同步状态释放时，会把首节点唤醒（==公平锁==），使其再次尝试获取同步状态。

```java
private volatile int state;
```

通过 `volatile` 来保证state的线程可见性，state的访问方式主要有三种，如下

```java
protected final int getState() { //获取state的值
    return state;
} 
protected final void setState(int newState) { //设置state的值
    state = newState;
} 
protected final boolean compareAndSetState(int expect, int update) { 
    //通过CAS操作更新state的值
    return unsafe.compareAndSwapInt(this, stateOffset, expect, update);
}
```

### AQS的资源共享方式有哪些？

- Exclusive：独占，只有一个线程可以执行，例如ReentrantLock
- Share：共享，多个线程可同时执行，如Semaphore/CountDownLatch

### 如何使用AQS自定义同步器？

AQS的底层使用了模板方法模式，自定义同步器只需要两步：

1. 继承`AbstractQueuedSynchronizer`
2. 重写以下几种方法：
   - isHeldExclusively()：该线程是否正在独占资源。只有用到condition才需要去实现它。
   - tryAcquire(int)：独占方式，尝试获取资源。
   - tryRelease(int)：独占方式，尝试释放资源。
   - tryAcquireShared(int)：共享方式，尝试获取资源。负数表示失败，0表示成功，但无剩余可用资源，正数表示成功并且有剩余资源
   - tryReleaseShared(int)：共享方式，尝试释放资源

下面举例说明，以独占式的 `ReentrantLock` 为例， `state` 初始状态为0，表示未锁定状态。A线程进行 `lock()` 时，会调用 `tryAcquire()` 独占该锁并将 `state+1`。此后，其他线程再调用 `tryAcquire()` 时就会失败，直到A线程 `unlock()` 到state=0（即释放锁）为止，其它线程才有机会获取该锁。当然，释放锁之前，A线程自己是可以重复获取此锁的（state会累加），这就是可重入的概念。但要注意，获取多少次就要释放多少次，这样才能保证state是能回到零态的。

再以共享式的 `CountDownLatch` 以例，任务分为N个子线程去执行，state也初始化为N（注意N要与线程个数一致）。这N个子线程是并行执行的，每个子线程执行完后 `countDown()` 一次，state会CAS减1。等到所有子线程都执行完后(即state=0)，会 `unpark()` 主调用线程，然后主调用线程就会从 `await()` 函数返回，继续后续动作。一般来说，自定义同步器要么是独占方法，要么是共享方式，他们也只需实现 `tryAcquiretryRelease` 、`tryAcquireShared-tryReleaseShared` 中的一种即可。但AQS也支持自定义同步器同时实现独占和共享两种方式，如 `ReentrantReadWriteLock` 。

`CountDownLatch` ：https://blog.csdn.net/hbtj_1216/article/details/109655995

AQS参考博客：https://www.cnblogs.com/waterystone/p/4920797.html