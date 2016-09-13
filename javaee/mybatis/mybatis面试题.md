
###ibatis 中的#与$的区别
>是否进行类型匹配  
在Ibatis中我们使用SqlMap进行Sql查询时需要引用参数，在参数引用中遇到的符号#和$之间的区分为，#可以进行与编译，进行类型匹配，而$不进行数据类型匹配  	
使用#传入参数，sql语句解析是会加上"",比如  select * from table where name = #{name} ,传入的name为小李，那么最后打印出来的就是select * from table where name = ‘小李’  
如果你要做动态的排序，比如  order by   column，这个时候务必要用${},因为如果你使用了#{},那么打印出来的将会是select * from table order by  'name'  ,这样是没用  
[链接](http://blog.csdn.net/jimmy609/article/details/43020143)

###讲下MyBatis的缓存
MyBatis的缓存分为一级缓存和二级缓存,
一级缓存放在session里面,默认就有,二级缓存放在它的命名空间里,默认是打开的,使用二级缓存属性类需要实现Serializable序列化接口(可用来保存对象的状态),可在它的映射文件中配置  
```
<cache/>
```

###MyBatis(IBatis)的好处是什么
>ibatis把sql语句从Java源程序中独立出来，放在单独的XML文件中编写，给程序的维护带来了很大便利。  
ibatis封装了底层JDBC API的调用细节，并能自动将结果集转换成Java Bean对象，大大简化了Java数据库编程的重复工作。  
因为Ibatis需要程序员自己去编写sql语句，程序员可以结合数据库自身的特点灵活控制sql语句，
因此能够实现比hibernate等全自动orm框架更高的查询效率，能够完成复杂查询  