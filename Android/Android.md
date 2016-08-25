#Handler
Handler机制相关的类有
- Handler
- Looper
- MessageQueue
- Message
而Looper是通过管道(pipe)实现的。

>关于管道，简单来说，管道就是一个文件。
在管道的两端，分别是两个打开文件文件描述符，这两个打开文件描述符都是对应同一个文件，其中一个是用来读的，别一个是用来写的。
一般的使用方式就是，一个线程通过读文件描述符中来读管道的内容，当管道没有内容时，这个线程就会进入等待状态，而另外一个线程通过写文件描述符来向管道中写入内容，写入内容的时候，如果另一端正有线程正在等待管道中的内容，那么这个线程就会被唤醒。这个等待和唤醒的操作是如何进行的呢，这就要借助Linux系统中的epoll机制了。 Linux系统中的epoll机制为处理大批量句柄而作了改进的poll，是Linux下多路复用IO接口select/poll的增强版本，它能显著减少程序在大量并发连接中只有少量活跃的情况下的系统CPU利用率。

##初始化完成的工作
- Java层，创建Looper对象，Looper的构造函数汇创建消息队列MessageQueue对象。MessageQueue对象用于储存消息队列，管理消息。
- Native层，MessageQueue创建时，会调用JNI函数，初始化NativeMessageQueue。NativeMessageQueue会初始化Looper对象，Looper对象的作用就是，当Java层的消息队列没有消息时，就使Android线程进入等待状态，而当Java层的消息队列中来了消息之后，就唤醒Android应用程序的主线程来处理这个消息。

##发送消息
消息发送最终会调用到`MessageQueue.enqueueMessage()`，根据message.when来确定插入的位置并把消息加入到队列中，分为以下两种情况
- 消息队列为空：此时app主线程应处于等待状态，需要调用wake唤醒。
- 消息队列不为空：不需要唤醒主线程。

##程序为什么不会被Looper.loop()卡死
进程：每个app运行时首先创建一个进程，该进程是由Zygote fork出来的，用于Activity/Service的运行。
线程：线程对应用来说非常常见，比如每次new Thread().start都会创建一个新的线程。该线程与App所在进程之间资源共享，从Linux角度来说进程与线程除了是否共享资源外，并没有本质的区别，都是一个task_struct结构体，在CPU看来进程或线程无非就是一段可执行的代码，CPU采用CFS调度算法，保证每个task都尽可能公平的享有CPU时间片。

##主要调用关系
```
            +------------------+
            |      Handler     |
            +----^--------+----+
                 |        |
        dispatch |        | send
                 |        |
                 |        v
                +----+ <---+
                |          |
                |  Looper  |
                |          |
                |          |
                +---> +----+
                  ^      |
             next |      | enqueue
                  |      |
         +--------+------v----------+
         |       MessageQueue       |
         +--------+------+----------+
                  |      |
  nativePollOnce  |      |   nativeWake
                  |      |
+----------------------------------------------+ Native Layer
                  |      |
       pollOnce   |      |  wake
                  |      |
         +--------v------v--------+
         |   NativeMessageQueue   |
         +--------+------+--------+
                  |      |
         pollOnce |      |  wake
         pollInner|      |  awoken
                  |      |
              +---v------v---+
              |    Looper    |
              +-+----------+-+
                |          |
     epoll_wait |          |  wake
  +-------------v-+      +-v--------------+
  |mWakeReadPipeFd|      |mWakeWritePipeFd|
  +-------------^-+      +-+--------------+
                |          |
          read  |          | write
                |          |
              +-+----------v-+
              |     Pipe     |
              +--------------+
```



#Binder





`Toast.makeText().show()`是将Toast加入显示队列