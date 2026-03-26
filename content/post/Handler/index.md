---
title: "Handler"
description: 
date: 2026-03-24T12:27:05Z
image: 
math: 
license: 
comments: true
build:
    list: always    # Change to "never" to hide the page from the list
---
# Handler学习

在Android开发中，消息传递和线程通信是常见的需求。而Android的Handler机制就是用来实现这种消息传递和线程通信的重要组件。它在Android应用开发中扮演者至关重要的角色。

## 1.Handler组件

### Handler的重要性：

1.**线程间通信**：Android应用通常会涉及到多个线程的并发执行，而Handler机制提供了一种简单而高效的方式来实现线程间的通信。通过Handler，我们可以将信息发送到目标线程的消息队列中，并在目标线程中处理消息，从而实现线程间的通信和合作。

2.**异步消息处理**：在Android开发中，许多任务需要在后台线程中执行，完成后将结果更新到UI界面上。Handler机制允许我们在后台线程中处理任务，并通过Handler将结果发送到UI线程，以便更新UI界面。这种异步消息处理的机制在*保持UI的响应性和避免阻塞主线程*方面起到了至关重要的作用。

3.**定时任务处理**：Handler机制还可以用于处理定时任务。我们可以使用Handler的postDelayed()方法来延迟代码块或发送延时消息。这对于实现定时刷新、定时执行任务等场景非常有用。

### Handler应用场景：

1.**UI更新**：当后台任务完成之后，通过Handler机制将结果发送到UI线程，以更新UI界面，如显示加载完成的数据，更新进度条等。

2.**后台任务处理**：通过Handler机制，可以将后台任务发送到子线程来处理，避免主线程阻塞，从而保持UI的流畅性，如网络请求、文件读写等。

3.**定时任务**：通过Handler的定时任务功能，可以实现定时执行代码块或延时发送消息，如定时刷新、定时通知等。

4.**线程间通信**：通过Handler机制，在不同线程间发送消息和处理消息，实现线程间的通信和协作，如主线和后台线程之间的通信。

## 2.Handler机制概述

### Handler、Message和Looper的基本概念

**Handler**

Handler是Android中的消息处理器，它负责接收和处理消息。每个Handler实例都关联一个特定的线程，并与该线程的消息队列相关联。通过Handler，我们可以发送和处理消息，实现线程间的通信和协作。

**Message**

Message是Handler传递的消息对象，用于在不同线程之间传递数据。它包含了要传递的数据和附加信息，如消息类型、标志等。Message对象可以通过Handler的sendMessage()方法发送到目标线程的消息队列中，并在目标队列中被处理。

**Looper**

Looper是一个消息循环器，它用于管理线程的消息队列。每个线程只能有一个Looper对象，它负责循环读取消息队列中的消息，并将消息传递给对应的Handler进行处理。Looper的工作方式是不断从消息队列中取出消息，并通过handler的dispatchMessage()方法将消息分发给目标Handler进行处理。

![Handlerwork](handler1.png)
![Looper](looperyark.png)
![messagesend](messagesend.png)

工作原理:

    ·当一个线程需要使用Handler来处理消息时，首先要先创建一个looper对象，并调用其prepare()方法创建一个与当前线程关联的消息队列。

    ·接着，通过Looper的loop()方法启动消息循环，开始循环读取消息队列中的消息。

    ·当有消息通过Handler的sendMessage()方法发送到消息队列时，Looper会不断从消息队列中取出消息，并将其传递给对应的Handler处理。

    ·Handler在接收消息后，会调用自己的handleMessage()方法来处理消息。

## 源码结构

### 1.Handler的源码结构：

```java
public class Handler {
    private Looper mLooper;
    private MessageQueue mQueue;
 
    public Handler() {
        this(Looper.myLooper());
    }
 
    public Handler(Looper looper) {
        mLooper = looper;
        mQueue = looper.getQueue();
    }
 
    public void handleMessage(Message msg) {
        // 处理消息的逻辑
    }
 
    public final boolean sendMessage(Message msg) {
        return sendMessageDelayed(msg, 0);
    }
 
    public final boolean sendMessageDelayed(Message msg, long delayMillis) {
        if (msg.target == null) {
            msg.target = this;
        }
        return mQueue.enqueueMessage(msg, delayMillis);
    }
}
```

### 2.Message的源码结构

```java
public final class Message {
    public int what;
    public Object obj;
    public Handler target;
    // 其他成员变量
 
    public static Message obtain() {
        return new Message();
    }
}
```

### 3.Looper的源码结构

```java
public final class Looper {
    static final ThreadLocal<Looper> sThreadLocal = new ThreadLocal<>();
    MessageQueue mQueue;
 
    private Looper() {
        mQueue = new MessageQueue();
    }
 
    public static void prepare() {
        if (sThreadLocal.get() != null) {
            throw new RuntimeException("Only one Looper may be created per thread");
        }
        sThreadLocal.set(new Looper());
    }
 
    public static Looper myLooper() {
        return sThreadLocal.get();
    }
 
    public static void loop() {
        Looper me = myLooper();
        if (me == null) {
            throw new RuntimeException("No Looper; Looper.prepare() wasn't called on this thread.");
        }
        MessageQueue queue = me.mQueue;
 
        while (true) {
            Message msg = queue.next();
            if (msg == null) {
                return;
            }
            msg.target.dispatchMessage(msg);
            msg.recycle();
        }
    }
 
    public MessageQueue getQueue() {
        return mQueue;
    }
}
```

