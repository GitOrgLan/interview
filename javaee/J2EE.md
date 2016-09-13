###Cookie和Session的作用
	描述Cookie和Session的作用，区别和各自的应用范围，Session工作原理

###Session
>用于保存每个用户的专用信息. 每个客户端用户访问时，服务器都为每个用户分配一个唯一的会话ID（Session ID) . 她的生存期是用户持续请求时间再加上一段时间(一般是20分钟左右).Session中的信息保存在Web服务器内容中,保存的数据量可大可小.
当 Session超时或被关闭时将自动释放保存的数据信息.
由于用户停止使用应用程序后它仍然在内存中保持一段时间,因此使用Session对象使保存用户数据的方法效率很低.
对于小量的数据,使用Session对象保存还是一个不错的选择

###Cookie 
>用于保存客户浏览器请求服务器页面的请求信息,程序员也可以用它存放非敏感性的用户信息，信息保存的时间可以根据需要设置.
如果没有设置Cookie失效日期,它们仅保存到关闭浏览器程序为止.如果将Cookie对象的Expires属性设置为Minvalue,则表示Cookie永远不会过期.
Cookie存储的数据量很受限制,大多数浏览器支持最大容量为4K,因此不要用来保存数据集及其他大量数据.
由于并非所有的浏览器都支持Cookie,并且数据信息是以明文文本的形式保存在客户端的计算机中,因此最好不要保存敏感的,未加密的数据,否则会影响网站的安全性

###session工作原理
- 当有Session启动时，服务器生成一个唯一值，称为Session ID（好像是通过取进程ID的方式取得的）。
- 然后，服务器开辟一块内存，对应于该Session ID。
- 服务器再将该Session ID写入浏览器的cookie。
- 服务器内有一进程，监视所有Session的活动状况，如果有Session超时或是主动关闭，服务器就释放改内存块。
- 当浏览器连入IIS时并请求的ASP内用到Session时，IIS就读浏览器Cookie中的Session ID。
- 然后，服务检查该Session ID所对应的内存是否有效。
- 如果有效，就读出内存中的值。
- 如果无效，就建立新的Session。

###session怎么实现 
-  通过cookie  
	Cookie是保存在客户端的一小段信息，服务器在响应请求时可以将一些数据以“键-值”对的形式通过响应信息保存在客户端。当浏览器再次访问相同的应用时，会将原先的Cookie通过请求信息带到服务器端。
	1. 当有Session启动时，服务器生成一个唯一值，称为Session ID（好像是通过取进程ID的方式取得的）。
	- 然后，服务器开辟一块内存，对应于该Session ID。
	- 服务器再将该Session ID写入浏览器的cookie。
	- 服务器内有一进程，监视所有Session的活动状况，如果有Session超时或是主动关闭，服务器就释放改内存块。
	- 当浏览器连入IIS时并请求的ASP内用到Session时，IIS就读浏览器Cookie中的Session ID。
	- 然后，服务检查该Session ID所对应的内存是否有效。
	- 如果有效，就读出内存中的值。
	- 如果无效，就建立新的Session。
- url重写   
	通过cookie可以很好地实现session，但是如果客户端由于某些原因（比如出于安全考虑）而禁用cookie，在这种情况之下，为了使session能够继续生效，可以采用url重写。
	它会将SessionID的信息作为请求地址的一部分传到了服务器端

