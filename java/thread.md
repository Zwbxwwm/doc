# <font color="#9c3">多线程</font>

## 进程与线程

### 区别
    进程是操作系统资源分配的基本单位，而线程是任务调度和执行的基本单位
    直白的说；线程是进程的一部分，一个操作系统有多个进程运行，而一个进程里面有多个线程进行（通过CPU进行调度，每个时间片只有一个线程），线程也成为轻量级进程；

### 进程间通讯
- 无名管道(Pipe)：
  - 管道为半双工的；
  - 管道只能在具有公共祖先的两个进程之间使用，通常，一个管道由一个进程创建，在进程调用fork之后，这个管道就能在父进程和子进程之间使用了。
- 命名管道(FIFO)：
  - 其可以在不相关的程序之间交换数据。
  - FIFO其实是一种文件类型，创建FIFO类似于创建文件；
- 信号量(Semophore)：
  - 它是一个计数器，用于为多个进程提供共享数据对象的访问。
- 消息队列(Message Queue)：
  - 消息队列是消息的链接表，存储在内核中，有消息队列标识符标识。
- 共享内存(Shared Memory)：
  - 共享存储允许两个或多个进程共享一个给定的存储区。
  - 共享内存无须复制，信息量大是其最大的优势。但是需要考虑同步问题。
- 套接字(Socket)：
  - 是一种通信机制，凭借这种机制，客户/服务器（即要进行通信的进程）系统的开发工作既可以在本地单机上进行，也可以跨网络进行。
  - 也就是说它可以让不在同一台计算机但通过网络连接计算机上的进程进行通信。也因为这样，套接字明确地将客户端和服务器区分开来。

### 线程间通讯

- 锁机制
  - 互斥锁：提供了以排它方式阻止数据结构被并发修改的方法。(volatile/reentrantLock)
  - 读写锁：允许多个线程同时读共享数据，而对写操作互斥。(readwriteLock)
  - 条件变量锁：可以以原子的方式阻塞进程，直到某个特定条件为真为止。对条件测试是在互斥锁的保护下进行的。条件变量始终与互斥锁一起使用。
- 信号量机制
  - 无名线程信号量
  - 有名线程信号量
- 管道通信
  - 使用java.io.PipedInputStream 和 java.io.PipedOutputStream进行通信
- wait/notify()机制
------
## 并发编程中的三个概念
    原子性：即一个操作或者多个操作要么全部执行并且执行的过程不会被任何因素打断，要么就都不执行。
    
    可见性:是指当多个线程访问同一个变量时，一个线程修改了这个变量的值，其他线程能够立即看得到修改的值。
    
    有序性：即程序执行的顺序按照代码的先后顺序执行。

## 1、synchronize锁和Lock锁的区别

1.  synchronize的自动释放锁，但是Lock必须手动进行释放，所以这就引发一个问题，如果程序在中间抛出异常，那么相对于synchronize来说，Lock就不会进行unlock，所以每次的解锁都必须放进finally里面进行。
2.  Lock有共享锁的功能，所以可以设置读写锁来提高效率，但是synchronize不能。
3.  Lock锁的事代码块，而synchronize锁不只是代码块还可以是方法锁还有类锁。
4.  Lock可以知道线程有没有获取到锁，但是synchronize不能。

------

##  2、synchronize在jdk1.6是如何进行优化的

1. ​	适应自旋锁，为了减少线程状态改变带来的改变，不停的执行当前线程。
2. ​    锁消除，不可能进行共享数据竞争的锁进消除。
3. ​    锁粗化，对于连续加锁的操作，优化成只加一把锁。
4. ​    轻量级锁，在无竞争条件下，通过CAS进行消除同步互斥
5. ​    偏向锁，在无竞争条件下，消除同步互斥，连CAS都不操作

------

## 3、关于CyclicBarrier的使用介绍

- 从字面来看，Cyclic（可循环利用）Barrier（屏障）。它的功能是让一组线程到达屏障或者是公共点之后被阻塞。直到最后一个线程也到达屏障时，才会打开屏障，所有被屏障拦截的线程才会继续运行。
- 每一个Cyclic类里面都有一个计数器，当一个线程进来之后调用await方法，计数器-1，当计数器归0的时候，放开栅栏；执行所有线程。这就是cyclicBarrier的拦截原理

------

## 4、Java调用线程的几种方法

1.   继承Thread类，重写run()方法
2.   实现Runnable接口
3.   实现callable接口，并用future类包装
4.   使用线程池，如Excutor框架

------

## 5、线程的状态有哪些

1. ​    创建状态（new）单纯创建一个线程，
2. ​    就绪状态（Runnable）线程创建之后，调用Thread的start()方法启动线程，表示线程进入就绪状态，
3. ​    运行状态（running）当线程获取到CPU资源之后进入运行状态，
4. ​    阻塞状态（blocked）线程进入运行状态之后，由于其他原因进入阻塞的状态，如：线程调用sleep()方法之后进入睡眠，wait()方法进入等待，
5. ​    死亡状态（dead）线程正常死亡，

