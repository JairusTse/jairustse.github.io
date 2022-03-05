---
layout: post
title: 多线程2：Android消息机制
category: android
tags: [android,Handler,AsyncTask]
---


Handler消息机制是Android特有的消息机制。


## Handler消息机制

Android应用程序有几个不同于服务端的Java程序：
- 主线程不能执行耗时操作，主要是为了界面不卡顿；
- 网络请求等耗时请求，执行完后要通知主线程更新；
- 主线程没有任务时需要阻塞，等待新任务，不能退出。

Handler消息机制就是为了Android程序量身定做的一套消息机制。

Handler消息机制里面有几个角色：
- Handler：发消息和处理消息，一般谁发就谁处理；
- Looper：循环器，不断的从MessageQueue中取出Message交给Handler处理；
- MessageQueue：消息队列，里面有多个Message；
- Message：消息

> __每个线程有1个Looper，每个Looper有1个MessageQueue，MessageQueue里面的消息都是按顺序处理的，每个Message都有1个对应处理的Handler。每个Looper可以有多个Handler。__

> __Handler能在指定执行中的线程执行任务（一般只指定在主线程上执行）。__

_UI线程也是一个HandlerThread_


### ThreadLocal
每个线程独有的变量，跟其他线程不共享

```
ThreadLocal<Integer> threadNum = ThreadLocal<>();
threadNum.set(1);
threadNum.get();
```

__作用：使用ThreadLocal<Looper> 来保存每个线程的Looper__

> 获取当前线程的Looper `Looper.myLooper()`
> 获取主线程的Looper `Looper.getMainLooper()`


## AsnycTask的内存泄漏
AsyncTask是Android官方提供的一个线程工具，__在Android11已经弃用;__
内存泄漏的原因：内部类会隐式持有外部类对象的引用，因为AsyncTask会启动新线程，持有的对象引用在线程结束之前不会被释放，所以一般建议使用static。但是由于 __AsyncTask一般只用来做短时间的任务，__ 晚点释放也问题不大，所以说会造成内存泄漏有点夸张。


## Thread、Executor、AsyncTask、HandlerThread、InterService该用哪个

- __`Executor`：尽量用这个；__
- `Thread`：尽量不直接用，不方便；
- `AsyncTask`：已废弃（适用于短任务）；
- `HandlerThread`：和`Executor`差不多，稍微复杂一点（单线程，多线程还是要用`Executor`）；
- `InterService`：比较重（持有上下文信息），单次后台任务，需要用到上下文才用




## Service、IntentService该用哪个
- Service：后台线程活动的空间，需要不断跟后台线程交互用它
- IntentService：即是一个后台任务，也有上下文信息，跟Service不算同一个工具。




完毕。