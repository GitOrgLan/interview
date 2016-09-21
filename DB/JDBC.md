###JDBC中，statement,prepared statement ,Callable statement的区别是什么?
- Statement 接口提供了执行语句和获取结果的基本方法。
- PreparedStatement 接口添加了处理 IN 参数的方法；
- CallableStatement 添加了处理 OUT 参数的方法。  
	是PreparedStatement的子类,它只是用来执行存储过程的.

>PreparedStatement:对于同一条语句的多次执行,
Statement每次都要把SQL语句发送给数据库,这样做效率明显不高,
而如果数据库支持预编译,PreparedStatement可以先把要执行的语句一次发给它,然后每次执行而不必发送相同的语句,效率当然提高,
当然如果数据库不支持预编译,PreparedStatement会象Statement一样工作,只是效率不高而不需要用户工手干预.
另外PreparedStatement还支持接收参数.在预编译后只要传输不同的参数就可以执行,大大提高了性能.

###注册驱动的三种方式
-  DriverManager.registerDriver(new com.mysql.jdbc.Driver())     
	会造成DriverManager中产生两个一样的驱动，并会对具体的驱动类产生依赖。   
	具体来说就是：   
	- 加载的时候注册一次驱动（原因请看第三中注册方式），实例化的时候又注册一次。所以两次。 
	- 由于实例化了com.mysql.jdbc.Driver.class，导致必须导入该类(就是要把这个类import进去)，从而具体驱动产生了依赖。不方便扩展代码。   

- System.setProperty("jdbc.drivers","com.mysql.jdbc.Driver")  
	通过系统的属性设置注册驱动  
	如果要注册多个驱动，则System.setProperty  ("jdbc.drivers","com.mysql.jdbc.Driver:com.oracle.jdbc.Driver");   
	虽然不会对具体的驱动类产生依赖；但注册不太方便，所以很少使用。 

- Class.forName("com.mysql.jdbc.Driver")  
	推荐这种方式，不会对具体的驱动类产生依赖（就是不用import package了）。   
	其实这个只是把com.mysql.jdbc.Driver.class这个类装载进去，但是关键就在于，在 
	这个类中，有个静态块，如下：   
	```
	static{ 
	   try{ 
			java.sql.DriverManager.registerDriver(new Driver()); 
		}catch(SQLException e){ 
	   		throw new RuntimeException("can't register driver!"); 
		} 
	} 
	```
	就是因为这个代码块，让类在加载的时候就把驱动注册进去了！ 