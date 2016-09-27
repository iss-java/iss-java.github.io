---
layout:     post
title:      "Java Thread Tutorial"
date:       2016-09-27 13:00:00
author:     "汤汉"
header-img: "img/post1.jpg"
header-mask: 0.3
catalog:    true
---

## Java 线程

#### 何为线程

[Java Document](https://docs.oracle.com/javase/tutorial/essential/concurrency/procthread.html) 中关于进程和线程的解释，进程与线程都能提供一个执行环境。所谓执行环境，就是堆栈、代码段、数据段等内存空间，寄存器等CPU资源，以及文件描述符等I/O接口。然而两者最大的不同在于，操作系统以进程为单位分配上述系统资源，而线程必须依附于进程，只能共享进程中的资源。
若还是模糊，推荐几篇博文增强理解：
[进程与线程的一个简单解释](http://www.ruanyifeng.com/blog/2013/04/processes_and_threads.html)
[进程和线程的区别](http://www.cnblogs.com/lmule/archive/2010/08/18/1802774.html)

#### 在Java中创建线程

- 继承Thread类
	- 复写run方法
	- 创建该类实例
	- 调用线程实例的**start方法**，启动线程。

```
/**
 * 继承Thread并且复写run方法
 */
class InheritanceThread extends Thread{
    private String name = null;
    public InheritanceThread(String name){
        this.name = name;
    }
    public void run(){
        while(true) {
            System.out.printf(name + " thread is running\n");

        }
    }
}
public class Main {
    public static void main(String args[]){
        InheritanceThread thread1 = new InheritanceThread("First");
        InheritanceThread thread2 = new InheritanceThread("Second");
        thread2.start();
        thread1.start();
    }
}

/** Output
First thread is running
First thread is running
Second thread is running
Second thread is running
First thread is running
Second thread is running
*/
```

- 实现Runnable接口来实现多线程
	- 定义Runnable接口的实现类，并且实现该接口的run方法
	- 创建Runnable接口实现类的实例
	- 调用实例对象的start方法，启动线程。

<br>

```
/**
 *实现Runnable接口run函数
 */
class RunnableThread implements Runnable{
    private String name = null;

    public RunnableThread(String name){
            this.name = name;
    }
    /**
    *复写run函数
    */
    @Override
    public void run() {
        while(true) {
            System.out.println(name + " thread is running");
        }
    }
}

public class Main {
    public static void main(String args[]){
        RunnableThread r1 = new RunnableThread("First");
        RunnableThread r2 = new RunnableThread("Second");
        Thread t1 = new Thread(r1);
        Thread t2 = new Thread(r2);
        t1.start();
        t2.start();
    }
}

/** output **
First thread is running
Second thread is running
First thread is running
Second thread is running
Second thread is running
First thread is running
Second thread is running
*/
```

继承Thread类复写run方法，和实现Runnable的run方法，是最简单的两种线程创建方法。这两种方法创建的线程没有返回值。

细心的同学应该有疑惑，这两种方法中我们实现或者复写的都是run方法，但是在执行线程的时候调用的不是run，而是start方法。如果我们直接调用run方法，会有什么情况发生呢？我们做个试验。
<br>

```
/**
*继承Thread类
*/
public class InheritanceThread extends Thread{
    private String name = null;
    public InheritanceThread(String name){
        this.name = name;
    }
    public void run(){
        int count = 1;
        while(true) {
            if (count > 4) {
                break;
            }
            System.out.printf(name + " thread is running\n");
            count += 1;
        }
    }
}

/**
*实例化并调用run方法
*/
public static void main(String args[]) {
    InheritanceThread t1 = new InheritanceThread("First");
    InheritanceThread t2 = new InheritanceThread("Second");
    t1.run();
    t2.run();
}

/** Output **
First thread is running
First thread is running
First thread is running
First thread is running
Second thread is running
Second thread is running
Second thread is running
Second thread is running
**/
```

会发现，在直接调用线程的run方法的时候，两个线程会顺序执行，即在线程t1没有执行完之前不会调用线程t2。实际上，start方法会调用操作系统的API，从而创建一个新的线程环境（堆栈等），并在该环境中执行run()。

#### 多线程的危险性

多线程执行的结果是混乱的，这是由于CPU调度算法所致：线程在进入就绪状态后，等待着CPU的调度，只有成功获取CPU的线程才能够执行，执行中的线程也可能因为某种原因而被剥夺或者主动放弃CPU资源给其他线程使用。一般而言，由于系统中其他线程的干扰，线程的执行情况是不可预测的，即无法确定线程在下一刻是等待状态还是执行状态。

扩展阅读：

[Java Doucment:Tread.State](https://docs.oracle.com/javase/7/docs/api/java/lang/Thread.State.html)
[一张图让你看懂JAVA线程间的状态转换](一张图让你看懂JAVA线程间的状态转换)

前面说过，多线程执行的结果是不确定的。对于如下的例子，开两个线程对同一个整形变量分别进行加法和减法，屏幕上面会显示出什么结果呢?
1. 若加线程在减线程之前获得CPU，并且能够执行完毕，那么屏幕将打印出原始变量的内容；
2. 若减线程在加线程之前获取CPU，并且能够执行完毕，那么屏幕将会答应出修改后的变量的内容；
3. 若加线程和减线程交替获取CPU，那么变量的值则会难以预测。


试验代码如下：

```
public class Main {

	public static int x = 50;

    public static void main(String args[]) throws InterruptedException {
        Thread ta = new Thread() {
        	@Override
        	public void run() {
        		for (int i = 0; i < 1000000; ++i) {
        			x += 10;
        		}
        	}
        };
        Thread tb = new Thread() {
        	@Override
        	public void run() {
        		for (int i = 0; i < 1000000; ++i) {
        			x -= 10;
        		}
        	}
        };
        ta.start();
        tb.start();
		// Ten seconds are enough for the two threads to finish
		Thread.sleep(10000);
		System.out.printf("Final result of x = %d\n", x);
	}
}


/** output **
Final result of x = 161480
**/
```

根据加减法交换律，结果应该是50才对，但是结果却是161480。多次运行该程序，结果是随机的。这就验证了，两个线程不加保护地访问数据就会破坏数据。数据损坏的后果是很严重的，为此我们必须对线程访问数据进行控制，于是引入了锁(Lock)的概念。我们在本文中只介绍两种相对简单的锁，互斥锁和读写锁。

#### Synchronized互斥锁

互斥锁是最简单、也是并发度最低的锁模型。互斥锁保证了等待锁的多个线程中，只有一个线程能够执行加锁的代码。

`synchronized`关键字和括号中的**对象**共同构成了一个同步块。**多个线程执行到同步块时，只有一个线程能够进入同步块**。当这个线程执行完同步块中的代码时，JVM自动调度下一个进入同步块的线程。

括号中的对象没有实际意义，可以是`this`，也可以是任何（在当前作用域可用的）对象。这么说吧，`synchronize(this)`是一个锁，而`synchronize(obj)`是另外一个锁。
	
下面我们用同步块来改良一下多线程加减法的代码。

```

public class Main {

	public static int x = 50;
	public static final Object obj = new Object();

    public static void main(String args[]) throws InterruptedException {

        Thread ta = new Thread() {
        	@Override
        	public void run() {
        		for (int i = 0; i < 1000000; ++i) {
                    synchronized (obj) {
						x += 10;
					}
        		}
        	}
        };

        Thread tb = new Thread() {
        	@Override
        	public void run() {
        		for (int i = 0; i < 1000000; ++i) {
					synchronized (obj) {
						x -= 10;
					}
        		}
        	}
        };

        ta.start();
        tb.start();
		// Ten seconds are enough for the two threads to finish
		Thread.sleep(10000);
		System.out.printf("Final result of x = %d\n", x);
	}
}

/** output **
Final result of x = 50
*/
```

现在有了同步之后，线程就不会破坏数据了。

#### ReentrantLock 互斥锁

ReentrantLock是另外一个常用的互斥锁实现。和synchronize相比，ReentrantLock有如下特性

- 计数：当前线程可以多次请求该锁，锁不会阻塞，而会增加计数。这就是类名中`Reentrant`的含义。
- 手动释放：需要在当前线程中调用unlock函数来释放锁。**【调用unlock的次数要和调用lock的次数应该相同。】**
- 适合非块结构：也就是**可以在一个函数中获得锁，在另一个函数中释放锁**。这用synchronized是做不到的。
- 异步地获得锁：由于lock调用可能阻塞，因此还有一个`boolean trylock()`方法，如果锁被别的线程占用了，就返回false，否则获得锁并返回true。
- 限时用锁：`boolean trylock(long time, TimeUnit unit)`在给定的时间内占据锁，超过时间就自动释放。

要注意的是，如果其他线程调用lock时，锁计数大于0，就会阻塞，直到计数等于0时才能获得锁。

```
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class Main {

	public static int x = 50;
    public static Lock lock = new ReentrantLock();

    public static void main(String args[]) throws InterruptedException {

        Thread ta = new Thread() {
        	@Override
        	public void run() {
        		for (int i = 0; i < 1000000; ++i) {
					lock.lock();
					try {
						x += 10;
					}
					finally {
						lock.unlock();
					}
				}
        	}
        };

        Thread tb = new Thread() {
        	@Override
        	public void run() {
        		for (int i = 0; i < 1000000; ++i) {
                    lock.lock();
					try {
						x -= 10;
					}
					finally {
						lock.unlock();
					}
				}
        	}
        };

        ta.start();
        tb.start();
		// Two seconds are enough for the two threads to finish
		Thread.sleep(10000);
		System.out.printf("Final result of x = %d\n", x);
	}
}

/** output **
Final result of x = 50
*/
```


#### ReadWriteLock 读写锁

读写锁能提供更高的并发度，因为读写锁允许【读-读并发】。但是为了保护数据，还是要做到【读-写互斥】和【写-写互斥】。java中的`ReentrantReadWriteLock`提供了一个可重入的读写锁实现。

```
import java.util.Random;
import java.util.concurrent.locks.ReadWriteLock;
import java.util.concurrent.locks.ReentrantReadWriteLock;

/**
 * Created by maxtang on 16-9-24.
 */
class Data {

    ReadWriteLock rwlock = new ReentrantReadWriteLock();

    // 临界数据，也就是要被并发访问的数据
    private Object data = null;

    /**
     * 读数据，可以多个线程同时读，所以上读锁即可
     */
    public void get() {
        rwlock.readLock().lock();

        try {
            System.out.println(Thread.currentThread().getName() + "准备读数据!");
            System.out.println(Thread.currentThread().getName() + "读出的数据为 :" + data);
        } finally {
            rwlock.readLock().unlock();
        }

    }

    /**
     * 写数据，多个线程不能同时写，所以必须上写锁
     *
     * @param data
     */
    public void put(Object data) {

        /* 上写锁 */
        rwlock.writeLock().lock();

        try {
            System.out.println(Thread.currentThread().getName() + " 准备写数据!");
            this.data = data;
            System.out.println(Thread.currentThread().getName() + " 写入的数据: " + data);
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            rwlock.writeLock().unlock();
        }
    }
}

class WriteThread extends Thread {
    private Data data = null;

    public WriteThread(Data data) {
        this.data = data;
    }

    public void run() {
    	// 写入一个随机数
        this.data.put(new Random().nextInt(8));
    }
}

class ReadThread extends Thread {
    private Data data = null;

    public ReadThread(Data data) {
        this.data = data;
    }

    public void run() {
        this.data.get();
    }
}

/**
 * 主函数
 */

public class Main {
    public static void main(String args[]) {
        /* 创建ReadWrite对象 */
        final Data data = new Data();

        /* 创建并启动3个读线程 */
        ReadThread t1 = new ReadThread(data);
        ReadThread t2 = new ReadThread(data);
        ReadThread t3 = new ReadThread(data);


        /*创建3个写线程*/
        WriteThread w1 = new WriteThread(data);
        WriteThread w2 = new WriteThread(data);
        WriteThread w3 = new WriteThread(data);

        t1.start();
        t2.start();
        t3.start();

        w1.start();
        w2.start();
        w3.start();
    }
}

/** output **
Thread-0准备读数据!
Thread-0读出的数据为 :null
Thread-4 准备写数据!
Thread-4 写入的数据: 3
Thread-5 准备写数据!
Thread-5 写入的数据: 2
Thread-3 准备写数据!
Thread-3 写入的数据: 7
Thread-1准备读数据!
Thread-1读出的数据为 :7
Thread-2准备读数据!
Thread-2读出的数据为 :7
*/
```

由结果我们可以看出，读数据时候可以多线程同时访问，而在涉及到写数据时候，只能存在一个写线程访问。如同前文所述的ReentrantLock，读写锁在执行完毕后，也要手动释放锁。