###正则表达式 
	Java 常用正则表达式 
	[链接1]（http://blog.csdn.net/ljchlx/article/details/8525124）
	[链接2]（http://blog.csdn.net/kiss_vicente/article/details/8050816）
	
	[史上最全最常用的正则表达式-（基本够用值得收藏）](http://www.2cto.com/kf/201405/300289.html)
	
	由一些普通字符和一些元字符（metacharacters）组成。普通字符包括大小写的字母和数字，而元字符则具有特殊的含义

	举例简单说明NFA与DFA工作的区别：
	由此可以看出来，两种引擎的工作方式完全不同，一个(NFA)以表达式为主导，一个(DFA)以文本为主导！
	一般而论，DFA引擎则搜索更快一些！但是NFA以表达式为主导，反而更容易操纵，因此一般程序员更偏爱NFA引擎！ 两种引擎各有所长，而真正的引用则取决与你的需要以及所使用的语言！

	举例：
	his is yaabc’s blog 
	/ya(msen|nsen|nsem)/




	
###struts2 必备包
commons-fileupload-1.2.1.jar
freemarker-2.3.13.jar
ognl-2.6.11.jar
struts2-core-2.1.6.jar
xwork-2.1.2.jar


###结合spring 源码谈谈其中的设计模式

day1:J2EE  
>J2EE 是Sun公司提出的多层(multi-diered),分布式(distributed),基于组件(component-base)的企业级应用模型 (enterpriese application model).


###关于J2EE应用服务器的容器?
	答：j2ee的应用服务器：weblogic, webspere, jboss, jrun, resin, enhydra, tomcat等等
	J2EE 应用服务器（Application Server）采用目前国际最先进的开发理念、拥有许多适合基于Web 的应用系统需求的特点：
		1.三层结构体系—最适合Internet环境，可以使系统有很强的可扩展性和可管理性。安全性等
		2. 分布式环境—可以保证系统的稳定性，同时拥有较高的性能。负载均衡等
		3. 面向对象的模块化组件设计—可以提高开发速度，降低开发成本。消息网关 交易日 产品 项目 交易子系统 资金子系统 账户子系统 会员子系统 登记托管系统等
		4. 采用JAVA技术—完全跨平台，适应Internet需要，并能得到大多数厂商支持，保护用户投资。

###J2EE是技术还是平台还是框架？
- J2EE是技术: 核心技术为 JSP, Servlet, EJB ..
- J2EE是平台: 符合J2EE规范的服务器平台，都是J2EE服务器，比如JBoss,weblogic,webspher
- J2EE也是一个框架,包括JDBC、JNDI、RMI、JMS、EJB、JTA等技术

###Servlet执行时一般实现哪几个方法？
- doGet(HttpServletRequest request,HttpServletResponse response)
- doPost(HttpServletRequest request,HttpServletResponse response)
- init()
- destroy()

###filter时序图,流程,servlet处理完之后filter还能否继续修改request和response的内容
>Servlet过滤器能够在Servlet被调用之前检查Request对象，修改Request Header和Request内容。  
Servlet被调用之后检查Response对象，修改Response Header和Response内容。Servlet过滤器负责过滤的Web组件可以是Servlet,JSP和HTML。  
Servler过滤器实现了javax.Servlet.Filter的接口；  
init(Filter Config)Servlet过滤器的初始化方法，Servlet过滤器实例后将调用这个方法，在这个方法中可以读取web.xml 文件中Servlet过滤器的初始化参数。  
destory():Servlet容器在销毁过滤器的实例前调用该方法，在这个方法中可以释放Servlet过滤器占用的资源。  
doFilter(ServletRequest ,ServletResponse,FilterChain):完成实际的过滤。  

![flter流程图](https://github.com/GitOrgLan/interview/blob/master/img/j2ee/filter%E6%B5%81%E7%A8%8B%E5%9B%BE.gif)

#filter,listener,servlet区别
- Filter  
	实现javax.servlet.Filter接口，在web.xml中配置与标签指定使用哪个Filter实现类过滤哪些URL链接。  
	只在web启动时进行初始化操作。filter 流程是线性的， url传来之后，检查之后，可保持原来的流程继续向下执行，被下一个filter, servlet接收等，而servlet 处理之后，不会继续向下传递。  
	filter功能可用来保持流程继续按照原来的方式进行下去，或者主导流程，而servlet的功能主要用来主导流程。  
	特点：可以在响应之前修改Request和Response的头部，只能转发请求，不能直接发出响应。filter可用来进行字符编码的过滤，检测用户是否登陆的过滤，禁止页面缓存等  
- Servlet  
	servlet 流程是短的，url传来之后，就对其进行处理，之后返回或转向到某一自己指定的页面。它主要用来在业务处理之前进行控制。  
- Listener  
	servlet,filter都是针对url之类的，而listener是针对对象的操作的，如session的创建session.setAttribute的发生，在这样的事件发生时做一些事情。  

###EJB与JAVA BEAN的区别？
>EJB与JAVA BEAN是SUN的不同组件规范，EJB是在容器中运行的，分步式的，而JAVA BEAN主要是一种可利用的组件，主要在客户端UI表现上。  

###JAVA解析XML的方式？
SAX、DOM、JDOM 、DOM4J 

- DOM(Document Object Model):	  
	DOM是以层次结构组织的节点或信息片断的集合。这个层次结构	允许开发人员在树中寻找特定信息。  分析该结构通常需要加载整个文档和构造层次结构，然后才能做任何工作。  
	由于它是基于信息层次的，因而DOM被认为是基于树或基于对象的。  
	DOM以及广义的基于树的处理具有几个优点。  
	首先，由于树在内存中是持久的，因此可以修改它以便应用程序能对数据和结	构作出更改。  
	它还可以在任何时候在树中上下导航，而不是像SAX那样是一次性的处理  
- SAX(Simple API for XML):	
	SAX，事件驱动。当解析器发现元素开始、元素结束、文本、文档的开始或结束等时，发送事件，程序员编写响应这些事件的代码，保存数据。  
	优点：不用事先调入整个文档，占用资源少；SAX解析器代码比DOM解析器代码小，适于Applet，下载。  
	缺点：不是持久的；事件过后，若没保存数据，那么数据就丢了；无状态性；从事件中只能得到文本，但不知该文本属于哪个元素；  
- JDOM:	  
	JDOM与DOM主要有两方面不同。  
	首先，JDOM仅使用具体类而不使用接口。这在某些方面简化了API，但是也限制了灵活性。  
	第二，API大量使用了Collections类，简化了那些已经熟悉这些类的Java开发者的使用。  
- DOM4J :   
	它是JDOM的一种智能分支。它合并了许多超出基本XML文档表示的功能，包括集成的XPath支持、XML Schema支持以及用于大文档或流化文档的基于事件的处理。  
	解析JSON   
	googleJson fastJson等解析  
	JSONObject JsonArray  

###LINUX下线程，GDI类的解释。

###三大框架的原理及其优缺点
	S:18个拦截器 


###如何理解J2EE的系统架构?
>答：表现层，业务逻辑层，数据层。（MVC）
优点：移植性，安全性，可用性，可扩展性。J2EE平台的其他主要优点还有：自动负载平衡、可伸缩、容错和具有故障排除等功能。部署在J2EE环境中的组件将自动获得上述特性，而不必增加额外的代码开销。
缺点：系统和标准相当庞大和复杂，中小型企业用不到那么多的标准


###为什么要用：
>JSP、Servlet、JavaBean技术的出现给我们构建强大的企业应用系统提供了可能。但用这些技术构建的系统非常的繁乱，所以在此之上，我们需要一个规则、一个把这些技术组织起来的规则，这就是框架，Struts便应运而生。
基于Struts开发的应用由3类组件构成：控制器组件、模型组件、视图组件

###解释下面关于J2EE框架技术的名词
- JNDI:
	Java Naming & Directory Interface,JAVA命名目录服务.主要提供的功能是：提供一个目录系统，让其它各地的应用程序在其上面留下自己的索引，从而满足快速查找和定位分布式应用程序的功能.
- JMS：
	Java Message Service,JAVA消息服务.主要实现各个应用程序之间的通讯.包括点对点和广播.
- JTA：
	Java Transaction API,JAVA事务服务.提供各种分布式事务服务.应用程序只需调用其提供的接口即可.
- JAF: 
	Java Action FrameWork,JAVA安全认证框架.提供一些安全控制方面的框架.让开发者通过各种部署和自定义实现自己的个性安全控制策略.
- RMI:
	Remote Method Interface,远程方法调用 RMI/IIOP:（Remote Method Invocation /internet对象请求中介协议）他们主要用于通过远程调用服务。例如，远程有一台计算机上运行一个程序，它提供股票分析服务，我们可以在本地计算机上实现对其直接调用。当然这是要通过一定的规范才能在异构的系统之间进行通信。RMI是JAVA特有的。

###关于是Web Service?
>Web Service就是为了使原来各孤立的站点之间的信息能够相互通信、共享而提出的一种接口。  
Web Service所使用的是Internet上统一、开放的标准，如HTTP、XML、SOAP（简单对象访问协议）、WSDL等，所以Web Service可以在任何支持这些标准的环境（Windows,Linux）中使用。  
注：SOAP协议（Simple Object Access Protocal,简单对象访问协议）,它是一个用于分散和分布式环境下网络信息交换的基于XML的通讯协议。  
在此协议下，软件组件或应用程序能够通过标准的HTTP协议进行通讯。它的设计目标就是简单性和扩展性，这有助于大量异构程序和平台之间的互操作性，从而使存在的应用程序能够被广泛的用户访问。  

- 优势：
	1. 跨平台；
	- SOAP协议是基于XML和HTTP这些业界的标准的，得到了所有的重要公司的支持。
	- 由于使用了SOAP，数据是以ASCII文本的方式而非二进制传输，调试很方便；并且由于这样，它的数据容易通过防火墙，不需要防火墙为了程序而单独开一个“漏洞”。
	- 此外，WebService实现的技术难度要比CORBA和DCOM小得多。
	- 要实现B2B集成，EDI比较完善与比较复杂；而用WebService则可以低成本的实现，小公司也可以用上。
	- 在C/S的程序中，WebService可以实现网页无整体刷新的与服务器打交道并取数。
- 缺点：
	- WebService使用了XML对数据封装，会造成大量的数据要在网络中传输。
	- WebService规范没有规定任何与实现相关的细节，包括对象模型、编程语言，这一点，它不如CORBA。




###DWR框架简介   
- DWR框架是一个可以允许你去创建AJAX WEB站点的JAVA开源库。它可以让你在浏览器的JavaScript代码中调用Web服务器的Java代码，就像Java代码在浏览器中一样。DWR工作原理是通过动态把Java类生成JavaScript，让使用者感觉调用就像发生在浏览器端。   
- DWR的使用场合   
当我们的业务需要在页面不提交的情况下访问服务器端并实现页面数据局部刷新时，我们就可以使用DWR。




###java web 过滤器跟拦截器的区别和使用
	[链接](http://zhidao.baidu.com/link?url=scCMDfkX5wRA13uCX2KC5v_94K7pbYNDf4S5NYJloNOAPWMtLZcMPpwkp2Y5H5dpIR5CqPec5_qY5Q1mUoVdOpZyVTRKVU3COl9ab1wFpeC)  

拦截器与过滤器的区别 ：        
- 拦截器是基于java的反射机制的，而过滤器是基于函数回调。     
- 拦截器不依赖与servlet容器，过滤器依赖与servlet容器。（过滤字符）      
- 拦截器只能对action请求起作用，而过滤器则可以对几乎所有的请求起作用。     
- 拦截器可以访问action上下文、值栈里的对象，而过滤器不能访问。      
- 在action的生命周期中，拦截器可以多次被调用，而过滤器只能在容器初始化时被调用一次





