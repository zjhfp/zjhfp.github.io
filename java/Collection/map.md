1、HashMap
	1.7版本：
		table：Entry数组，Entry是一个单向链表，包括了key、value、next、hash
			当出现hash碰撞的时候就会在entry链表上添加数据
		threshold：阈值，用于判断是否需要扩容，=容量*加载因子
		loadFactor：加载因子（默认0.75）
		modCount：用来实现fail-fast(同AarrayList)

		添加键值对过程：
			1、计算key值得hash值
			2、通过indexFor(hash,table.length)计算出数组下标
			3、如果该下标的entry为空则创建entry
				如果size超出了
			4、如果发生hash碰撞则往entry后面挂新的entry
		扩容是在下一次添加键值对的时候完成的

	1.8版本：
	数组+链表+红黑树（JDK1.8增加了红黑树部分）。
	```
		Node<K,V>[] table：存储数据的数组
		Node：实现了Map.Entry的类
			final int hash;    //用来定位数组索引位置
		    final K key;
		    V value;
		    Node<K,V> next;   //链表的下一个node
		TreeNode：继承自LinkedHashMap.Entry(继承自Node)
			红黑树
	```
	发生hash碰撞时，如果节点数小于8使用Node进行存储，大于8使用TreeNode进行存储
	并且在添加的时候就进行扩容