### 4.关系和协作机制

    ·Handler类通过持有Looper对象和与之关联的MessageQueue实现了消息的发送和处理。

    ·Looper负责管理线程的消息循环，它在loop()方法中不断从MessageQueue中取出消息，并通过Handler的dispatchMessage()方法将消息分发给对应的Handler进行处理。

    ·Message对象封装了要传递的消息和附加信息，包括目标Handler、消息类型等。

    ·当调用Handler的sendMessage()或sendMessageDelayed()方法时，会将Message对象发送到MessageQueue中，等待Looper的循环读取和处理。

    ·在Looper的循环过程中，取出的每个Message对象都会通过diapatchMessage()方法传递给目标Handler，最终由目标Handler的handleMessage()方法处理消息。

## Handler的使用方法

### 1.创建Handler对象：
通过以下方式创建Handler对象：

```java
Handler handler = new handler();
```

此时，Handler会与当前线程的Looper关联。

### 2.发送消息：
使用Handler的sendMessage()或sendMessageDelayed()方法发送消息到消息队列中，供线程处理：

```java
Message msg = handler.obtainMessage();
msg.what = 1;//设置消息类型
msg.obj = "Hello";//设置消息数据
handler.sendMessage(msg);//发送消息
```

上述代码创建了一个消息对象，设置了消息类型和数据，并通过Handler的sendMessage()方法将消息发送到消息队列中。

### 3.处理消息：
在Handler中重写handleMessage()方法，用于处理接收到的消息：

```java
public void handleMessage(Message msg){
    switch (msg.what) {
        case 1:
            String data = (String) msg.obj;//获取消息数据
            //处理消息逻辑
            break;
            //其他消息类型的处理
    }
}
```

在handleMessage()方法中，可以根据不同的消息类型进行逻辑处理。通过msg.obj可以获取消息携带的数据。

### 4.在特定的线程中创建Handler对象

有时需要在特定的线程中创建Handler对象，可以通过Looper来实现。例如，在后台线程中创建Handler对象：

```java
Handler handler;
Thread thread = new Thread(new Runnable() {
    public void run() {
        Looper.prepare();
        handler = new Handler() {
            public void handleMessage(Message msg) {
                //处理消息逻辑
            }
        };
        Looper.loop();
    }
});
thread.start();
```

通过在新的线程中调用Looper的prepare()和loop()方法，为该线程创建一个消息循环器，然后在该循环器中创建了Handler对象。这样，该Handler就与新线程的消息队列相关联，可以接收并处理该线程中的消息。

## 5.Handler的线程安全性

**1.创建Handler的正确方式：**

*· 在主线程中创建Handler：* 在主线程中创建Handler时，无需担心线程安全性问题，因为主线程默认具有一个消息循环器（即主线程的Looper）

*· 在其他线程中创建Handler：* 在其他线程中创建Handler时，需要先调用Looper的prepare()和loop()方法来创建消息循环器，并确保在这个线程中只有一个Looper存在。

**2.处理消息的线程安全：**

*· 避免直接访问UI元素：* 如果Handler用于更新UI元素，确保只在主线程中更新UI。可以使用Handler的post()或postDelayed()方法将更新UI的任务提交到主线程执行。

*· 使用同步机制：* 当多个线程同时访问Handler对象时，需要使用同步机制来保护共享资源，避免数据竞争和线程冲突。

（1）*·使用关键词synchronized：* 在访问Handler的关键代码块或方法上使用synchronized关键字，以确保同一时间只有一个线程访问Handler对象。
（2）*·使用线程安全的数据结构：* 可以使用线程安全的数据结构，如ConcurrentLinkedQueue，来存储消息，以避免多个线程之间的竞争。

**3.确保消息的处理在正确的线程中执行：**

*·发送到主线程的消息：* 如果需要将消息发送到主线程处理，可以使用主线程的Handler，或者使用Activity或Fragment的runOnUiThread()方法。

*·发送到其他线程的消息：* 如果需要将消息发送到其他线程处理，确保该线程拥有正确的Looper和消息循环器。可以使用Handler的构造函数或post()方法指定目标线程的Looper。

**总结**

为了确保Handler在多线程坏境下的线程安全性，需要注意以下几点：

    ·在正确的线程中创建Handler对象。

    ·在访问Handler对象时使用同步机制，如synchronized关键字。

    ·避免直接访问UI元素，使用合适的方法更新UI。

    ·确保消息的处理在正确的线程中执行，根据需要使用适当的Handler和Looper。

## 6.Handler的进阶操作

**1.异步加载数据：**

```java
// 在后台线程中加载数据
Thread thread = new Thread(new Runnable() {
    public void run() {
        // 执行耗时操作，如网络请求或数据库查询
        // 加载完成后，发送消息通知主线程
        Message message = handler.obtainMessage();
        message.what = LOAD_COMPLETE;
        message.obj = loadedData;
        handler.sendMessage(message);
    }
});
thread.start();
 
// 主线程中处理消息
Handler handler = new Handler() {
    public void handleMessage(Message msg) {
        if (msg.what == LOAD_COMPLETE) {
            // 处理加载完成的数据
            Object data = msg.obj;
            // 更新UI等操作
        }
    }
};
```

**2.延时操作：**

```java
Handler handler = new Handler();
handler.postDelayed(new Runnable() {
    public void run() {
        // 延时操作，例如延时2秒后执行某个任务
        // 更新UI等操作
    }
}, 2000); // 2000毫秒的延时
```

**3.UI线程更新:**

```java
Handler handler = new Handler();
handler.post(new Runnable() {
    public void run() {
        // 在UI线程中执行任务，用于更新UI
        // 更新UI等操作
    }
});
```
