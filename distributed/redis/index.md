## 常见问题

### 什么是redis

Redis 是一个基于内存的高性能key-value数据库

### Reids的特点　

Redis本质上是一个Key-Value类型的内存数据库，很像memcached，整个数据库统统加载在内存当中进行操作，定期通过异步操作把数据库数据flush到硬盘上进行保存。因为是纯内存操作，Redis的性能非常出色，每秒可以处理超过 10万次读写操作，是已知性能最快的Key-Value DB。

　　　Redis的出色之处不仅仅是性能，Redis最大的魅力是支持保存多种数据结构，此外单个value的最大限制是1GB，不像 memcached只能保存1MB的数据，因此Redis可以用来实现很多有用的功能，比方说用他的List来做FIFO双向链表，实现一个轻量级的高性能消息队列服务，用他的Set可以做高性能的tag系统等等。另外Redis也可以对存入的Key-Value设置expire时间，因此也可以被当作一 个功能加强版的memcached来用。

　　　Redis的主要缺点是数据库容量受到物理内存的限制，不能用作海量数据的高性能读写，因此Redis适合的场景主要局限在较小数据量的高性能操作和运算上。
　　　
### 使用redis有哪些好处

- 速度快，因为数据存在内存中，类似于HashMap，HashMap的优势就是查找和操作的时间复杂度都是O(1) 
- 支持丰富数据类型，支持string，list，set，sorted set，hash 
- 支持事务，操作都是原子性，所谓的原子性就是对数据的更改要么全部执行，要么全部不执行 
- 丰富的特性：可用于缓存，消息，按key设置过期时间，过期后将会自动删除

### redis相比memcached有哪些优势

- memcached所有的值均是简单的字符串，redis作为其替代者，支持更为丰富的数据类型 
- redis的速度比memcached快很多 
- redis可以持久化其数据

### redis常见性能问题和解决方案

- Master写内存快照，save命令调度rdbSave函数，会阻塞主线程的工作，当快照比较大时对性能影响是非常大的，会间断性暂停服务，所以Master最好不要写内存快照。
- Master AOF持久化，如果不重写AOF文件，这个持久化方式对性能的影响是最小的，但是AOF文件会不断增大，AOF文件过大会影响Master重启的恢复速度。Master最好不要做任何持久化工作，包括内存快照和AOF日志文件，特别是不要启用内存快照做持久化,如果数据比较关键，某个Slave开启AOF备份数据，策略为每秒同步一次。
- Master调用BGREWRITEAOF重写AOF文件，AOF在重写的时候会占大量的CPU和内存资源，导致服务load过高，出现短暂服务暂停现象。
- Redis主从复制的性能问题，为了主从复制的速度和连接的稳定性，Slave和Master最好在同一个局域网内

### 为什么redis需要把所有数据放到内存中

Redis为了达到最快的读写速度将数据都读到内存中，并通过异步的方式将数据写入磁盘。所以redis具有快速和数据持久化的特征。如果不将数据放在内存中，磁盘I/O速度为严重影响redis的性能。在内存越来越便宜的今天，redis将会越来越受欢迎。
如果设置了最大使用的内存，则数据已有记录数达到内存限值后不能继续插入新值

### redis的并发竞争问题如何解决

Redis为单进程单线程模式，采用队列模式将并发访问变为串行访问。Redis本身没有锁的概念，Redis对于多个客户端连接并不存在竞争，但是在Jedis客户端对Redis进行并发访问时会发生连接超时、数据转换错误、阻塞、客户端关闭连接等问题，这些问题均是由于客户端连接混乱造成。对此有2种解决方法：

- 客户端角度，为保证每个客户端间正常有序与Redis进行通信，对连接进行池化，同时对客户端读写Redis操作采用内部锁synchronized。
- 服务器角度，利用setnx实现锁。

注：对于第一种，需要应用程序自己处理资源的同步，可以使用的方法比较通俗，可以使用synchronized也可以使用lock；第二种需要用到Redis的setnx命令，但是需要注意一些问题。

### redis 最适合的场景