## 6、什么是互斥锁，读写锁，自旋锁，分布式锁

### 互斥锁
    共享资源的使用是互斥的，只有加锁和未加锁的状态。
    当一个线程获得该资源的使用权后便会将其加锁，如使用过程中有其他线程想要获取该资源，就会被阻塞，直到该资源被使用完释放锁才会被唤醒。
### 读写锁
    有三个状态: 读模式下加锁状态, 写模式加锁状态和未加锁状态。
    读模式下，允许其他线程读，但是不允许写；
    写模式下，不允许其他线程的任何操作。
    在读频率高的情况下有更好的性能
### 自旋锁
    自旋锁也是广义上的互斥锁, 是互斥锁的实现方式之一, 它不会产生线程的调度, 而是通过"循环"来尝试获取锁, 优点是能很快的获取锁, 缺点是会占用过多的CPU时间, 这被称为忙等待(busy-waiting)。
### 分布式锁
    在单机情况下, 在内存中的一个互斥锁就能控制到一个程序中所有线程的并发.
    但由于有集群架构(负载均衡/微服务等场景下), 内存中的锁就没用了. 所以我们需要一个"全局锁"去实现控制多个程序/多个机器上的线程并发. 这个全局锁就叫"分布式锁"。

------

## 7、java实现多线程方式继承Thread和实现Runnable接口的区别

1.  使用runnable实现多线程的例子

   ```java
   class MyRunnable implements Runnable {
       private int ticket = 10;
   
       @Override
       public void run() {
           for (int i = 0; i < 50; i++) {
               if (ticket > 0) {
                   System.out.println(Thread.currentThread().getName() + "+sell ticket:" + ticket--);
               }
           }
       }
   }
   
   public class ThreadDemo2 {
       public static void main(String[] args) {
           MyRunnable my = new MyRunnable();
           new Thread(my).start();
           new Thread(my).start();
           new Thread(my).start();
       }
   }
   ```

   ,<font color="blue">结果：三个线程使用一个计数器</font>

   ```java
   Thread-1+sell ticket:10
   Thread-2+sell ticket:8
   Thread-0+sell ticket:9
   Thread-2+sell ticket:6
   Thread-1+sell ticket:7
   Thread-2+sell ticket:4
   Thread-2+sell ticket:2
   Thread-2+sell ticket:1
   Thread-0+sell ticket:5
   Thread-1+sell ticket:3
   ```

2. 使用Thread实现多线程

   ```java
   class MyThread extends Thread {
       private int ticket = 10;
       @Override
       public void run() {
           for (int i = 0; i < 50; i++) {
               if (ticket > 0) {
                   System.out.println(Thread.currentThread().getName() + "+sell ticket:" + ticket--);
               }
           }
       }
   }
   
   public class ThreadDemo2 {
       public static void main(String[] args) {
           new MyThread().start();
           new MyThread().start();
           new MyThread().start();
       }
   }
   ```

   <font color="blue">结果：三个独立的计数器线程</font>

   ```java
   Thread-0+sell ticket:10
   Thread-1+sell ticket:10
   Thread-1+sell ticket:9
   Thread-0+sell ticket:9
   Thread-1+sell ticket:8
   Thread-2+sell ticket:10
   Thread-1+sell ticket:7
   Thread-0+sell ticket:8
   Thread-1+sell ticket:6
   Thread-2+sell ticket:9
   Thread-1+sell ticket:5
   Thread-0+sell ticket:7
   Thread-1+sell ticket:4
   Thread-1+sell ticket:3
   Thread-1+sell ticket:2
   Thread-1+sell ticket:1
   Thread-2+sell ticket:8
   Thread-0+sell ticket:6
   Thread-2+sell ticket:7
   Thread-0+sell ticket:5
   Thread-2+sell ticket:6
   Thread-0+sell ticket:4
   Thread-2+sell ticket:5
   Thread-0+sell ticket:3
   Thread-2+sell ticket:4
   Thread-0+sell ticket:2
   Thread-2+sell ticket:3
   Thread-0+sell ticket:1
   Thread-2+sell ticket:2
   Thread-2+sell ticket:1
   ```

   

3. 区别====》Runnable的有点更好

   - 如上述演示的一样，Runnable适合多个相同代码的程序处理相同的资源
   - 避免单继承带来的局限性
   - 增强代码的健壮性，实现了相同代码的复用性，实现代码和数据的独立

------

## 8、Thread.start()和Thread.run()方法的区别

- start()方法；在调用start()的时候，系统会分配另一个线程来对这个任务进行处理，实现的异步的操作

- run()方法；在调用run()的时候，系统会使用当前线程来对该任务进行处理，这样实现的就是同步操作

  总得来说就是调用run()方法并不能实现多线程的效果，只是顺序执行的效果。



































