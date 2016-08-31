[TOC]
#Kernel Layer
##系统启动
- 开启电源后，引导程序Bootloader会加载系统内核，各种驱动。有了驱动以后启动Android系统并加载第一个*用户级别*的init进程
- 加载init.rc配置文件，会启动一个Zygote进程，其他Android进程都是由该进程fork()启动
- Zygote进程主要执行：加载ZygoteInit类，注册Zygote Socket服务端。加载Dalvik虚拟机，预加载SDK核心类和系统资源
- fork出SystemServer进程，SystemServer负责启动和管理整个Framework
- 在SystemServer的main方法中，初始化了其他系统服务，并通过IPC加入ServiceManger，初始完成后调用系统服务systemReady()
- 启动桌面程序Launcher

![Boot](http://upload-images.jianshu.io/upload_images/277730-b5028a5459d60625.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#Application Layer
##Activity
###启动流程
1. Activity：startActivity方法的真正实现在Activity中
2. Instrumentation：用来辅助Activity完成启动Activity的过程
3. ActivityThread（包含ApplicationThread + ApplicationThreadNative + IApplicationThread）：真正启动Activity的实现都在这里

Activity#startActivity -> Activity#startActivityForResult
-> Instrumentation#execStartActivity
-> ActivityManagerNative.getDefault#startActivity(这里通过Binder调用了ActivityManagerService中的方法)
-> ActivityMangerService#startActivity -> ActivityManagerService#startActivityAsUser
-> ActivityStackSupervisor#startActivityMayWait -> startActivityLocked -> startActivityUncheckedLocked
-> ActivityStack#resumeTopActivityLocked -> resumeTopActivityInnerLocked
-> ActivityStackSupervisor#startSpecificActivityLocked -> (已有进程)realStartActivityLocked
-> ApplicationThread#scheduleLaunchActivity -> sendMessage(发送消息给H)
-> H#handleMessage -> handleLaunchActivity -> performLaunchActivity -> 回调OnCreate方法

在上述startSpecificActivityLocked中，若不存在进程，则调用`ActivityServiceManager.startProcessLocked()`，在其中调用`Process.start()`通过Zygote fork出一个新的进程，并执行`ActivityThread.main()`;接下来通过IPC调用AMS的`attachApplication()`，调用`realStartActivityLocked()`方法，接下来就一样了。

performLaunchActivity主要完成
1. 从ActivityClientRecord中获取待启动的Activity的组件信息
2. 通过Instrumentation的newActivity方法使用类加载器创建Activity
3. 尝试创建Application对象
4. 创建ContextImpl对象并通过Activity的attach方法来完成重要的初始化
5. 回调Activity的OnCreate

###启动模式
- standard 标准模式，每次启动一个Activiy都会重建一个新的实例，不管这个实例是否已经存在。
- singleTop 栈顶复用模式，如果启动的Activity有实例位于栈顶，那么此Activity不会重建，同时栈顶实例的`onNewIntent`方法会被回调。
- singleTask 栈内复用模式，只要启动的Activity实例在栈内存在，就把该实例放到栈顶(弹出栈中该实例上的所有其他Activity实例)，并调用`onNewIntent`。可以指定任务栈(TaskAffinity)，如果任务栈不存在，会创建新的任务栈
- singleInstance 单例模式，此模式的实例只能单独位于一个任务栈中。由于栈内复用的特性，后续的请求均不会创建新的Activity，除非这个独特的任务栈被销毁了。

###生命周期
![Activity](http://p.blog.csdn.net/images/p_blog_csdn_net/hpoi/EntryImages/20091003/347621596238419942633901667161718750.jpg)

##Service
###Service保活
[出处](http://blog.csdn.net/marswin89/article/details/50890708)
1. 将Service设置为前台进程

2. 在service的onstart方法里返回 STATR_STICK

3. 添加Manifest文件属性值为android:persistent=“true”

4. 覆写Service的onDestroy方法

5. 添加广播监听android.intent.action.USER_PRESENT事件以及其他一些可以允许的事件

6. 服务互相绑定

7. 设置闹钟，定时唤醒

8. 账户同步，定时唤醒

9. native层保活

##View
###ViewRoot和DecorView
ViewRoot对应`ViewRootImpl`类，是连接WindowManager和DecorView的纽带，View的三大流程均是通过ViewRoot来完成的。在ActivityThread中，当Activity对象被创建出来后，会将DecorView添加到Window中，同时会创建ViewRootImpl对象，并将ViewRootImpl对象和DecorView建立关联。
绘制流程都是从`ViewRootImpl.performTraversals()`开始，依次调用`performMeasure()`,`performLayout()`,`performDraw()`。这些方法会调用DecorView的`measure()`方法，`measure()`又会调用`onMeasure()`完成子View的measure。其余的流程和Measure类似。
DecorView实际上是一个FrameLayout，View层的事件都要先经过DecorView然后才传递到View。

###MeasureSpec
MeasureSpec是一个32位int值，高2位代表SpecMode(测量模式)，低30位代表SpecSize(测量大小)。
SpecMode有三类
- UNSPECIFIED 父容器不对View做限制，要多大有多大
- EXACTLY 父容器已经测量出了精确大小，这时候View的最终大小就是SpecSize所指定的值，它对应于match_parent和精确值这两种
- AT\_MOST 父容器指定了一个SpecSize，View的大小不能大于这个值，具体是什么值要看不同View的具体实现。对应于wrap\_content

对DecorView来说，其MeasureSpec由窗口的尺寸和其自身的LayoutParams来共同决定；而普通View，其MeasureSpec由父容器的MeasureSpec和自身的LayoutParams来决定。

###View的工作流程
####measure
如果是View，测量自己就可以了。如果是一个ViewGroup，除了完成自己的测量外，还会遍历去调用所有子元素的measure方法，各个子元素再递归执行这个流程

####layout
layout的作用是ViewGroup用来确定子元素的位置，当ViewGroup的位置被确定之后，他在onLayout中会遍历所有的子元素并调用其layout方法，在layout方法中onLayout又会被调用。
layout方法确定View本身位置，onLayout方法确定所有子元素位置。

####draw过程
View的draw过程有如下几步
1. background.draw()
2. onDraw()
3. dispatchDraw()
4. onDrawScrollBars()
**有一个特殊的方法 ： setWillNotDraw，如果一个View不需要绘制任何内容，则可以将这个标记为设置为true。ViewGroup默认开启了这个标志位，所以ViewGroup的OnDraw方法不会进行绘制。**


##Handler
Handler机制相关的类有
- Handler
- Looper
- MessageQueue
- Message
而Looper是通过管道(pipe)实现的。

>关于管道，简单来说，管道就是一个文件。
在管道的两端，分别是两个打开文件文件描述符，这两个打开文件描述符都是对应同一个文件，其中一个是用来读的，别一个是用来写的。
一般的使用方式就是，一个线程通过读文件描述符中来读管道的内容，当管道没有内容时，这个线程就会进入等待状态，而另外一个线程通过写文件描述符来向管道中写入内容，写入内容的时候，如果另一端正有线程正在等待管道中的内容，那么这个线程就会被唤醒。这个等待和唤醒的操作是如何进行的呢，这就要借助Linux系统中的epoll机制了。 Linux系统中的epoll机制为处理大批量句柄而作了改进的poll，是Linux下多路复用IO接口select/poll的增强版本，它能显著减少程序在大量并发连接中只有少量活跃的情况下的系统CPU利用率。

###初始化完成的工作
- Java层，创建Looper对象，Looper的构造函数汇创建消息队列MessageQueue对象。MessageQueue对象用于储存消息队列，管理消息。
- Native层，MessageQueue创建时，会调用JNI函数，初始化NativeMessageQueue。NativeMessageQueue会初始化Looper对象，Looper对象的作用就是，当Java层的消息队列没有消息时，就使Android线程进入等待状态，而当Java层的消息队列中来了消息之后，就唤醒Android应用程序的主线程来处理这个消息。

###发送消息
消息发送最终会调用到`MessageQueue.enqueueMessage()`，根据message.when来确定插入的位置并把消息加入到队列中，分为以下两种情况
- 消息队列为空：此时app主线程应处于等待状态，需要调用wake唤醒。
- 消息队列不为空：不需要唤醒主线程。

###程序为什么不会被Looper.loop()卡死
进程：每个app运行时首先创建一个进程，该进程是由Zygote fork出来的，用于Activity/Service的运行。
线程：线程对应用来说非常常见，比如每次new Thread().start都会创建一个新的线程。该线程与App所在进程之间资源共享，从Linux角度来说进程与线程除了是否共享资源外，并没有本质的区别，都是一个task_struct结构体，在CPU看来进程或线程无非就是一段可执行的代码，CPU采用CFS调度算法，保证每个task都尽可能公平的享有CPU时间片。

###主要调用关系
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
#Framework Layer
##Binder
###为什么使用Binder
- 传输性能 socket是通用接口，传输效率低，开销大，主要用在跨网络的进程间通信和本机上进程间的低速通信。消息队列和管道采用存储-转发方式，即数据先从发送方缓存区拷贝到内核开辟的缓存区中，然后再从内核缓存区拷贝到接收方缓存区，至少有两次拷贝过程。共享内存虽然无需拷贝，但控制复杂，难以使用
- 安全性 传统的LinuxIPC方式的接收方无法获得对方进程可靠的UID/PID（用户ID/进程ID），从而无法鉴别对方身份。Android为每个安装好的应用程序分配了自己的UID，故进程的UID是鉴别进程身份的重要标志。而Binder基于Client-Server通信模式，传输过程只需一次拷贝，为发送发添加UID/PID身份，既支持实名Binder也支持匿名Binder，安全性高

###Binder通信模型
Binder在FrameWork的通信主要与以下几个模块有关
- Server (如各种系统调用，AMS,WMS,PMS)
- Client
- ServiceManager 提供Client查找Server的途径
- Binder driver Binder驱动程序提供设备文件/dev/binder与用户空间交互，Client、Server和Service Manager通过open和ioctl文件操作函数与Binder驱动程序进行通信。Client和Server之间的进程间通信通过Binder驱动程序间接实现
![模型](http://hi.csdn.net/attachment/201107/19/0_13110996490rZN.gif)
通信过程：
1. 首先一个进程向驱动通知他是ServiceManager，驱动同意后分配给这个进程0号引用，其他进程通过0号引用就可以和ServcieManager通信。
2. 各个Service启动后通过驱动向Service注册，ServiceManager便保存Service名和Service的地址。
3. Client要与Service通信，首先询问ServiceManager，通过Service名获得Service地址，这样就可以和Service进行通信。

###跨进程原理
IPC 中最基本的问题在于进程间使用的虚拟地址空间是相互独立的，不能直接访问，所以要相互访问，就要借助 kernel ，就是要让数据用用户空间进入到内核空间，然后再去到另一个进程的用户空间。
一般的Linux IPC方式通常的做法是：发送方将准备好的数据存放在缓存区中，调用API通过系统调用进入内核中。内核服务程序在内核空间分配内存，将数据从发送方缓存区复制到内核缓存区中。接收方读数据时也要提供一块缓存区，内核将数据从内核缓存区拷贝到接收方提供的缓存区中并唤醒接收线程，完成一次数据发送。
而Binder采用了新的策略，它把内核空间的地址和用户空间的虚拟地址映射到了同一段物理地址上，所以就只需要把数据从原始用户空间复制到内核空间，把目标进程用户空间和内核空间映射到同一段物理地址，这样第一次复制到内核空间，其实目标的用户空间上也有这段数据了。这就是 binder 比传统 IPC 高效的一个原因。

###使用以及上层原理
直观来说，Binder是Android中的一个类，他实现了IBinder接口。从IPC角度来说，Binder是Android中的一种跨进程通信方式，Binder还可以理解为一种虚拟的物理设备，它的设备驱动是/dev/nomder。从Android Framework的角度来说，Binder是ServiceManager连接各种Manager和响应ManagerService的桥梁；从Android应用层来说，Binder是客户端和服务端进行通信的媒介，当bindService的时候，服务端会返回一个包含了服务端业务调用的Binder对象，通过这个对象，客户端就可以获取服务端提供的服务或数据，这里的服务包括普通服务和基于AIDL的服务。
Android开发中，Binder主要用于Service，包括AIDL和Messenger，其中普通Service中的Binder不涉及进程间通信，所以较为简单，无法触及Binder的核心，而Messenger的底层其实是AIDL。

####IBinder/IInterface/Binder/BinderProxy/Stub
- IBinder是一个接口，它代表了一种跨进程传输的能力；只要实现了这个接口，就能将这个对象进行跨进程传递；这是驱动底层支持的；在跨进程数据流经驱动的时候，驱动会识别IBinder类型的数据，从而自动完成不同进程Binder本地对象以及Binder代理对象的转换。
- IInterface中有一个`asBinder`方法，实际上，一般的声明的AIDL接口都会继承IInterface，可以代表远程实体有什么方法
- Java层的Binder类，代表的其实就是Binder本地对象。BinderProxy类是Binder类的一个内部类，它代表远程进程的Binder对象的本地代理；这两个类都继承自IBinder, 因而都具有跨进程传输的能力；实际上，在跨越进程的时候，Binder驱动会自动完成这两个对象的转换。
- 在使用AIDL的时候，编译工具会给我们生成一个Stub的静态内部类；这个类继承了Binder, 说明它是一个Binder本地对象，它实现了IInterface接口，表明它具有远程Server承诺给Client的能力；Stub是一个抽象类，具体的IInterface的相关实现需要我们手动完成，这里使用了策略模式。

####AIDL
使用AIDL生成的类，大体结构如下
- 继承IInterface并拥有声明的aidl接口的方法
- stub内部类，实现上述接口，给每一个方法分配一个标志序列号，通过`onTransact()`传入的序列号调用子类实现的方法。一般本地实体都继承自这个类。
- Proxy类，远程代理类，如果请求的调用在同一进程，则直接返回实现stub的Binder，否则，返回代理类。代理类和Binder有组合的关系，实际上被Client调用时，将参数封装，通过Binder驱动传递到Binder实体的`onTransact()`，再由`onTransact()`进行调用。

####类似

```
ActivityMangerService   ActiviyManagerProxy   ActivityMangerNative
        |                         |                     |
      Binder                    Proxy                  Stub
```


#其他
`Toast.makeText().show()`是将Toast加入显示队列