- 会话缓存（Session Cache）
　　最常用的一种使用Redis的情景是会话缓存（session cache）。用Redis缓存会话比其他存储（如Memcached）的优势在于：Redis提供持久化。当维护一个不是严格要求一致性的缓存时，如果用户的购物车信息全部丢失，大部分人都会不高兴的。

- 全页缓存（FPC）
　　除基本的会话token之外，Redis还提供很简便的FPC平台。回到一致性问题，即使重启了Redis实例，因为有磁盘的持久化，用户也不会看到页面加载速度的下降，这是一个极大改进，类似PHP本地FPC。
再次以Magento为例，Magento提供一个插件来使用Redis作为全页缓存后端。
此外，对WordPress的用户来说，Pantheon有一个非常好的插件 wp-redis，这个插件能帮助你以最快速度加载你曾浏览过的页面。

- 队列
　　Reids在内存存储引擎领域的一大优点是提供 list 和 set 操作，这使得Redis能作为一个很好的消息队列平台来使用。Redis作为队列使用的操作，就类似于本地程序语言（如Python）对 list 的 push/pop 操作。
　　如果你快速的在Google中搜索“Redis queues”，你马上就能找到大量的开源项目，这些项目的目的就是利用Redis创建非常好的后端工具，以满足各种队列需求。例如，Celery有一个后台就是使用Redis作为broker，你可以从这里去查看。

- 排行榜/计数器

　　Redis在内存中对数字进行递增或递减的操作实现的非常好。集合（Set）和有序集合（Sorted Set）也使得我们在执行这些操作的时候变的非常简单，Redis只是正好提供了这两种数据结构。所以，我们要从排序集合中获取到排名最靠前的10个用户–我们称之为“user_scores”，我们只需要像下面一样执行即可：
　　当然，这是假定你是根据你用户的分数做递增的排序。如果你想返回用户及用户的分数，你需要这样执行：ZRANGE user_scores 0 10 WITHSCORES

- 发布/订阅

　　Redis的发布/订阅功能。发布/订阅的使用场景确实非常多。我已看见人们在社交网络连接中使用，还可作为基于发布/订阅的脚本触发器，甚至用Redis的发布/订阅功能来建立聊天系统！
　　
## 数据结构

