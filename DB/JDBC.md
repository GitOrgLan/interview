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

###说出数据连接池的工作机制是什么？
	J2EE服务器启动时会建立一定数量的池连接，并一直维持不少于此数目的池连接。
	客户端程序需要连接时，池驱动程序会返回一个未使用的池连接并将其表记为忙。
	如果当前没有空闲连接，池驱动程序就新建一定数量的连接，新建连接的数量有配置参数决定。
	(通过参数可以决定最大连接数是多少，服务器启动的时候建立多少连接，池中需要维持多少空闲连接等。)
	当使用的连接调用完成后，就可以将连接close，这个时候池驱动程序将此连接放回连接池并且标记为空闲，其他调用就可以使用这个连接。