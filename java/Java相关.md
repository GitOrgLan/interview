[TOC]
#数据结构在Java中的实现
代码基于JDK8
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

##HashMap
内部有成员变量，table，类型为`Node &lt;K,V&gt;[]`。Node的成员变量为hash，key，value，next。可以说HashMap是一个链表的数组的包装类。

###put
计算has值后调用`putVal()`，如果table为null或者大小为0，调用`resize()`得到一个不为空的table，在其中要根据threshold来判断是否扩容，threshold＝容量*加载因子。如果旧容量的二倍小于最大容量并且旧容量大于默认容量，就将新的threshold设置为旧阈值的二倍。如果旧容量==0并且旧阈值==0，令新容量 = 默认容量，阈值 = 默认阈值。设置完毕之后创建新的table，并将旧table的内容移入其中后返回该table。
第二步是插入元素，根据hash值计算位置，分以下几种情况
- 当前位置为空，则直接把值包装成节点并且插入。
- 当前位置不为空，但是此位置上元素的key和新插入元素的key相同，则替换旧的元素。
- 当前位置不为空，并且该位置元素类型为TreeNode(Node的子类，红黑树的节点)，执行`TreeNode.putTreeVal()`，将新节点插入已存在的树中。
- 最后一种情况，类似之前的版本，遍历该节点下链接的链表，如果有相同key的，替换值，没有的话新建节点插入链表的尾端。这里多了一个相对于之前版本的判断，如果该节点下的链表数目超过了'树化'的阈值，就调用`treeifyBin()`转换成树。

#引用
##ReferenceQueue
当一个obj被gc掉之后，其相应的包装reference，即ref对象会被放入queue中。我们可以从queue中获取到相应的对象信息，同时进行额外的处理。比如反向操作，数据清理等。
具体可以是扩展Reference之后，在实例中持有相应集合的key或Index，当Reference被GC后，就可以获得Reference并且通过key或index删除集合的数据。

#一些优化
##分割字符串
JDK8一下的`String.split()`实现使用的是正则，效率比较低
使用indexOf + subString()可以提高效率