### 五大数据类型
[参考链接](https://www.cnblogs.com/ysocean/p/9080940.html)
### 底层数据结构
[参考链接](https://www.cnblogs.com/ysocean/p/9080942.html)
### 五大数据类型的底层实现
[参考链接](https://www.cnblogs.com/ysocean/p/9102811.html)

### 相关命令时间复杂度

#### String
String是Redis的基础数据类型，Redis没有Int、Float、Boolean等数据类型的概念，所有的基本类型在Redis中都以String体现。
与String相关的常用命令

- SET：为一个key设置value，可以配合EX/PX参数指定key的有效期，通过NX/XX参数针对key是否存在的情况进行区别操作，时间复杂度O(1)
- GET：获取某个key对应的value，时间复杂度O(1)
- GETSET：为一个key设置value，并返回该key的原value，时间复杂度O(1)
- MSET：为多个key设置value，时间复杂度O(N)
- MSETNX：同MSET，如果指定的key中有任意一个已存在，则不进行任何操作，时间复杂度O(N)
- MGET：获取多个key对应的value，时间复杂度O(N)

Redis的基本数据类型只有String，但Redis可以把String作为整型或浮点型数字来使用，主要体现在INCR、DECR类的命令上

- INCR：将key对应的value值自增1，并返回自增后的值。只对可以转换为整型的String数据起作用。时间复杂度O(1)
- INCRBY：将key对应的value值自增指定的整型数值，并返回自增后的值。只对可以转换为整型的String数据起作用。时间复杂度O(1)
- DECR/DECRBY：同INCR/INCRBY，自增改为自减。

#### List

- LPUSH：向指定List的左侧（即头部）插入1个或多个元素，返回插入后的List长度。时间复杂度O(N)，N为插入元素的数量
- RPUSH：同LPUSH，向指定List的右侧（即尾部）插入1或多个元素
- LPOP：从指定List的左侧（即头部）移除一个元素并返回，时间复杂度O(1)
- RPOP：同LPOP，从指定List的右侧（即尾部）移除1个元素并返回
- LPUSHX/RPUSHX：与LPUSH/RPUSH类似，区别在于，LPUSHX/RPUSHX操作的key如果不存在，则不会进行任何操作
- LLEN：返回指定List的长度，时间复杂度O(1)
- LRANGE：返回指定List中指定范围的元素（双端包含，即LRANGE key 0 10会返回11个元素），时间复杂度O(N)。应尽可能控制一次获取的元素数量，一次获取过大范围的List元素会导致延迟，同时对长度不可预知的List，避免使用LRANGE key 0 -1这样的完整遍历操作。

应谨慎使用的List相关命令

- LINDEX：返回指定List指定index上的元素，如果index越界，返回nil。index数值是回环的，即-1代表List最后一个位置，-2代表List倒数第二个位置。时间复杂度O(N)
- LSET：将指定List指定index上的元素设置为value，如果index越界则返回错误，时间复杂度O(N)，如果操作的是头/尾部的元素，则时间复杂度为O(1)
- LINSERT：向指定List中指定元素之前/之后插入一个新元素，并返回操作后的List长度。如果指定的元素不存在，返回-1。如果指定key不存在，不会进行任何操作，时间复杂度O(N)

为了更好支持队列的特性，Redis还提供了一系列阻塞式的操作命令，如BLPOP/BRPOP等，能够实现类似于BlockingQueue的能力，即在List为空时，阻塞该连接，直到List中有对象可以出队时再返回。

#### Hash

- HSET：将key对应的Hash中的field设置为value。如果该Hash不存在，会自动创建一个。时间复杂度O(1)
- HGET：返回指定Hash中field字段的值，时间复杂度O(1)
- HMSET/HMGET：同HSET和HGET，可以批量操作同一个key下的多个field，时间复杂度：O(N)，N为一次操作的field数量
- HSETNX：同HSET，但如field已经存在，HSETNX不会进行任何操作，时间复杂度O(1)
- HEXISTS：判断指定Hash中field是否存在，存在返回1，不存在返回0，时间复杂度O(1)
- HDEL：删除指定Hash中的field（1个或多个），时间复杂度：O(N)，N为操作的field数量
- HINCRBY：同INCRBY命令，对指定Hash中的一个field进行INCRBY，时间复杂度O(1)

应谨慎使用的Hash相关命令

- HGETALL：返回指定Hash中所有的field-value对。返回结果为数组，数组中field和value交替出现。时间复杂度O(N)
- HKEYS/HVALS：返回指定Hash中所有的field/value，时间复杂度O(N)

对于尺寸不可预知的Hash，应严格避免使用上面三个命令，而改为使用HSCAN命令进行游标式的遍历

#### Set

Redis Set是无序的，不可重复的String集合

- SADD：向指定Set中添加1个或多个member，如果指定Set不存在，会自动创建一个。时间复杂度O(N)，N为添加的member个数
- SREM：从指定Set中移除1个或多个member，时间复杂度O(N)，N为移除的member个数
- SRANDMEMBER：从指定Set中随机返回1个或多个member，时间复杂度O(N)，N为返回的member个数
- SPOP：从指定Set中随机移除并返回count个member，时间复杂度O(N)，N为移除的member个数
- SCARD：返回指定Set中的member个数，时间复杂度O(1)
- SISMEMBER：判断指定的value是否存在于指定Set中，时间复杂度O(1)
- SMOVE：将指定member从一个Set移至另一个Set

慎用的Set相关命令

- SMEMBERS：返回指定Hash中所有的member，时间复杂度O(N)
- SUNION/SUNIONSTORE：计算多个Set的并集并返回/存储至另一个Set中，时间复杂度O(N)，N为参与计算的所有集合的总member数
- SINTER/SINTERSTORE：计算多个Set的交集并返回/存储至另一个Set中，时间复杂度O(N)，N为参与计算的所有集合的总member数
- SDIFF/SDIFFSTORE：计算1个Set与1或多个Set的差集并返回/存储至另一个Set中，时间复杂度O(N)，N为参与计算的所有集合的总member数

上述几个命令涉及的计算量大，应谨慎使用，特别是在参与计算的Set尺寸不可知的情况下，应严格避免使用。可以考虑通过SSCAN命令遍历获取相关Set的全部member

#### Sorted Set

Redis Sorted Set是有序的、不可重复的String集合。Sorted Set中的每个元素都需要指派一个分数(score)，Sorted Set会根据score对元素进行升序排序。如果多个member拥有相同的score，则以字典序进行升序排序。

- ZADD：向指定Sorted Set中添加1个或多个member，时间复杂度O(Mlog(N))，M为添加的member数量，N为Sorted Set中的member数量
- ZREM：从指定Sorted Set中删除1个或多个member，时间复杂度O(Mlog(N))，M为删除的member数量，N为Sorted Set中的member数量
- ZCOUNT：返回指定Sorted Set中指定score范围内的member数量，时间复杂度：O(log(N))
- ZCARD：返回指定Sorted Set中的member数量，时间复杂度O(1)
- ZSCORE：返回指定Sorted Set中指定member的score，时间复杂度O(1)
- ZRANK/ZREVRANK：返回指定member在Sorted Set中的排名，ZRANK返回按升序排序的排名，ZREVRANK则返回按降序排序的排名。时间复杂度O(log(N))
- ZINCRBY：同INCRBY，对指定Sorted Set中的指定member的score进行自增，时间复杂度O(log(N))

慎用的Sorted Set相关命令

- ZRANGE/ZREVRANGE：返回指定Sorted Set中指定排名范围内的所有member，ZRANGE为按score升序排序，ZREVRANGE为按score降序排序，时间复杂度O(log(N)+M)，M为本次返回的member数
- ZRANGEBYSCORE/ZREVRANGEBYSCORE：返回指定Sorted Set中指定score范围内的所有member，返回结果以升序/降序排序，min和max可以指定为-inf和+inf，代表返回所有的member。时间复杂度O(log(N)+M)
- ZREMRANGEBYRANK/ZREMRANGEBYSCORE：移除Sorted Set中指定排名范围/指定score范围内的所有member。时间复杂度O(log(N)+M)

## redis持久化

### redis持久化的几种方式

- 快照（snapshots）

　　缺省情况情况下，Redis把数据快照存放在磁盘上的二进制文件中，文件名为dump.rdb。你可以配置Redis的持久化策略，例如数据集中每N秒钟有超过M次更新，就将数据写入磁盘；或者你可以手工调用命令SAVE或BGSAVE。
　　Redis forks子进程开始将数据写到临时RDB文件中。当子进程完成写RDB文件，用新文件替换老文件。这种方式可以使Redis使用copy-on-write技术。

- AOF

　　快照模式并不十分健壮，当系统停止，或者无意中Redis被kill掉，最后写入Redis的数据就会丢失。这对某些应用也许不是大问题，但对于要求高可靠性的应用来说，Redis就不是一个合适的选择。Append-only文件模式是另一种选择。你可以在配置文件中打开AOF模式

- 虚拟内存方式

　　当你的key很小而value很大时,使用VM的效果会比较好.因为这样节约的内存比较大.
　　当你的key不小时,可以考虑使用一些非常方法将很大的key变成很大的value,比如你可以考虑将key,value组合成一个新的value.
　　vm-max-threads这个参数,可以设置访问swap文件的线程数,设置最好不要超过机器的核数,如果设置为0,那么所有对swap文件的操作都是串行的.可能会造成比较长时间的延迟,但是对数据完整性有很好的保证.
　　
### RDB存在哪些优势

- 对性能影响最小。如前文所述，Redis在保存RDB快照时会fork出子进程进行，几乎不影响Redis处理客户端请求的效率。
- 每次快照会生成一个完整的数据快照文件，所以可以辅以其他手段保存多个时间点的快照（例如把每天0点的快照备份至其他存储媒介中），作为非常可靠的灾难恢复手段。
- 使用RDB文件进行数据恢复比使用AOF要快很多。

### RDB有哪些劣势

- 快照是定期生成的，所以在Redis crash时或多或少会丢失一部分数据。
- 如果数据集非常大且CPU不够强（比如单核CPU），Redis在fork子进程时可能会消耗相对较长的时间（长至1秒），影响这期间的客户端请求。

### AOF的优势

- 最安全，在启用appendfsync always时，任何已写入的数据都不会丢失，使用在启用appendfsync everysec也至多只会丢失1秒的数据。
- AOF文件在发生断电等问题时也不会损坏，即使出现了某条日志只写入了一半的情况，也可以使用redis-check-aof工具轻松修复。
- AOF文件易读，可修改，在进行了某些错误的数据清除操作后，只要AOF文件没有rewrite，就可以把AOF文件备份出来，把错误的命令删除，然后恢复数据。

### AOF的劣势

- AOF文件通常比RDB文件更大
- 性能消耗比RDB高
- 数据恢复速度比RDB慢
　　
### RDB持久化配置
Redis会将数据集的快照dump到dump.rdb文件中。此外，我们也可以通过配置文件来修改Redis服务器dump快照的频率，在打开6379.conf文件之后，我们搜索save，可以看到下面的配置信息：

- save 900 1              #在900秒(15分钟)之后，如果至少有1个key发生变化，则dump内存快照。
- save 300 10            #在300秒(5分钟)之后，如果至少有10个key发生变化，则dump内存快照。
- save 60 10000        #在60秒(1分钟)之后，如果至少有10000个key发生变化，则dump内存快照。

### AOF持久化配置
在Redis的配置文件中存在三种同步方式，它们分别是：

- appendfsync always     #每次有数据修改发生时都会写入AOF文件。
- appendfsync everysec  #每秒钟同步一次，该策略为AOF的缺省策略。
- appendfsync no          #从不同步。高效但是数据不会被持久化。

## redis的缓存失效策略和主键失效机制

作为缓存系统都要定期清理无效数据，就需要一个主键失效和淘汰策略。在Redis当中，有生存期的key被称为volatile。在创建缓存时，要为给定的key设置生存期，当key过期的时候（生存期为0），它可能会被删除

### 影响生存时间的一些操作

生存时间可以通过使用 DEL 命令来删除整个 key 来移除，或者被 SET 和 GETSET 命令覆盖原来的数据，也就是说，修改key对应的value和使用另外相同的key和value来覆盖以后，当前数据的生存时间不同。
　　比如说，对一个 key 执行INCR命令，对一个列表进行LPUSH命令，或者对一个哈希表执行HSET命令，这类操作都不会修改 key 本身的生存时间。另一方面，如果使用RENAME对一个 key 进行改名，那么改名后的 key的生存时间和改名前一样。
　　RENAME命令的另一种可能是，尝试将一个带生存时间的 key 改名成另一个带生存时间的 another_key ，这时旧的 another_key (以及它的生存时间)会被删除，然后旧的 key 会改名为 another_key ，因此，新的 another_key 的生存时间也和原本的 key 一样。使用PERSIST命令可以在不删除 key 的情况下，移除 key 的生存时间，让 key 重新成为一个persistent key 。
　　
### 如何更新生存时间

可以对一个已经带有生存时间的 key 执行EXPIRE命令，新指定的生存时间会取代旧的生存时间。过期时间的精度已经被控制在1ms之内，主键失效的时间复杂度是O（1），EXPIRE和TTL命令搭配使用，TTL可以查看key的当前生存时间。设置成功返回 1；当 key 不存在或者不能为 key 设置生存时间时，返回 0 。

### 数据淘汰策略

redis 内存数据集大小上升到一定大小的时候，就会施行数据淘汰策略（回收策略）。redis 提供 6种数据淘汰策略：

- volatile-lru：从已设置过期时间的数据集（server.db[i].expires）中挑选最近最少使用的数据淘汰
- volatile-ttl：从已设置过期时间的数据集（server.db[i].expires）中挑选将要过期的数据淘汰
- volatile-random：从已设置过期时间的数据集（server.db[i].expires）中任意选择数据淘汰
- allkeys-lru：从数据集（server.db[i].dict）中挑选最近最少使用的数据淘汰
- allkeys-random：从数据集（server.db[i].dict）中任意选择数据淘汰
- no-enviction（驱逐）：禁止驱逐数据
　　

## 事务

### redis事务的实现特征

和众多其它数据库一样，Redis作为NoSQL数据库也同样提供了事务机制。在Redis中，MULTI/EXEC/DISCARD/WATCH这四个命令是我们实现事务的基石。相信对有关系型数据库开发经验的开发者而言这一概念并不陌生，即便如此，我们还是会简要的列出Redis中事务的实现特征：

- 在事务中的所有命令都将会被串行化的顺序执行，事务执行期间，Redis不会再为其它客户端的请求提供任何服务，从而保证了事物中的所有命令被原子的执行。
- 和关系型数据库中的事务相比，在Redis事务中如果有某一条命令执行失败，其后的命令仍然会被继续执行。
- 我们可以通过MULTI命令开启一个事务，有关系型数据库开发经验的人可以将其理解为"BEGIN TRANSACTION"语句。在该语句之后执行的命令都将被视为事务之内的操作，最后我们可以通过执行EXEC/DISCARD命令来提交/回滚该事务内的所有操作。这两个Redis命令可被视为等同于关系型数据库中的COMMIT/ROLLBACK语句。
- 在事务开启之前，如果客户端与服务器之间出现通讯故障并导致网络断开，其后所有待执行的语句都将不会被服务器执行。然而如果网络中断事件是发生在客户端执行EXEC命令之后，那么该事务中的所有命令都会被服务器执行。
- 当使用Append-Only模式时，Redis会通过调用系统函数write将该事务内的所有写操作在本次调用中全部写入磁盘。然而如果在写入的过程中出现系统崩溃，如电源故障导致的宕机，那么此时也许只有部分数据被写入到磁盘，而另外一部分数据却已经丢失。

Redis服务器会在重新启动时执行一系列必要的一致性检测，一旦发现类似问题，就会立即退出并给出相应的错误提示。此时，我们就要充分利用Redis工具包中提供的redis-check-aof工具，该工具可以帮助我们定位到数据不一致的错误，并将已经写入的部分数据进行回滚。修复之后我们就可以再次重新启动Redis服务器了。

### WATCH命令和基于CAS的乐观锁

在Redis的事务中，WATCH命令可用于提供CAS(check-and-set)功能。假设我们通过WATCH命令在事务执行之前监控了多个Keys，倘若在WATCH之后有任何Key的值发生了变化，EXEC命令执行的事务都将被放弃，同时返回Null multi-bulk应答以通知调用者事务

　执行失败。例如，我们再次假设Redis中并未提供incr命令来完成键值的原子性递增，如果要实现该功能，我们只能自行编写相应的代码。其伪码如下：
```
　　val = GET mykey
　　val = val + 1
　　SET mykey $val
```
　　以上代码只有在单连接的情况下才可以保证执行结果是正确的，因为如果在同一时刻有多个客户端在同时执行该段代码，那么就会出现多线程程序中经常出现的一种错误场景--竞态争用(race condition)。比如，客户端A和B都在同一时刻读取了mykey的原有值，假设该值为10，此后两个客户端又均将该值加一后set回Redis服务器，这样就会导致mykey的结果为11，而不是我们认为的12。为了解决类似的问题，我们需要借助WATCH命令的帮助，见如下代码：
```
　　WATCH mykey
　　val = GET mykey
　　val = val + 1
　　MULTI
　　SET mykey $val
　　EXEC
```
　　和此前代码不同的是，新代码在获取mykey的值之前先通过WATCH命令监控了该键，此后又将set命令包围在事务中，这样就可以有效的保证每个连接在执行EXEC之前，如果当前连接获取的mykey的值被其它连接的客户端修改，那么当前连接的EXEC命令将执行失败。这样调用者在判断返回值后就可以获悉val是否被重新设置成功。