###Hibernate工作原理及为什么要用？
原理：
1. 读取并解析配置文件
- 读取并解析映射信息，创建SessionFactory
- 打开Sesssion
- 创建事务Transation
- 持久化操作
- 提交事务
- 关闭Session
- 关闭SesstionFactory

###为什么要用：
- 对JDBC访问数据库的代码做了封装，大大简化了数据访问层繁琐的重复性代码。
- Hibernate是一个基于JDBC的主流持久化框架，是一个优秀的ORM实现。他很大程度的简化DAO层的编码工作
- hibernate使用Java反射机制，而不是字节码增强程序来实现透明性。
- hibernate的性能非常好，因为它是个轻量级框架。映射的灵活性很出色。它支持各种关系数据库，从一对一到多对多的各种复杂关系。

###Hibernate是如何延迟加载?
- Hibernate2延迟加载实现：a)实体对象缓存  b)集合（Collection）缓存
- Hibernate3 提供了属性的延迟加载功能
当Hibernate在查询数据的时候，数据并没有存在与内存中，当程序真正对数据的操作时，对象才存在与内存中，就实现了延迟加载，他节省了服务器的内存开销，从而提高了服务器的性能。  
Hibernate中怎样实现类之间的关系?(如：一对多、多对多的关系)  
类与类之间的关系主要体现在表与表之间的关系进行操作，它们都市对对象进行操作，我们程序中把所有的表与类都映射在一起，它们通过配置文件中的many-to-one、one-to-many、many-to-many  

###说下Hibernate的缓存机制
- 内部缓存存在Hibernate中又叫一级缓存，属于应用事物级缓存
- 二级缓存：
	- 应用及缓存
	- 分布式缓存  
	 	条件：数据不会被第三方修改、数据大小在可接受范围、数据更新频率低、同一数据被系统频繁使用、非关键数据
	- 第三方缓存的实现

###Hibernate的查询方式
- Sql、Criteria,object comptosition
- Hql：
	- 属性查询
	- 参数查询、命名参数查询
	- 关联查询
	- 分页查询
	- 统计函数

###如何优化Hibernate？
* 使用双向一对多关联，不使用单向一对多
* 灵活使用单向一对多关联
* 不用一对一，用多对一取代
* 配置对象缓存，不使用集合缓存
* 一对多集合使用Bag,多对多集合使用Set
* 继承类使用显式多态
* 表字段要少，表关联不要怕多，有二级缓存撑腰

###一些Spring和Hibernate的面试题(附答案)
- 简述你对IoC（Inversion of Control）的理解，描述一下Spring中实现DI（Dependency Injection）的几种方式。
	IoC将创建的职责从应用程序代码搬到了框架中。Spring对Setter注入和构造方法注入提供支持。（详见http://martinfowler.com/articles/injection.html，以及http: //www.redsaga.com/spring_ref/2.0/html/beans.html#beans-factory- collaborators）

###Hibernate中的update()和saveOrUpdate()的区别
saveOrUpdate()方法可以实现update()的功能，但会多些步骤，具体如下：
- 如果对象在该session中已经被持久化，不进行操作；
- 对象的标识符属性(identifier property)在数据库中不存在或者是个暂时的值，调用save()方法保存它；
- 如果session中的另一个对象有相同的标识符抛出一个异常；
- 以上皆不符合则调用update()更新之。


###hibernate一级缓存是什么，二级缓存是什么，延迟加载是什么。
[链接](http://www.open-open.com/lib/view/open1413527015465.html) 
 
- 首先要明白缓存是干什么的，缓存就是要将一些经常使用的数据缓存到内存或者各种储存介质中，当再次使用时可以不用去数据库中查询，减少与数据库的交互，提高性能。

	- 一级缓存是针对session级别的，当这个session关闭后这个缓存就不存在了。多次加载同一个持久化对象，只有第一次向数据库发送SQL语句加载，之后的加载都是基于缓存的
	- 二级缓存是SessionFactory级别的，二级缓存我们通常使用其他的一些开源组件，比如hibernate经常使用的就是ECache，这个缓存在整个应用服务器中都会有效的。但是在一般的应用程序中，sessionfactory会以单例的形式存在，所以在整个应用程序的生命周期里，sessionfactory会一直存在。既二级缓存也一直存在直到关闭应用程序。
	 
区别：两者的作用范围不同。

- 什么样的数据适合存放到第二级缓存中？
	- 很少被修改的数据
	- 不是很重要的数据，允许出现偶尔并发的数据
	- 不会被并发访问的数据
	- 参考数据,指的是供应用参考的常量数据，它的实例数目有限，它的实例会被许多其他类的实例引用，实例极少或者从来不会被修改。

- 不适合存放到第二级缓存的数据？
	- 经常被修改的数据
	- 财务数据，绝对不允许出现并发
	- 与其他应用共享的数据。
	- 常用的缓存插件 Hibernater二级缓存是一个插件，下面是几种常用的缓存插件：
	
- Ehcache：可作为进程范围的缓存，存放数据的物理介质可以是内存或硬盘，对Hibernate的查询缓存提供了支持。
- OSCache：可作为进程范围的缓存，存放数据的物理介质可以是内存或硬盘，提供了丰富的缓存数据过期策略，对Hibernate的查询缓存提供了支持。
- SwarmCache：可作为群集范围内的缓存，但不支持Hibernate的查询缓存。
- JBossCache：可作为群集范围内的缓存，支持事务型并发访问策略，对Hibernate的查询缓存提供了支持。


###Spring对多种ORM框架提供了很好的支持，简单描述在Spring中使用Hibernate的方法，并结合事务管理。
>在context中定义DataSource，创建SessionFactoy，设置参数；  
DAO类继承HibernateDaoSupport，实现具体接口，从中获得HibernateTemplate进行具体操作。  
在使用中如果遇到OpenSessionInView的问题，可以添加OpenSessionInViewFilter或OpenSessionInViewInterceptor。（详见Spring framework 2.0 Reference的12.2节Hibernate）  
声明式事务需声明事务管理器  


###spring+hibernate的配置文件中的主要类有那些?如何配置?
>dataSource  
sessionFactory:hibernate.cfg.xml  
transactionManager  
userDao (extends HibernateDaoSupport)  
sessionFactory  
facade  
proxy  
sessionFactory  
transactionManager  
facade  
 
###Spring里面如何定义hibernate mapping？ applicationContext.xml  ”mappingResources”
添加hibernate mapping 文件到web/WEB-INF目录下的applicationContext.xml文件里面。示例如下：
```
<property name=”mappingResources”>
<list>
<value>org/appfuse/model/User.hbm.xml</value>
</list>
</property>
```

