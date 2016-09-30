[TOC]
#数据结构在Java中的实现

Collection
├List
│├LinkedList
│├ArrayList
│└Vector
│　└Stack
└Set

Map
├Hashtable
│ 	└Properties
├HashMap
│	└LinkedHashMap
├SortedMap
│ 	└TreeMap
└WeakHashMap

代码基于JDK8，相比之前的版本有较大改动
##ArrayList
内部实际上是一个Object[]对象，实际上ArrayList就是封装了对数组的操作，ArrayList具有数组的所有特性。

###add
主要方法`public boolean add(E e)`，将数据加入数组的尾端。
在其中`ensureCapacityInternal()`会判断内部数组是否有足够的容量容纳新加入的元素。如果是第一次调用add方法，会根据add添加元素的数量和默认值(=10)来确定内部数组的大小。
如果当前数组大小不足，会通过`ensureExplicitCapacity()`调用`grow()`
创建新数组，策略是新的大小变为原先的3/2(`int newCapacity = oldCapacity + (oldCapacity >> 1);`)，如果新的大小还是小于添加元素后数组的数量，则让新的数组大小等于新添加元素后目标数组的大小。如果新数组大小大于ArrayList所要求的最大大小(`Integer.MAX_VALUE - 8`)，则会去判断添加元素的个数是否已经溢出(minCapacity &lt; 0)，若溢出，抛出异常。确定了新的数组大小之后，创建数组，并将所有元素移动到新的数组。

###get
判断边界，如果正常返回数组内指定元素，否则抛出异常。

###remove
同样先判断边界，取出目标值作为返回值，并将所有index后的元素前移，最后把内部数组的末位设置为null。

##LinkedList
链表结构，并且实现了Queue接口，有直接操作出入队的API。内部有两个成员变量Node，分别为first和last，指向链表的头尾，其中Node中的成员为一个名为next和一个名为prev的Node元素和一个item，可以看出ArrayList维护的是一个双向链表。

###add
调用`linkLast(E e)`将元素内容创建一个新的节点并插入到链表的尾部
###get
先判断边界，然后根据index的大小选择是从前遍历查找还是从后遍历查找。

###remove
根据同上操作获得需要被删除的节点，传入`unlink()`中，改变next prev的指向。

##Vector
扩容时请求双倍大小，线程安全的类。

##HashMap
内部有成员变量，table，类型为`Node &lt;K,V&gt;[]`。Node的成员变量为hash，key，value，next。可以说HashMap是一个链表的数组的包装类。

###put
计算has值后调用`putVal()`，如果table为null或者大小为0，调用`resize()`得到一个不为空的table，在其中要根据threshold来判断是否扩容，threshold＝容量*加载因子。如果旧容量的二倍小于最大容量并且旧容量大于默认容量，就将新的threshold设置为旧阈值的二倍。如果旧容量==0并且旧阈值==0，令新容量 = 默认容量，阈值 = 默认阈值。设置完毕之后创建新的table，并将旧table的内容移入其中后返回该table。
第二步是插入元素，根据hash值计算位置，分以下几种情况
- 当前位置为空，则直接把值包装成节点并且插入。
- 当前位置不为空，但是此位置上元素的key和新插入元素的key相同，则替换旧的元素。
- 当前位置不为空，并且该位置元素类型为TreeNode(Node的子类，红黑树的节点)，执行`TreeNode.putTreeVal()`，将新节点插入已存在的树中。
- 最后一种情况，类似之前的版本，遍历该节点下链接的链表，如果有相同key的，替换值，没有的话新建节点插入链表的尾端。这里多了一个相对于之前版本的判断，如果该节点下的链表数目超过了'树化'的阈值，就调用`treeifyBin()`转换成树。

###为什么HashMap初始大小要是2的指数次幂
**计算数据存放位置的方式是hashcode和数组长度-1做与操作，而如果数组长度是2的次幂，减一的话就是全1，完全是根据hash值的后几位来决定位置，而如果数组长度不是2次幂，那么减一后就会有几位为0，在这几位上的hashcode不论是0还是1运算后都会分配到同一位置，提高了冲突率。**

##HashTable
除了以下几点差异，几乎和HashMap相同
- HashMap可以接受为null的key 和 value
- HashTable是synchronized的，而HashMap不是
- HashTable直接使用对象的hashCode。而HashMap重新计算hash值。 
- HashTable中的hash数组初始大小是11，增加的方式是 old*2+1。HashMap中hash数组的默认大小是16，而且一定是2的指数。


##ConcurrentHashMap
1.6使用的是volatile，1.8使用了CAS操作在某些场合下替换Synchronized进行互斥同步。

