###JVM的系统结构
- JVM主要包括两个子系统和两个组件。 
	- 两个子系统分别是Class loader子系统和Execution engine(执行引擎) 子系统；  
		* Class loader子系统的作用：根据给定的全限定名类名(如 java.lang.Object)来装载class文件的内容到 Runtime data area中的method area(方法区域)。Java程序员可以extends java.lang.ClassLoader类来写自己的Class loader。
		* Execution engine子系统的作用：执行classes中的指令。任何JVM specification实现(JDK)的核心都是Execution engine，不同的JDK例如Sun 的JDK 和IBM的JDK好坏主要就取决于他们各自实现的Execution engine的好坏     
	- 两个组件分别是Runtime data area (运行时数据区域)组件和Native interface(本地接口)组件。  
		* Native interface组件：与native libraries交互，是其它编程语言交互的接口。当调用native方法的时候，就进入了一个全新的并且不再受虚拟机限制的世界，所以也很容易出现JVM无法控制的native heap OutOfMemory。
		* Runtime Data Area组件：这就是我们常说的JVM的内存了。   
		它主要分为五个部分
		   	*  Heap (堆)：一个Java虚拟实例中只存在一个堆空间   
			* Method Area(方法区域)：被装载的class的信息存储在Method area的内存中。  当虚拟机装载某个类型时，它使用类装载器定位相应的class文件，然后读入这个    class文件内容并把它传输到虚拟机中。  
			* Java Stack(java的栈)：虚拟机只会直接对Java stack执行两种操作：以帧为单位的压栈或出栈   
			* Program Counter(程序计数器)：每一个线程都有它自己的PC寄存器，也是该线程启动时创建的。PC寄存器的内容总是指向下一条将被执行指令的地址，这里的地址可以是一个本地指针，也可以是在方法区中相对应于该方法起始指令的偏移量。    
			* Native method stack(本地方法栈)：保存native方法进入区域的地址
