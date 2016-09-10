###说出Servlet的生命周期，并说出Servlet和CGI的区别？
>Servlet运行在Servlet容器中，其生命周期由容器来管理。Servlet的生命周期通过javax.servlet.Servlet接口中的init()、service()和destroy()方法来表示。
Servlet的生命周期包含了下面4个阶段：

- 加载和实例化  
	Servlet容器负责加载和实例化Servlet。当Servlet容器启动时，或者在容器检测到需要这个Servlet来响应第一个请求时，创建Servlet实例。
- 初始化  
	在Servlet实例化之后，容器将调用Servlet的init()方法初始化这个对象。  
	初始化的目的是为了让Servlet对象在处理客户端请求前完成一些初始化的工作，如建立数据库的连接，获取配置信息等。对于每一个Servlet实例，init()方法只被调用一次。  
	在初始化期间，Servlet实例可以使用容器为它准备的ServletConfig对象从Web应用程序的配置信息（在web.xml中配置）中获取初始化的参数信息。  
	在初始化期间，如果发生错误，Servlet实例可以抛出ServletException异常或者UnavailableException异常来通知容器。  
- 请求处理  
	Servlet容器调用Servlet的service()方法对请求进行处理。要注意的是，在service()方法调用之前，init()方法必须成功执行。  
	在service()方法中，Servlet实例通过ServletRequest对象得到客户端的相关信息和请求信息，在对请求进行处理后，调用ServletResponse对象的方法设置响应信息。  
	在service()方法执行期间，如果发生错误，Servlet实例可以抛出ServletException异常或者UnavailableException异常。  
- 服务终止  
	当容器检测到一个Servlet实例应该从服务中被移除的时候，容器就会调用实例的destroy()方法，以便让该实例可以释放它所使用的资源，保存数据到持久存储设备中。  
	当需要释放内存或者容器关闭时，容器就会调用Servlet实例的destroy()方法。在destroy()方法调用之后，容器会释放这个Servlet实例，该实例随后会被Java的垃圾收集器所回收。  
	如果再次需要这个Servlet处理请求，Servlet容器会创建一个新的Servlet实例。  
	在整个Servlet的生命周期过程中，创建Servlet实例、调用实例的init()和destroy()方法都只进行一次，当初始化完成后，Servlet容器会将该实例保存在内存中，通过调用它的service()方法，为接收到的请求服务   

###JAVA SERVLET API中forward() 与redirect()的区别
- 从地址栏显示来说 
	forward是服务器请求资源,服务器直接访问目标地址的URL,把那个URL的响应内容读取过来,然后把这些内容再发给浏览器.浏览器根本不知道服务器发送的内容从哪里来的,所以它的地址栏还是原来的地址.
	redirect是服务端根据逻辑,发送一个状态码,告诉浏览器重新去请求那个地址.所以地址栏显示的是新的URL.
- 从数据共享来说 
	forward:转发页面和转发到的页面可以共享request里面的数据.
	redirect:不能共享数据.
- 从运用地方来说 
	forward:一般用于用户登陆的时候,根据角色转发到相应的模块.
	redirect:一般用于用户注销登陆时返回主页面和跳转到其它的网站等.
- 从效率来说 
	forward:高.
	redirect:低

	
一句话，转发是服务器行为，重定向是客户端行为

- 转发过程：  
	客户浏览器发送http请求---->web服务器接受此请求-->调用内部的一个方法在容器内部完成请求处理和转发动作---->将目标资源 发送给客户；  
	在这里，转发的路径必须是同一个web容器下的url，其不能转向到其他的web路径上去，中间传递的是自己的容器内的request。在客 户浏览器路径栏显示的仍然是其第一次访问的路径，也就是说客户是感觉不到服务器做了转发的。转发行为是浏览器只做了一次访问请求。 

- 重定向过程：  
	客户浏览器发送http请求---->web服务器接受后发送302状态码响应及对应新的location给客户浏览器--》客户浏览器发现 是302响应，则自动再发送一个新的http请求，请求url是新的location地址----》服务器根据此请求寻找资源并发送给客户。  
	在这里 location可以重定向到任意URL，既然是浏览器重新发出了请求，则就没有什么request传递的概念了。在客户浏览器路径栏显示的是其重定向的 路径，客户可以观察到地址的变化的。
	重定向行为是浏览器做了至少两次的访问请求的。 


###JSP中动态INCLUDE与静态INCLUDE的区别？
>动态INCLUDE用jsp:include动作实现
<jsp:include page=”included.jsp” flush=”true”/>
它总是会检查所含文件中的变化，适合用于包含动态页面，并且可以带参数
静态INCLUDE用include伪码实现,定不会检查所含文件的变化，适用于包含静态页面
<%@ include file=”included.htm” %>

###四种会话跟踪技术？
- page 
	是代表与一个页面相关的对象和属性。一个页面由一个编译好的Java servlet 类.这既包括servlet 又包括被编译成servlet 的JSP页面
- request
	代表与Web客户机发出的一个请求相关的对象和属性。一个请求可能跨越多个页面，涉及多个Web组件。比如forward指令就可以使请求跨越多个页面。
- session 
	是是代表与用于某个Web客户机的一个用户体验相关的对象和属性。一个 Web会话可以也经常会跨越多个客户机请求。一次会话（session）通常持续于用户打开浏览器后的一系列访问中。
- application 
	是代表与整个Web应用程序相关的对象和属性。这实质上是跨越整个 Web应用程序，包括多个页面、请求和会话的一个全局作用域



###JSP和Servlet有哪些相同点和不同点，他们之间的联系是什么？
>Jsp和servlet相同的地方在于jsp和servlet本质上都是一个.java类。
Jsp表面看起来不是一个java文件，但是在jsp的生命周期中它首先生成一个servlet源程序，进而再编译执行。
Servlet和JSP最主要的不同点在于，Servlet的应用逻辑是在Java文件中，并且完全从表示层中的HTML里分离开来。而JSP的情况是Java和HTML可以组合成一个扩展名为.jsp的文件。
JSP侧重于视图，Servlet主要用于控制逻辑

###页面间对象传递的方法？
	使用request,session,application,cookie可以实现页面间对象传递。
	涉及到的方法：set/getAttribute设置获取属性方法。方法对于request，sesion，application都适用。对cookie来说request.getCookies()方法和response. addCookie(Cookie cookie);