##WeakHashMap
使用了WeakReference对持有弱引用
###ReferenceQueue
当一个obj被gc掉之后，其相应的包装reference，即ref对象会被放入queue中。我们可以从queue中获取到相应的对象信息，同时进行额外的处理。比如反向操作，数据清理等。
具体可以是扩展Reference之后，在实例中持有相应集合的key或Index，当Reference被GC后，就可以获得Reference并且通过key或index删除集合的数据。

##TreeMap
实现了SortMap接口，能够把它保存的记录根据键排序，默认是按键值的升序排序， 也可以指定Comparator，当用Iterator遍历TreeMap时，得到的记录是排过序的。

#Set
##HashSet
HashSet 内部用一个HashMap对象存储数据，更具体些，只用到了key，value全部为一dummy对象

#并发
##Java中的锁
###Synchronized
一个关键字，当用它来修饰一个方法或者一个代码块的时候，能够保证在同一时刻最多只有一个线程在执行该段代码。
####几种锁的作用域
- 方法锁 通过在方法声明中加入关键字，每个类实例对应一把锁，每个Synchronized方法都必须获得调用该方法的类实例的锁才能执行，否则方法阻塞。这种机制确保了同一时刻对于每个类实例，其所有声明为synchronized的成员函数之至多只有一个处于执行状态，从而有效避免了类成员变量之间的访问冲突。
- 对象锁 当一个对象中有synchronized method和synchronized block的时候调用此对象的同步方法进入其同步区域时，就必须获得对象锁。(方法锁也是对象锁)。Java的所有对象都包含一个互斥锁，这个锁由JVM自动获取和释放，线程进入synchronized方法时会自动获取该对象的锁，当然如果已有线程获取了这个对象锁，那么该线程会等待，方法抛出异常时，JVM也会自动释放锁(lock不会)。
- 类锁 对象锁是用来控制实例方法之间的同步，类锁是用来控制静态方法(或静态变量互斥体)之间的同步。

####实现方式
Synchronized编译之后，就会在同步块的前后分别形成monitorenter和monitorexit这两个字节码指令。根据虚拟机规范的要求，在执行monitorenter指令时，首先要尝试获取对象的锁，如果获得了锁，把锁的计数器加1，与此对应的，如果执行monitorexit指令，把锁的计数器减一，当计数器的值为零，锁便被释放。由于 synchronized 同步块对同一个线程是可重入的，因此一个线程可以多次获得同一个对象的互斥锁，同样，要释放相应次数的该互斥锁，才能最终释放掉该锁。

###Lock(ReentrantLock)
api层面的互斥锁，有一些额外的功能
- 等待可中断 正在等待锁的线程可以选择放弃等待，改为处理其他事情
- 公平锁 锁哥线程在等待同一个锁时，必须按照申请锁的时间顺序来依次获得锁
- 锁可绑定多个条件 调用newCondition()方法就可以多一个条件关联

####Lock.lock()和Lock.lockInterruptibly()
前一种被通知中断后，会获取锁之后才响应中断。而后一种被中断后，立即返回，不再参与锁的竞争并且取消锁的获取操作。

##线程池
优势
- 重用线程池中的线程，避免因为线程的创建和销毁所带来的性能开销
- 能有效控制线程池的最大并发数量，避免大量的线程之间因互相抢占系统资源而导致的阻塞现象
- 能过对线程进行简单的管理，并提供定时执行以及制定建构循环执行等功能

###ThreadPoolExecutor
```
public ThreadPoolExecutor(int corePoolSize,
						int maximumPoolSize,
						long keepAliveTime,
						TimeUnit unit,
						BlockingQueue<Runnable> workQueue,
						ThreadFactory threadFactory)
```
- corePoolSize 线程池的核显线程数，默认情况下，核心线程会在线程中一直存活，及时他们处于闲置状态。如果将ThreadPoolExecutor的allowCoreThreadTimeOut属性设置为true,那么闲置的核心线程在等待新任务到来时会有超时策略，这个时间间隔由keepAliveTime所指定，当等待时间超过后，核心线程就会被中止
- maximumPoolSize 线程池所能容纳最大线程数，当活动线程到达这个数值后，后续的新任务将会被阻塞。
- keepAliveTime 非核心线程的超时时长，超过这个时长，非核心线程就会被回收。
- unit 时间单位
- workQueue 线程池中的任务队列，通过线程池的execute方法提交的Runnable对象会存储在这个参数中
- threadFactory 线程工厂，为线程池提供创建新线程的功能。

执行规则：
1. 如果线程池中的线程数量未达到核心线程的数量，那么会直接启动一个核心线程来执行任务
2. 如果线程池中的线程数量已经达到或者超过核心线程的数量，那么任务会被插入到任务队列中等待
3. 如果任务队列已满，并且线程数量未达到线程池规定的最大值，那么会立即启动一个非核心线程来执行任务
4. 如果3中的线程数量已经达到最大值，那么就拒绝执行此任务。会调用`RejectedExecutionHandler`的`rejectedExecution`方法来通知调用者

