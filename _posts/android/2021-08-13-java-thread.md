---
layout: post
title: 多线程1：Java多线程
category: android
tags: [android,java,Thread]
---

Java多线程

## 线程基础
#### 进程和线程
进程：进程是一个程序，是一个静态的概念。不同进程间的资源是独立的。程序开始运行就是主线程开始执行。
线程：是使用资源的最小单位，一个进程里面可以开启多个线程，多个线程共享进程的资源。


## Java提供的线程类

#### Thread和Runnable
`Thread` 是Java提供的最基础的线程类，通过`thread.start();` 可以启动一个线程。线程启动后就可以去排队使用CPU资源。直接调用`thread.run()` 只是方法调用，不会启动新线程。 

`Runnable` 是Java提供的接口，线程任务写在`Runnable`的`run()`方法里面，作为参数传给`thread`，可以多次复用。

###### 线程开始和结束：
`thread.start();`：启动一个新线程
`run()` 方法执行完，线程就正常结束。
###### 终止线程的方法：
`thread.stop();`：线程马上停止（会导致资源状态不可预测，已经废弃，不要使用）
`thread.interrupt();`：线程标记为终止，需要被标记的线程自己判断状态并终止，比较友好
`InterruptedException`：线程的中断异常，比如线程正在sleep，被标记为`interrupt`就会抛出这个异常，线程可以处理并终止。


建议的终止方法：
```
@Override
public void run() {
    while(!Thread.currentThread().isInterrupted()) {
        //线程监听到中断，执行完当次循环后退出
    }
}
```

###### 线程的其他常用方法：
`Thread.sleep(3000); `：静态方法，在哪个线程调用就哪个线程休眠
`thread1.join();`：在当前线程插入thread1，thread1执行完后才继续执行（并行变成串行）
`thread.yield();`：给同优先级的其他线程让出一下CPU等待时间

##### 线程优先级：

1-10，越大优先级越高，默认是5。优先级高的得到的CPU执行时间多。
`Thread.MIN_PRIORITY = 1;`  
`Thread.NORM_PRIORITY = 5;`  
`Thread.MAX_PRIORITY = 10;`


#### ThreadFactory
工厂方法创建线程，统一使用ThreadFactory来创建thread



#### Callable
一个有返回值的Runable，可以阻塞式获取返回值，常用的场景是在主线程有个while循环，里面有等待callable的返回值，获取到后跳出循环。

```
@Override
public String call() {
    //do something
    return str;
}

Future<String> future = executor.submit(callable);
//阻塞式获取返回值
String result = future.get();

```

#### Executor
创建线程池(可以自定义线程池的最小数、最大数，存活时间)，来启动线程。

__现在基本上不直接使用Thread和Runnable使用线程，而是用Executor。__

```
ExecutorService executors = Executors.newCachedThreadPool();
executors.execute(runnable);//直接调用这个方法就可以启动线程，简单方便好用
executors.shutDown(); //执行完线程再结束
executors.shutDownNow(); //马上结束（用interrupt设置标记来结束线程，也不是马上结束）
```



## Java线程同步

#### 线程安全
什么是线程安全？
> 多个线程访问同一个资源，导致资源出错。

为什么会有线程安全问题？
> 每个线程都有独立的空间，为了减少交互，会把内存的值拷贝到自己的空间，处理完再写回去（不同步）

#### 线程同步
线程同步是为了解决多线程安全问题的一种方案。
> 线程在访问一个资源的时候，对资源上锁，其他线程不能访问。锁释放后才可以被其他线程访问。


#### synchronized（互斥锁）

```
//synchronized修饰符能使方法具有同步性和互斥访问
private synchronized void count() {
    x++;
}
```

`synchronized` 是通过给对象加锁，实现互斥访问的，给方法加上synchronized关键字，默认加锁的对象就是类的对象本身。


__单例模式经常使用到`synchronized`，防止线程安全问题。__

```
class SingleMan {
    //volitile打开SingleMan对象的同步性，防止没有初始化完成就可以用
    private static volitile SingleMan sInstance;
    
    private SingleMan() {
    }
    
    static SingleMan newInstance() {
        if(sInstance == null) {
            synchronized(SingleMan.class) {
                //第二次判断，如果多个线程排队，只有第一个才会创建对象
                if(sInstance == null) {
                    sInstance = new SingleMan();
                }
            }
        }
        return sInstance;
    }
}

```


##### 死锁
两个互斥锁等待对方的锁释放。

##### 乐观锁/悲观锁
__不是线程问题，是数据库并发控制问题。__
乐观锁：不加锁，发现资源被改了丢弃前面的处理，重新处理一遍。（适合冲突少的场景）
悲观锁：每次都加锁，牺牲速度换安全性。

##### 可重入锁ReentrantLock
可以根据需要加读锁或者写锁，增加效率。
读锁：加一个读锁，其他线程也可以进来读（多个线程可以一起读资源）

#### volatile
打开同步，每次拿之前都先读一次，处理完马上写回去（积极同步），性能降低但是更安全了。需要自己判断是否要打开同步，有多个线程操作同一个变量，有可能出错时才打开。

#### 原子操作
原子操作：不可拆的操作。多线程切换可以在任意非原子操作期间切换。
`x++` 是分两步执行的，所以不是一个原子操作。

`AtomicInteger count = 0` 是具有原子性和同步性的Int；
`count.incrementAndGet()`：//具有原子性和同步性的++count
`count.getAndIncrement()`：//具有原子性和同步性的count++
`AtomicReference<User>`：//具有原子性和同步性的User对象




## Java线程间通信
线程间通信指的是协同使用共享资源，而不是真正的切换线程。

#### 一个线程启动另一个线程
`thread.start()`
#### 一个线程终结另一个线程
`thread.stop()` ：已经被弃用（强停，结果不可预测）
`thread.interrupt()` ：标记为中断（需要被中断的线程主动检查标记，主动结束）

```
//isInterrupted可以多次判断，在耗时操作之前判断，终止之前可以做收尾工作
if(thread.isInterrupted()) {
    return;
}
```

`InterruptException`：等待中的线程被中断，就会抛出这个异常，可以马上停止等待收尾并退出

#### 两个线程互相配合
`thread.wait()`：线程等待某个条件，进入等待区（`wait`和`notify/notifyAll`要同时使用）
`thread.notify()`：随机唤醒一个在等待区的线程
`thread.notifyAll()`：唤醒等待区所有的线程，全部重新排队（通常用这个）
`thread.join()`：在当前线程插入另一个线程，也是一种等待，把并行的两个线程变成串行
`thread.yield()`：当前线程短暂让出等待给 __同优先级__ 的线程



完毕。