- 注意：以上五部分只有Heap 和Method Area是被所有线程的共享使用的；而Java stack, Program counter 和Native method stack是以线程为粒度的，每个线程独自拥有自己的部分. 
![JAVA虚拟机的体系结构](https://github.com/GitOrgLan/interview/blob/master/img/java/JAVA%E8%99%9A%E6%8B%9F%E6%9C%BA%E7%9A%84%E4%BD%93%E7%B3%BB%E7%BB%93%E6%9E%84.png)

###JVM系统结构
[JVM Runtime Data Areas](http://www.tuicool.com/articles/AjeAzq)  

<div align="center">    
<img src="https://github.com/GitOrgLan/interview/blob/master/img/java/Runtime%20Data.png" width = "550" height = "370" alt="Runtime Data"/>
<img src="https://github.com/GitOrgLan/interview/blob/master/img/java/Runtime%20Data2.jpg" width = "550" height = "370" alt="Runtime Data"/>  
</div> 

- 堆(Heap):  
	jvm中内存占用最大的一块,是所有线程共享的一块内存区域.在jvm启动时创建,存放的是所有对象实例（或数组），所有的对象实例都在这里进行动态分配，当类空间无法再扩张会抛出OutOfMemoryError异常。Java堆是垃圾收集器管理的主要区域，而收集器采用分代收集算法。

- 方法区(Method Area)：  
	与堆类似,也是各个线程共享的内存区域，主要用来存储加载的类信息，常量，静态变量，即时编译器编译后的代码等数据，当方法区无法满足内存分配时,也抛出OutOfMemoryError异常。运行时常量池是方法区的一部分，用于存放编译期生成的各种字面量和符号引用相对于Class文件常量池的重要特征是具备动态性（常量并非强制编译期产生，运行期间也可以新增，例如String类的intern()方法）。

- 虚拟机栈：  
	和程序计数器一样，都属于线程私有,生命周期与线程相同，描述的是java方法执行的内存模型,每个方法执行都会创建一个栈帧，用于存储局部变量表，操作栈，动态链接，方法出口等信息，每一个方法被调用直至执行完成的过程，就对应一个栈帧在jvm stack 从入栈到出栈的过程.局部变量表存放了编译期可知的各种数据基本类型(Boolean,byte,char,short,int,float,long,double),以及对象的引用。这个区域中定义了2种异常情况,如果线程请求的栈深度大于jvm所允许的深度,将抛出StackOverflowError异常,如果jvm可以动态扩张，当扩张无法申请到足够的内存空间是会抛出OutOfMemoryError异常。（这些数据区域异常将在下面的例子都讲到）。
- 本地方法栈：  
	与虚拟机栈比较相似。其区别：虚拟机栈为虚拟机执行Java方法服务，而本地方法栈则为虚拟机使用Native方法服务。

- 程序计数器(Program Counter Register):  
	属于线程私有的，占用的内存空间较少，可以看成是当前线程所执行字节码的行号指示器，字节码解释器工作时就是通过改变这个计数器的值来选择下一条，需要执行的字节码指令，分支，循环，跳转，异常处理，线程恢复等基础功能需要依赖这个计数器来完成，这个区域是jvm规范中没有规定任何OutOfMemoryError情况区域。

###一些简单概念
- 堆(Heap)和非堆(Non-heap)内存  
	按照官方的说法：“Java 虚拟机具有一个堆，堆是运行时数据区域，所有类实例和数组的内存均从此处分配。堆是在 Java 虚拟机启动时创建的。”“在JVM中堆之外的内存称为非堆内存(Non-heap memory)”。可以看出JVM主要管理两种类型的内存：堆和非堆。简单来说堆就是Java代码可及的内存，是留给开发人员使用的；非堆就是JVM留给 自己用的，所以方法区、JVM内部处理或优化所需的内存(如JIT编译后的代码缓存)、每个类结构(如运行时常数池、字段和方法数据)以及方法和构造方法 的代码都在非堆内存中。		
- 堆内存分配  
	JVM初始分配的内存由-Xms指定，默认是物理内存的1/64；JVM最大分配的内存由 -Xmx指定，默认是物理内存的1/4。默认空余堆内存小于40%时，JVM就会增大堆直到-Xmx的最大限制；空余堆内存大于70%时，JVM会减少堆 直到-Xms的最小限制。因此服务器一般设置-Xms、-Xmx相等以避免在每次GC 后调整堆的大小。
- 非堆内存分配  
	JVM使用-XX:PermSize设置非堆内存初始值，默认是物理内存的1/64；由XX:MaxPermSize设置最大非堆内存的大小，默认是物理内存的1/4。
- JVM内存限制(最大值)  
	首先JVM内存限制于实际的最大物理内存(废话！呵呵)，假设物理内存无限 大的话，JVM内存的最大值跟操作系统有很大的关系。简单的说就32位处理器虽然可控内存空间有4GB,但是具体的操作系统会给一个限制，这个限制一般是 2GB-3GB（一般来说Windows系统下为1.5G-2G，Linux系统下为2G-3G），而64bit以上的处理器就不会有限制了 

###推荐的两款内存检测工具
- jconsole  
	 JDK自带的内存监测工具，路径jdk bin目录下jconsole.exe，双击可运行。连接方式有两种，第一种是本地方式如调试时运行的进程可以直接连，第二种是远程方式，可以连接以服务形式启动的进程。远程连接方式是：在目标进程的jvm启动参数中添加-Dcom.sun.management.jmxremote.port=1090 -Dcom.sun.management.jmxremote.ssl=false -Dcom.sun.management.jmxremote.authenticate=false 1090是监听的端口号具体使用时要进行修改，然后使用IP加端口号连接即可。通过该工具可以监测到当时内存的大小，CPU的使用量以及类的加载，还提供了手动gc的功能。优点是效率高，速度快，在不影响进行运行的情况下监测产品的运行。缺点是无法看到类或者对象之类的具体信息。使用方式很简单点击几下就可以知道功能如何了，确实有不明白之处可以上网查询文档。

- JProfiler  
	 收费的工具，但是到处都有破解办法。安装好以后按照配置调试的方式配置好一个本地的session即可运行。可以监测当时的内存、CPU、线程等，能具体的列出内存的占用情况，还可以就某个类进行分析。优点很多，缺点太影响速度，而且有的类可能无法被织入方法，例如我使用jprofiler时一直没有备份成功过，总会有一些类的错误 

###为什么程序计数器要线程私有
>由于Java虚拟机的多线程是通过线程轮流切换并分配处理器执行时间的方式来实现的，在任何一个确定的时刻，一个处理器（对于多核处理器来说是一个内核）只会执行一条线程中的指令。
因此，为了线程切换后能恢复到正确的执行位置，每条线程都需要有一个独立的程序计数器，各条线程之间的计数器互不影响，独立存储，我们称这类内存区域为“线程私有”的内存。 

###[对象访问方式](http://blog.csdn.net/java2000_wl/article/details/8015105)
>由于reference类型在Java虚拟机规范里面只规定了一个指向对象的引用，并没有定义这个引用应该通过哪种方式去定位，以及访问到Java堆中的对
象的具体位置，因此不同虚拟机实现的对象访问方式会有所不同，主流的访问方式有两种：使用句柄和直接指针。 

- 如果使用句柄访问方式，Java堆中将会划分出一块内存来作为句柄池，reference中存储的就是对象的句柄地址，而句柄中包含了对象实例数据和类型数据各自的具体地址信息，
这两种对象的访问方式各有优势，使用句柄访问方式的最大好处就是reference中存储的是稳定的句柄地址，在对象被移动（垃圾收集时移动对象是非常普遍的行为）时只会改变句柄中的实例数据指针，而reference本身不需要被修改。
![句柄访问方式](https://github.com/GitOrgLan/interview/blob/master/img/java/%E5%8F%A5%E6%9F%84%E8%AE%BF%E9%97%AE%E6%96%B9%E5%BC%8F.jpg)
- 使用直接指针访问方式的最大好处就是速度更快，它节省了一次指针定位的时间开销，由于对象的访问在Java中非常频繁，因此这类开销积少成多后也是一项非常可观的执行成本。   
![指针访问方式](https://github.com/GitOrgLan/interview/blob/master/img/java/%E6%8C%87%E9%92%88%E8%AE%BF%E9%97%AE%E6%96%B9%E5%BC%8F.jpg)

###堆和栈的优缺点
>堆的优势是可以动态分配内存大小，生存期也不必事先告诉编译器，因为它是在运行时动态分配内存的。
缺点就是要在运行时动态分配内存，存取速度较慢；   
栈的优势是，存取速度比堆要快，仅次于直接位于CPU中的寄存器。
但缺点是，存在栈中的数据大小与生存期必须是确定的，缺乏灵活性。

###对象
>在Java中，创建一个对象包括对象的声明和实例化两步，下面用一个例题来说明对象的内存模型  
	假设有类Rectangle定义如下：
```
public class Rectangle {
	double width;
	double height;
	public Rectangle(double w,double h){
		w = width;
		h = height;
	}
}
``` 

- 声明对象时的内存模型
用Rectanglerect；声明一个对象rect时，将在栈内存为对象的引用变量rect分配内存空间，但Rectangle的值为空，称rect是一个空对象。空对象不能使用，因为它还没有引用任何"实体"。
- 对象实例化时的内存模型
当执行rect=newRectangle(3,5)；时，会做两件事：　在堆内存中为类的成员变量width,height分配内存，并将其初始化为各数据类型的默认值；接着进行显式初始化（类定义时的初始化值）；最后调用构造方法，为成员变量赋值。  返回堆内存中对象的引用（相当于首地址）给引用变量rect,以后就可以通过rect来引用堆内存中的对象了。
一个类通过使用new运算符可以创建多个不同的对象实例，这些对象实例将在堆中被分配不同的内存空间，改变其中一个对象的状态不会影响其他对象的状态。
```
Rectangle r1= new Rectangle(3,5);
Rectangle r2=r1;
```
则在堆内存中只创建了一个对象实例，在栈内存中创建了两个对象引用，两个对象引用同时指向一个对象实例。

###静态变量
>用static的修饰的变量和方法，实际上是指定了这些变量和方法在内存中的"固定位置"－static storage，可以理解为所有实例对象共有的内存空间。
static变量有点类似于C中的全局变量的概念；静态表示的是内存的共享，就是它的每一个实例都指向同一个内存地址。
把static拿来，就是告诉JVM它是静态的，它的引用（含间接引用）都是指向同一个位置，在那个地方，你把它改了，它就不会变成原样，你把它清理了，它就不会回来了。         那静态变量与方法是在什么时候初始化的呢？对于两种不同的类属性，static属性与instance属性，初始化的时机是不同的。
instance属性在创建实例的时候初始化，static属性在类加载，也就是第一次用到这个类的时候初始化，对于后来的实例的创建，不再次进行初始化。我们常可看到类似以下的例子来说明这个问题：
```
class Student{
	static int numberOfStudents=0;
	Student()
	{
		numberOfStudents++;
	}
}
```
每一次创建一个新的Student实例时,成员numberOfStudents都会不断的递增,并且所有的Student实例都访问同一个numberOfStudents变量,实际上int numberOfStudents变量在内存中只存储在一个位置上。

###理解final问题有很重要的含义
>许多程序漏洞都基于此----final只能保证引用永远指向固定对象，不能保证那个对象的状态不变。
在多线程的操作中,一个对象会被多个线程共享或修改，一个线程对对象无意识的修改可能会导致另一个使用此对象的线程崩溃。
一个错误的解决方法就是在此对象新建的时候把它声明为final，意图使得它"永远不变"。其实那是徒劳的。 

###心得分享
1. jvm的内存分区分级大粒度管理相较memcache的固定单元小粒度内存管理，拥有更高的内存利用率，但带来内存碎片的问题。 
- 为了解决内存碎片问题，jvm采取了碎片整理的方式，但碎片整理是很耗时的。
- 为了提高碎片整理的效率，因此引入了周期性的GC，而且分区分级的方式也控制了每次GC和碎片整理的范围。 
- 由于jvm使用堆内存来存储局部变量，而局部变量具有生存周期短，先申请的后释放的特点，因此在低级别的分区中进行GC是效率最高的方式。 感觉环环相扣，有点奇妙。
	再补充GC的一个作用：寻找回路的孤立存储，并释放其占用的空间。这更要求GC的非实时、周期性





