####线程池分类
1. FixedThreadPool
固定数量线程池。当线程处于空闲状态时，并不会被回收，除非线程池被关闭了。当所有线程处于活动状态时，新任务都会处于等待状态，直到有线程空闲出来。

2. CachedThreadPool
线程数量不定的线程池，只有非核心线程，并且最大数量为Integer.MAX_VALUE。空闲线程超时时间为60s，超过就会被回收。比较适合执行大量的耗时较少的任务。

3. ScheduledThreadPool
核心线程数量是固定的，非核心线程数量没有限制，适合执行定时任务和固定周期的重复任务。

4. SingleThreadExecutor
只有一个核心线程，确保所有任务都在一个线程中按顺序执行。意义在与统一所有的外界任务到一个线程之中而不需要处理线程同步的问题。

###生产者消费者模型
- 判断线程状态时要使用while而不是if，因为即使从wait中被唤醒，当前的状态也不一定是可以操作的。如两个删除线程和一个添加线程，并且用if来判断条件，两个删除线程在为空时阻塞，添加线程唤醒了他们，这时就有可能某个线程在执行删除操作的时候，集合已经为空。若是使用while，被唤醒后发现集合不为空，则再次进入等待状态。

##一些线程相关类
- ArrayBlockQueue 有界阻塞队列
- LinkedBlockQueue 若不带规定大小的参数，大小为Integer.MAX_VALUE
- Semaphore 信号量，通过acquire获取，通过release释放
- CountDownLatch 计数为0时触发之后的操作
- CyclicBarrier 类似CountDownLatch，可重用

##CAS
CAS就是Compare and Swap的意思，比较并操作。很多的cpu直接支持CAS指令。CAS是项乐观锁技术，当多个线程尝试使用CAS同时更新同一个变量时，只有其中一个线程能更新变量的值，而其它线程都失败，失败的线程并不会被挂起，而是被告知这次竞争中失败，并可以再次尝试。
###CAS应用
CAS有3个操作数，内存值V，旧的预期值A，要修改的新值B。当且仅当预期值A和内存值V相同时，将内存值V修改为B，否则什么都不做。
###CAS缺点
- ABA问题：CAS需要检测旧值有没有变化，如果变化则改变。但是如果一个值先从A变成B又从B变回A，那么CAS则不能检测到这种变化。类AtomicStampedReference可以解决ABA问题，这个类会同时检测目标值和目标标记，都未发生改变时才会进行更新
- 自旋开销：如果长时间不成功，则会造成非常大的CPU开销
- 仅能保证一个变量的原子操作：AtomicReference可以进行对象的原子操作，可以把多个变量放到对象中进行原子操作。

##Thread
###Interrupt


#注解
@interface用来声明一个注解，其中的每一个方法实际上是声明了一个配置参数。方法的名称就是参数的名称，返回值类型就是参数的类型。可通过default来声明参数的默认值。

##元注解
###@Target
表示该注解用于什么地方
- ElemenetType.CONSTRUCTOR 构造器声明 
- ElemenetType.FIELD 域声明（包括 enum 实例） 
- ElemenetType.LOCAL_VARIABLE 局部变量声明 
- ElemenetType.METHOD 方法声明 
- ElemenetType.PACKAGE 包声明 
- ElemenetType.PARAMETER 参数声明 
- ElemenetType.TYPE 类，接口（包括注解类型）或enum声明

###@Retention
表示在什么级别保存该注解信息
- RetentionPolicy.SOURCE 源码中保存，会被编译器丢弃
- RetentionPolicy.CLASS class文件中可用，会被虚拟机丢弃
- RetentionPolicy.RUNTIME 在运行期间也会保留注释，可以通过反射机制读取注解信息

###@Documented
注解将包含在Javadoc中

###@Inherited
允许子类继承父类中的注解


#泛型
##类型擦除
在生成的Java字节代码中是不包含泛型中的类型信息的。使用泛型的时候加上的类型参数，会被编译器在编译的时候去掉。这个过程就称为类型擦除。如在代码中定义的List<Object>和List<String>等类型，在编译之后都会变成List。JVM看到的只是List，而由泛型附加的类型信息对JVM来说是不可见的。Java编译器会在编译时尽可能的发现可能出错的地方，但是仍然无法避免在运行时刻出现类型转换异常的情况。

类型擦除的基本过程也比较简单，首先是找到用来替换类型参数的具体类。这个具体类一般是Object。如果指定了类型参数的上界的话，则使用这个上界。把代码中的类型参数都替换成具体的类。同时去掉出现的类型声明，即去掉<>的内容。比如T get()方法声明就变成了Object get()；List<String>就变成了List。接下来就可能需要生成一些桥接方法（bridge method）。这是由于擦除了类型之后的类可能缺少某些必须的方法。比如考虑下面的代码：

