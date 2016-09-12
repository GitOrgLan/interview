[链接](http://ifeve.com/spring-interview-questions-and-answers/)

##Spring 概述

###什么是spring?
>Spring 是个java企业级应用的开源开发框架。Spring主要用来开发Java应用，但是有些扩展是针对构建J2EE平台的web应用。Spring 框架目标是简化Java企业级应用开发，并通过POJO为基础的编程模型促进良好的编程习惯。


###使用Spring框架的好处是什么？
- 轻量：  
	Spring 是轻量的，基本的版本大约2MB。
- 控制反转：  
	Spring通过控制反转实现了松散耦合，对象们给出它们的依赖，而不是创建或查找依赖的对象们。
- 面向切面的编程(AOP)：  
	Spring支持面向切面的编程，并且把应用业务逻辑和系统服务分开。
- 容器：  
	Spring 包含并管理应用中对象的生命周期和配置。
- MVC框架：  
	Spring的WEB框架是个精心设计的框架，是Web框架的一个很好的替代品。
- 事务管理：  
	Spring 提供一个持续的事务管理接口，可以扩展到上至本地事务下至全局事务（JTA）。
- 异常处理：  
	Spring 提供方便的API把具体技术相关的异常（比如由JDBC，Hibernate or JDO抛出的）转化为一致的unchecked 异常。

###Spring由哪些模块组成?
以下是Spring 框架的基本模块：

1. Core module
- Bean module
- Context module
- Expression Language module
- JDBC module
- ORM module
- OXM module
- Java Messaging Service(JMS) module
- Transaction module
- Web module
- Web-Servlet module
- Web-Struts module
- Web-Portlet module

###Spring框架有哪几部分组成？
>Spring框架有七个模块组成组成，这7个模块(或组件)均可以单独存在，也可以与其它一个或多个模块联合使用，主要功能表现如下：

- Spring核心容器（Core）：  
	提供Spring框架的基本功能。核心容器的主要组件是BeanFactory，她是工厂模式的实现。    BeanFactory使用控制反转（Ioc）模式将应用程序的配置和依赖性规范与实际的应用代码程序分开。

- Spring AOP：  
	通过配置管理特性，Spring AOP模块直接面向方面的编程功能集成到了Spring框架中，所以可以很容易的使Spring框架管理的任何对象支持 AOP。  
	Spring AOP模块为基于Spring的应用程序中的对象提供了事务管理服务。通过使用Spring AOP，不用依赖于EJB组件，就可以将声明性事务管理集成到应用程序中。

- Spring ORM：  
	Spring框架集成了若干ORM框架,从而提供了ORM的对象关系工具,其中包括 JDO、Hibernate、iBatis和TopLink。  
	所有这些都遵从Spring的通用事务和DAO异常层结构。

- Spring DAO：  
	JDBC DAO抽象层提供了有意义的异常层次的结构，可用该结构来管理异常处理和不同数据供应商抛出的异常错误信息。  
	异常层次结构简化了错误处理，并且大大的降低了需要编写的异常代码数量（例如，打开和关系连接）。  
	Spring DAO的面向JDBC的异常遵从通用的DAO异常层结构。

- Spring WEB：
	Web上下文模块建立在上下文模块（Context）的基础之上，为基于Web服务的应用程序提供了上下文的服务。  
	所以Spring框架支持 Jakarta Struts的集成。  
	Web模块还简化了处理多部分请求及将请求参数绑定到域对象的工作。  

- Spring上下文（Context）：
	Spring上下文是一个配置文件，向Spring框架提供上下文信息。Spring上下文包括企业服务，例如 JNDI、EJB、电子邮件、国际化校验和调度功能。

- SpringMVC：
	Spring的MVC框架是一个全功能的构建Web应用程序的MVC实现。  
	通过策略接口，MVC框架变成为高度可配置的，MVC容纳的大量视图技术，包括JSP、Velocity、Tiles、iText和Pol  


###为什么用：
- AOP 
	让开发人员可以创建非行为性的关注点，称为横切关注点，并将它们插入到应用程序代码中。  
	使用 AOP 后，公共服务（比 如日志、持久性、事务等）就可以分解成方面并应用到域对象上，同时不会增加域对象的对象模型的复杂性。  

- IOC 
	允许创建一个可以构造对象的应用环境，然后向这些对象传递它们的协作对象。  
	正如单词 倒置 所表明的，IOC 就像反过来的 JNDI。没有使用一堆抽象工厂、服务定位器、单元素（singleton）和直接构造（straight construction），每一个对象都是用其协作对象构造的。因此是由容器管理协作对象（collaborator）。  

>Spring即使一个AOP框架，也是一IOC容器。 Spring 最好的地方是它有助于您替换对象。有了 Spring，只要用 JavaBean 属性和配置文件加入依赖性（协作对象）。然后可以很容易地在需要时替换具有类似接口的协作对象。

###解释一下Dependency injection(DI,依赖注入)和IOC(Inversion of control,控制反转)?
>依赖注入DI是一个程序设计模式和架构模型， 一些时候也称作控制反转，尽管在技术上来讲，依赖注入是一个IOC的特殊实现，依赖注入是指一个对象应用另外一个对象来提供一个特殊的能力  
例如：把一个数据库连接以参数的形式传到一个对象的结构方法里面而不是在那个对象内部自行创建一个连接。     
控制反转和依赖注入的基本思想就是把类的依赖从类内部转化到外部以减少依赖应用控制反转，对象在被创建的时候，由一个调控系统内所有对象的外界实体，将其所依赖的对象的引用，传递给它。    
也可以说，依赖被注入到对象中。所以，控制反转是，关于一个对象如何获取他所依赖的对象的引用，这个责任的反转。    
总结就是：对象间依赖关系交由容器统一调控和处理了    

###描述一下Spring中实现DI（Dependency Injection）的几种方式
- 方式一：接口注入，在实际中得到了普遍应用，即使在IOC的概念尚未确立时，这样的方法也已经频繁出现在我们的代码中。注解
- 方式二：Type2 IoC: Setter injection对象创建之后，将被依赖对象通过set方法设置进去
- 方式三：Type3 IoC: Constructor injection对象创建时，被依赖对象以构造方法参数的方式注入Spring的方式

###哪种依赖注入方式你建议使用，构造器注入，还是 Setter方法注入？
>你两种依赖方式都可以使用，构造器注入和Setter方法注入。最好的解决方案是用构造器参数实现强制依赖，setter方法实现可选依赖。

###简述你对IoC（Inversion of Control）的理解
>一个类需要用到某个接口的方法，我们需要将类A和接口B的实现关联起来，最简单的方法是类A中创建一个对于接口B的实现C的实例，但这种方法显然两者的依赖（Dependency）太大了。  
而IoC的方法是只在类A中定义好用于关联接口B的实现的方法，将类A，接口B和接口B的实现C放入IoC的 容器（Container）中，通过一定的配置由容器（Container）来实现类A与接口B的实现C的关联。

###ApplicationContext通常的实现是什么?
>FileSystemXmlApplicationContext ：此容器从一个XML文件中加载beans的定义，XML Bean 配置文件的全路径名必须提供给它的构造函数。
ClassPathXmlApplicationContext：此容器也从一个XML文件中加载beans的定义，这里，你需要正确设置classpath因为这个容器将在classpath里找bean配置。
WebXmlApplicationContext：此容器加载一个XML文件，此文件定义了一个WEB应用的所有bean。

###Bean 工厂和 Application contexts  有什么区别？
>Application contexts提供一种方法处理文本消息，一个通常的做法是加载文件资源（比如镜像），它们可以向注册为监听器的bean发布事件。  
>另外，在容器或容器内的对象上执行的那些不得不由bean工厂以程序化方式处理的操作，可以在Application contexts中以声明的方式处理。  
>Application contexts实现了MessageSource接口，该接口的实现以可插拔的方式提供获取本地化消息的方法。

###一个Spring的应用看起来象什么？
>一个定义了一些功能的接口。  
这实现包括属性，它的Setter ， getter 方法和函数等。  
Spring AOP。  
Spring 的XML 配置文件。  
使用以上功能的客户端程序。  


###spring工作机制及为什么要用?
spring mvc请所有的请求都提交给DispatcherServlet,它会委托应用系统的其他模块负责负责对请求进行真正的处理工作。

- DispatcherServlet查询一个或多个HandlerMapping,找到处理请求的Controller.
- DispatcherServlet请请求提交到目标Controller
- Controller进行业务逻辑处理后，会返回一个ModelAndView
- Dispathcher查询一个或多个ViewResolver视图解析器,找到ModelAndView对象指定的视图对象
- 视图对象负责渲染返回给客户端。

###简述spring 的事务传播行为和 隔离级别
[链接](http://blog.csdn.net/it_wangxiangpan/article/details/24180085)
 
- spring 的事务传播行为：  
	Spring在TransactionDefinition接口中规定了7种类型的事务传播行为，它们规定了事务方法和事务方法发生嵌套调用时事务如何进行传播：

	1. PROPAGATION_REQUIRED：  
		如果当前没有事务，就新建一个事务，如果已经存在一个事务中，加入- 到这个事务中。这是最常见的选择。
	- PROPAGATION_SUPPORTS：  
		支持当前事务，如果当前没有事务，就以非事务方式执行。
	- PROPAGATION_MANDATORY：  
		使用当前的事务，如果当前没有事务，就抛出异常。
	- PROPAGATION_REQUIRES_NEW：  
		新建事务，如果当前存在事务，把当前事务挂起。
	- PROPAGATION_NOT_SUPPORTED：  
		以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。
	- PROPAGATION_NEVER：  
		以非事务方式执行，如果当前存在事务，则抛出异常。
	- PROPAGATION_NESTED：  
		如果当前存在事务，则在嵌套事务内执行。如果当前没有事务，则执行与PROPAGATION_REQUIRED类似的操作。

- Spring 的隔离级别
	1. Serializable：最严格的级别，事务串行执行，资源消耗最大；
	- REPEATABLE READ：保证了一个事务不会修改已经由另一个事务读取但未提交（回滚）的数据。避免了”脏读取”和”不可重复读取”的情况，但是带来了更多的性能损失。
	- READ COMMITTED:大多数主流数据库的默认事务等级，保证了一个事务不会读到另一个并行事务已修改但未提交的数据，避免了”脏读取”。该级别适用于大多数系统。
	- Read Uncommitted：保证了读取过程中不会读取到非法数据。


##Spring Beans

###什么是Spring beans?
>Spring beans 是那些形成Spring应用的主干的java对象,它们被Spring IOC容器初始化，装配，和管理。这些beans通过容器中配置的元数据创建。比如，以XML文件中<bean/> 的形式定义。    
Spring 框架定义的beans都是单件beans。在bean tag中有个属性”singleton”，如果它被赋为TRUE，bean 就是单件，否则就是一个 prototype bean。默认是TRUE，所以所有在Spring框架中的beans 缺省都是单件。    

### 一个 Spring Bean 定义 包含什么？
>一个Spring Bean 的定义包含容器必知的所有配置元数据，包括如何创建一个bean，它的生命周期详情及它的依赖。

###如何给Spring 容器提供配置元数据?
这里有三种重要的方法给Spring 容器提供配置元数据。

- XML配置文件。
- 基于注解的配置。
- 基于java的配置。

###你怎样定义类的作用域? 
>当定义一个<bean> 在Spring里，我们还能给这个bean声明一个作用域。  
它可以通过bean 定义中的scope属性来定义。如，当Spring要在需要的时候每次生产一个新的bean实例，bean的scope属性被指定为prototype。  
另一方面，一个bean每次使用的时候必须返回同一个实例，这个bean的scope 属性 必须设为 singleton。  

###解释Spring支持的几种bean的作用域。
Spring框架支持以下五种bean的作用域：

- singleton : 
	当一个bean的作用域设置为singleton，那么Spring IOC容器中只会存在一个共享的bean实例，并且所有对bean的请求，只要id与该bean定义相匹配，则只会返回bean的同一实例。  
	换言之，当把一个bean定义设置为singleton作用域时，Spring IOC容器只会创建该bean定义的唯一实例。这个单一实例会被存储到单例缓存（singleton cache）中，并且所有针对该bean的后续请求和引用都将返回被缓存的对象实例，这里要注意的是singleton作用域和GOF设计模式中的单例是完全不同的，单例设计模式表示一个ClassLoader中只有一个class存在，而这里的singleton则表示一个容器对应一个bean，也就是说当一个bean被标识为singleton时候，spring的IOC容器中只会存在一个该bean。  
- prototype：  
	prototype作用域部署的bean，每一次请求（将其注入到另一个bean中，或者以程序的方式调用容器的getBean()方法）都会产生一个新的bean实例，相当于一个new的操作  
- request：  
	request表示该针对每一次HTTP请求都会产生一个新的bean，同时该bean仅在当前HttpRequest内有效  
- session：  
	session作用域表示该针对每一次HTTP请求都会产生一个新的bean，同时该bean仅在当前HTTP session内有效。  
- global-session：
	在一个全局的HTTP Session中，一个bean定义对应一个实例。该作用域仅在基于web的Spring ApplicationContext情形下有效。  

缺省的Spring bean 的作用域是Singleton.

###Spring框架中的单例bean是线程安全的吗?
>不，Spring框架中的单例bean不是线程安全的。

###解释Spring框架中bean的生命周期。  
>Spring容器 从XML 文件中读取bean的定义，并实例化bean。  
Spring根据bean的定义填充所有的属性。  
如果bean实现了BeanNameAware接口，Spring传递bean的ID到setBeanName方法。  
如果Bean 实现了 BeanFactoryAware 接口，Spring传递beanfactory 给setBeanFactory 方法。  
如果有任何与bean相关联的BeanPostProcessors，Spring会postProcesserBeforeInitialization()方法内调用它们。  
如果bean实现IntializingBean了，调用它的afterPropertySet方法，如果bean声明了初始化方法，调用此初始化方法。  
如果有BeanPostProcessors 和bean 关联，这些bean的postProcessAfterInitialization() 方法将被调用。  
如果bean实现了 DisposableBean，它将调用destroy()方法。

###哪些是重要的bean生命周期方法？ 你能重载它们吗？
>有两个重要的bean 生命周期方法，第一个是setup ， 它是在容器加载bean的时候被调用。第二个方法是 teardown  它是在容器卸载类的时候被调用。  
The bean 标签有两个重要的属性（init-method和destroy-method）。用它们你可以自己定制初始化和注销方法。它们也有相应的注解（@PostConstruct和@PreDestroy）。  

###什么是Spring的内部bean？
>当一个bean仅被用作另一个bean的属性时，它能被声明为一个内部bean，为了定义inner bean，在Spring 的 基于XML的 配置元数据中，可以在 <property/>或 <constructor-arg/> 元素内使用<bean/> 元素，内部bean通常是匿名的，它们的Scope一般是prototype。

###在Spring中如何注入一个java集合？
Spring提供以下几种集合的配置元素：

- <list>类型用于注入一列值，允许有相同的值。
- <set> 类型用于注入一组值，不允许有相同的值。
- <map> 类型用于注入一组键值对，键和值都可以为任意类型。
- <props>类型用于注入一组键值对，键和值都只能为String类型。

###什么是bean装配? 
>装配，或bean 装配是指在Spring 容器中把bean组装到一起，前提是容器需要知道bean的依赖关系，如何通过依赖注入来把它们装配到一起。

###什么是bean的自动装配？
>Spring 容器能够自动装配相互合作的bean，这意味着容器不需要<constructor-arg>和<property>配置，能通过Bean工厂自动处理bean之间的协作。

###解释不同方式的自动装配。
有五种自动装配的方式，可以用来指导Spring容器用自动装配方式来进行依赖注入。

- no：默认的方式是不进行自动装配，通过显式设置ref属性来进行装配。
- byName：通过参数名自动装配，Spring容器在配置文件中发现bean的autowire属性被设置成byname，之后容器试图匹配、装配和该bean的属性具有相同名字的bean。	
- byType:：通过参数类型自动装配，Spring容器在配置文件中发现bean的autowire属性被设置成byType，之后容器试图匹配、装配和该bean的属性具有相同类型的bean。如果有多个bean符合条件，则抛出错误。
- constructor：这个方式类似于byType， 但是要提供给构造器参数，如果没有确定的带参数的构造器参数类型，将会抛出异常。
- autodetect：首先尝试使用constructor来自动装配，如果无法工作，则使用byType方式。

###自动装配有哪些局限性 ?
自动装配的局限性是：

- 重写： 你仍需用 <constructor-arg>和 <property> 配置来定义依赖，意味着总要重写自动装配。
- 基本数据类型：你不能自动装配简单的属性，如基本数据类型，String字符串，和类。
- 模糊特性：自动装配不如显式装配精确，如果有可能，建议使用显式装配。

###你可以在Spring中注入一个null 和一个空字符串吗？
可以

###Bean的初始化
在配置文档中通过指定init-method 属性来完成
实现 org.springframwork.beans.factory.InitializingBean接口

###Bean的调用
- 使用BeanWrapper
- 使用BeanFactory
- 使用ApplicationConttext	

###Bean的销毁
- 使用配置文件中的 destory-method 属性
- 实现 org.springframwork.bean.factory.DisposebleBean接口

###spring的配置的主要标签是什么?有什么作用?
```
<beans>
<bean id=”” class=”” init=”” destroy=”” singleton=””>
<property name=””>
<value></value>
</property>
<property name=””>
<ref local></ref>
</property>
</bean>
</beans>
```
	
##Spring注解

###什么是基于Java的Spring注解配置? 给一些注解的例子.
基于Java的配置，允许你在少量的Java注解的帮助下，进行你的大部分Spring配置而非通过XML文件。

- 以@Configuration 注解为例，它用来标记类可以当做一个bean的定义，被Spring IOC容器使用。
- 另一个例子是@Bean注解，它表示此方法将要返回一个对象，作为一个bean注册进Spring应用上下文。

###什么是基于注解的容器配置?
>相对于XML文件，注解型的配置依赖于通过字节码元数据装配组件，而非尖括号的声明。  
开发者通过在相应的类，方法或属性上使用注解的方式，直接组件类中进行配置，而不是使用xml表述bean的装配关系。  

###怎样开启注解装配？
>注解装配在默认情况下是不开启的，为了使用注解装配，我们必须在Spring配置文件中配置 <context:annotation-config/>元素。

###@Required  注解
>这个注解表明bean的属性必须在配置的时候设置，通过一个bean定义的显式的属性值或通过自动装配，若@Required注解的bean属性未被设置，容器将抛出BeanInitializationException。

###@Autowired 注解
>@Autowired 注解提供了更细粒度的控制，包括在何处以及如何完成自动装配。它的用法和@Required一样，修饰setter方法、构造器、属性或者具有任意名称和/或多个参数的PN方法。
>
###@Qualifier 注解
当有多个相同类型的bean却只有一个需要自动装配时，将@Qualifier注解和@Autowire注解结合使用以消除这种混淆，指定需要装配的确切的bean。

##Spring数据访问

###在Spring框架中如何更有效地使用JDBC? 
>使用SpringJDBC框架，资源管理和错误处理的代价都会被减轻。所以开发者只需写statements 和 queries从数据存取数据，JDBC也可以在Spring框架提供的模板类的帮助下更有效地被使用，这个模板叫JdbcTemplate （例子见这里here）

###JdbcTemplate
>JdbcTemplate 类提供了很多便利的方法解决诸如把数据库数据转变成基本数据类型或对象，执行写好的或可调用的数据库操作语句，提供自定义的数据错误处理。

###Spring对DAO的支持
>Spring对数据访问对象（DAO）的支持旨在简化它和数据访问技术如JDBC，Hibernate or JDO 结合使用。
这使我们可以方便切换持久层。编码时也不用担心会捕获每种技术特有的异常。

###使用Spring通过什么方式访问Hibernate? 
在Spring中有两种方式访问Hibernate：

- 控制反转  HibernateTemplate和 Callback。
- 继承 HibernateDAOSupport提供一个AOP 拦截器。

###Spring支持的ORM
Spring支持以下ORM：

- Hibernate
- iBatis
- JPA (Java Persistence API)
- TopLink
- JDO (Java Data Objects)
- OJB

###Spring里面如何配置数据库驱动？DriverManagerDataSource
使用org.springframework.jdbc.datasource.DriverManagerDataSource”数据源来配置数据库驱动。示例如下：
```
	<bean id=”dataSource”>
	<property name=”driverClassName”>
	<value>org.hsqldb.jdbcDriver</value>
	</property>
	<property name=”url”>
	<value>jdbc:hsqldb:db/appfuse</value>
	</property>
	<property name=”username”><value>sa</value></property>
	<property name=”password”><value></value></property>
	</bean>
```

###spring的jdbc与传统的jdbc有什么区别，其核心类有那些?
>Spring的jdbc:节省代码，不管连接(Connection)，不管事务、不管异常、不管关闭(con.close() ps.close )  
TransactionTemplate(transactionManager):进行事务处理  
JdbcTemplate(dataSource):增、删、改、查 大量sql代码重复  

###如何通过HibernateDaoSupport将Spring和Hibernate结合起来？
	用Spring的 SessionFactory 调用 LocalSessionFactory。集成过程分三步：
	配置the Hibernate SessionFactory。
	继承HibernateDaoSupport实现一个DAO。
	在AOP支持的事务中装配。


###Spring对多种ORM框架提供了很好的支持，简单描述在Spring中使用Hibernate的方法。
>在context中定义DataSource，创建SessionFactoy，设置参数；DAO类继承HibernateDaoSupport，实现具体接口，从中获得HibernateTemplate进行具体操作。
在使用中如果遇到OpenSessionInView的问题，可以添加OpenSessionInViewFilter或OpenSessionInViewInterceptor

	48. Spring支持的事务管理类型
	Spring支持两种类型的事务管理：
	编程式事务管理：这意味你通过编程的方式管理事务，给你带来极大的灵活性，但是难维护。
	声明式事务管理：这意味着你可以将业务代码和事务管理分离，你只需用注解和XML配置来管理事务。

	49. Spring框架的事务管理有哪些优点？
	它为不同的事务API  如 JTA，JDBC，Hibernate，JPA 和JDO，提供一个不变的编程模式。
	它为编程式事务管理提供了一套简单的API而不是一些复杂的事务API如
	它支持声明式事务管理。
	它和Spring各种数据访问抽象层很好得集成。

	50. 你更倾向用那种事务管理类型？	大多数Spring框架的用户选择声明式事务管理，因为它对应用代码的影响最小，因此更符合一个无侵入的轻量级容器的思想。声明式事务管理要优于编程式事务管理，虽然比编程式事务管理（这种方式允许你通过代码控制事务）少了一点灵活性。
	
Spring面向切面编程（AOP）

	51.  解释AOP
	面向切面的编程，或AOP， 是一种编程技术，允许程序模块化横向切割关注点，或横切典型的责任划分，如日志和事务管理。

	52. Aspect 切面	AOP核心就是切面，它将多个类的通用行为封装成可重用的模块，该模块含有一组API提供横切功能。比如，一个日志模块可以被称作日志的AOP切面。根据需求的不同，一个应用程序可以有若干切面。在Spring AOP中，切面通过带有@Aspect注解的类实现。

	52. 在Spring AOP 中，关注点和横切关注的区别是什么？
	关注点是应用中一个模块的行为，一个关注点可能会被定义成一个我们想实现的一个功能。	横切关注点是一个关注点，此关注点是整个应用都会使用的功能，并影响整个应用，比如日志，安全和数据传输，几乎应用的每个模块都需要的功能。因此这些都属于横切关注点。

	54. 连接点
	连接点代表一个应用程序的某个位置，在这个位置我们可以插入一个AOP切面，它实际上是个应用程序执行Spring AOP的位置。

	55. 通知
	通知是个在方法执行前或执行后要做的动作，实际上是程序执行时要通过SpringAOP框架触发的代码段。
	Spring切面可以应用五种类型的通知：
	before：前置通知，在一个方法执行前被调用。
	after: 在方法执行之后调用的通知，无论方法执行是否成功。
	after-returning: 仅当方法成功完成后执行的通知。
	after-throwing: 在方法抛出异常退出时执行的通知。
	around: 在方法执行之前和之后调用的通知。

	56. 切点
	切入点是一个或一组连接点，通知将在这些位置执行。可以通过表达式或匹配的方式指明切入点。

	57. 什么是引入? 
	引入允许我们在已存在的类中增加新的方法和属性。

	58. 什么是目标对象? 
	被一个或者多个切面所通知的对象。它通常是一个代理对象。也指被通知（advised）对象。

	59. 什么是代理?
	代理是通知目标对象后创建的对象。从客户端的角度看，代理对象和目标对象是一样的。

	60. 有几种不同类型的自动代理？
	BeanNameAutoProxyCreator
	DefaultAdvisorAutoProxyCreator
	Metadata autoproxying

	61. 什么是织入。什么是织入应用的不同点？
	织入是将切面和到其他应用类型或对象连接或创建一个被通知对象的过程。
	织入可以在编译时，加载时，或运行时完成。

	62. 解释基于XML Schema方式的切面实现。
	在这种情况下，切面由常规类以及基于XML的配置实现。

	63. 解释基于注解的切面实现
	在这种情况下(基于@AspectJ的实现)，涉及到的切面声明的风格与带有java5标注的普通java类一致。

Spring 的MVC

	64. 什么是Spring的MVC框架？
	Spring 配备构建Web 应用的全功能MVC框架。Spring可以很便捷地和其他MVC框架集成，如Struts，Spring 的MVC框架用控制反转把业务对象和控制逻辑清晰地隔离。它也允许以声明的方式把请求参数和业务对象绑定。

	65. DispatcherServlet
	Spring的MVC框架是围绕DispatcherServlet来设计的，它用来处理所有的HTTP请求和响应。

	66. WebApplicationContext
	WebApplicationContext 继承了ApplicationContext  并增加了一些WEB应用必备的特有功能，它不同于一般的ApplicationContext ，因为它能处理主题，并找到被关联的servlet。

	67. 什么是Spring MVC框架的控制器？	控制器提供一个访问应用程序的行为，此行为通常通过服务接口实现。控制器解析用户输入并将其转换为一个由视图呈现给用户的模型。Spring用一个非常抽象的方式实现了一个控制层，允许用户创建多种用途的控制器。

	68. @Controller 注解
	该注解表明该类扮演控制器的角色，Spring不需要你继承任何其他控制器基类或引用Servlet API。

	69. @RequestMapping 注解
	该注解是用来映射一个URL到一个类或一个特定的方处理法上。
	
其他

	spring中的BeanFactory与ApplicationContext的作用和区别？
	作用：
	1. BeanFactory负责读取bean配置文档，管理bean的加载，实例化，维护bean之间的依赖关系，负责bean的声明周期。
	2. ApplicationContext除了提供上述BeanFactory所能提供的功能之外，还提供了更完整的框架功能：
		a. 国际化支持
		b. 资源访问：Resource rs = ctx. getResource(”classpath:config.properties”), “file:c:/config.properties”
		c. 事件传递：通过实现ApplicationContextAware接口


###如何在Spring的applicationContext.xml里面使用JNDI而不是datasource? JndiObjectFactoryBean
	可以使用”org.springframework.jndi.JndiObjectFactoryBean”来实现。   
	示例如下：  
	<bean id=”dataSource”>
 	<property name=”jndiName”>
 	<value>java:comp/env/jdbc/appfuse</value>
 	</property>
 	</bean>

###Spring里面applicationContext.xml文件能不能改成其他文件名？可以配置
	ContextLoaderListener是一个ServletContextListener, 它在你的web应用启动的时候初始化。缺省情况下， 它会在WEB-INF/applicationContext.xml文件找Spring的配置。 你可以通过定义一个<context-param>元素名字为”contextConfigLocation”来改变Spring配置文件的位置。示例如下：
	<listener>
	<listener-class>org.springframework.web.context.ContextLoaderListener
	<context-param>
	<param-name>contextConfigLocation</param-name>
	<param-value>/WEB-INF/xyz.xml</param-value>
	</context-param>
	</listener-class>
	</listener>



###Spring如何实现事件处理?
	事件
	Extends ApplicationEvent
	监听器
	Implements ApplicationListener
	事件源
	Implements ApplicationContextAware
	在applicationContext.xml中配置事件源、监听器
	先得到事件源，调用事件源的方法，通知监听器。



###spring中的BeanFactory与ApplicationContext的作用和区别？
作用：
- BeanFactory负责读取bean配置文档，管理bean的加载，实例化，维护bean之间的依赖关系，负责bean的声明周期。
- ApplicationContext除了提供上述BeanFactory所能提供的功能之外，还提供了更完整的框架功能：
	- 国际化支持
	- 资源访问：Resource rs = ctx. getResource(”classpath:config.properties”), “file:c:/config.properties”
	- 事件传递：通过实现ApplicationContextAware接口

- 常用的获取ApplicationContext的方法：
	FileSystemXmlApplicationContext：从文件系统或者url指定的xml配置文件创建，参数为配置文件名或文件名数组  
	ClassPathXmlApplicationContext：从classpath的xml配置文件创建，可以从jar包中读取配置文件  
	WebApplicationContextUtils：从web应用的根目录读取配置文件，需要先在web.xml中配置，可以配置监听器或者servlet来实现  
	通过如下方法取出applicationContext实例:  
	ApplicationContext ac=WebApplicationContextUtils.getWebApplicationContext(this.getServletContext); 

###如何在spring中实现国际化?
	在applicationContext.xml加载一个bean
	<bean id=”messageSource” class=”org.springframework.context.support.ResourceBundleMessageSource”>
	<property name=”basename”>
	<value>message</value>
	</property>
	</bean>
	在src目录下建多个properties文件Ø
	对于非英文的要用native2asciiØ -encoding gb2312 源  目转化文件相关内容
	其命名格式是message_语言_国家。Ø
	页面中的中显示提示信息，键名取键值。Ø
	当给定国家，系统会自动加载对应的国家的properties信息。Ø
	通过applictionContext.getMessage(“键名”,”参数”,”区域”)取出相关的信息。Ø