```
class MyString implements Comparable<String> {
    public int compareTo(String str) {        
        return 0;    
    }
} 
```
当类型信息被擦除之后，上述类的声明变成了class MyString implements Comparable。但是这样的话，类MyString就会有编译错误，因为没有实现接口Comparable声明的int compareTo(Object)方法。这个时候就由编译器来动态生成这个方法。


#引用
##四种引用
- 强引用
- 软引用
- 弱引用
- 虚引用

#语法
##Finally
finally可以分两方面理解
1. 执行时机问题。finally总会执行（除非是System.exit()），正常情况下在try后执行，抛异常时在catche后面执行
2. 返回值问题。可以认为try（或者catch）中的return语句的返回值放入线程栈的顶部：如果返回值是基本类型则顶部存放的就是值，如果返回值是引用类型，则顶部存放的是引用。finally中的return语句可以修改引用所对应的对象，无法修改基本类型。但不管是基本类型还是引用类型，都可以被finally返回的“具体值”具体值覆盖

#设计模式
##策略模式
一些方法在不同场景中有不同的实现，可以把那些方法空实现或者抽象，在子类根据不同的情况实现。或者是通过组合，每次传入实现策略方法的对象，调用这个对象的实现。

##责任链模式
类似Android触摸传递机制

#一些优化
##transient
1. 一旦变量被transient修饰，变量将不再是对象持久化的一部分，该变量内容在序列化后无法获得访问。
2. transient关键字只能修饰变量，而不能修饰方法和类。注意，本地变量是不能被transient关键字修饰的。变量如果是用户自定义类变量，则该类需要实现Serializable接口。
3. 被transient关键字修饰的变量不再能被序列化，一个静态变量不管是否被transient修饰，均不能被序列化。

##分割字符串
JDK8一下的`String.split()`实现使用的是正则，效率比较低
使用indexOf + subString()可以提高效率

##红黑树性质
1. 每个节点的颜色不是黑色就是红色
2. 根节点是黑色的
3. 每个叶子节点都带有两个黑色节点(null)。如果一个节点只有一个子节点，那么另一个节点是黑色节点(null)
4. 如果一个节点是红色节点，那么他的两个子节点都是黑的
5. 任意一个节点到其子节点的所有路径上都黑色节点的数目相同



##Object类内的方法
equals()/hashCode()/wait()/notify()/notifyAll()/clone()/toString()/getClass()/finalize()

##解析XML
- SAX 基于事件驱动，有解析器和事件处理器两部分。
- DOM 把整个文件读入内存之后索引查找需要的值
- PULL 类似SAX，也是基于事件驱动的。

#I/O
##NIO
```
ServerSocketChannel sc = ServerSocketChannel.open();  
sc.configureBlocking(false);  
ServerSocket ss = serverSocketChannel.socket();  
ss.bind(new InetSocketAddress(port));  
selector = Selector.open();  
serverSocketChannel.register(selector, SelectionKey.OP_ACCEPT);  

while (true) {  
    selector.select();  
    Set<SelectionKey> selectionKeys = selector.selectedKeys();  
    Iterator<SelectionKey> iterator = selectionKeys.iterator();  
    while (iterator.hasNext()) {          
        SelectionKey selectionKey = iterator.next();  
        iterator.remove();  
        handleKey(selectionKey);  
    }  
}  
```

#常用设计模式
- 单例模式
- 建造者模式
- 简单工厂模式
- 抽象工厂模式
- 责任链模式
- 观察者模式
- 装饰模式
- 外观模式
- 策略模式
- 原型模式 (clone)
- 组合模式
- 代理模式
- 适配器模式

#Java1.8
##Lambda
没有生成单独的类文件，而是生成了匿名内部类，在使用的时候才去生成

#JNI
##注册
- 静态注册：第一次查找并且通过函数名关联，保存JNI层函数的函数指针。
- 动态注册：JNINativeMethod

##GC
若有Native层的变量持有Java层变量的引用，则有可能产生Java层变量被GC而造成Native层持有野指针的问题，为了解决这个问题，JNI提供了三种引用：
- Local Reference：本地引用。在JNI层函数中使用的非全局引用对象都是Local Reference。它包括函数调用时传入的jobject、在JNI层函数中创建的jobject。LocalReference最大的特点就是，一旦JNI层函数返回，这些jobject就可能被垃圾回收。
- Global Reference：全局引用，这种对象如不主动释放，就永远不会被垃圾回收。
- Weak Global Reference：弱全局引用，一种特殊的GlobalReference，在运行过程中可能会被垃圾回收。所以在程序中使用它之前，需要调用JNIEnv的IsSameObject判断它是不是被回收了。