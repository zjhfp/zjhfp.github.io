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

2、HashTable
	数据结构同hashmap 1.7版本
	线程安全（synchronized）

3、LinkedHashMap
	是HashMap的子类，通过维护一个额外的双向链表保证了迭代顺序
	header：链表头的数据
	accessOrder：标志位，true表示按照访问顺序迭代，false表示按照插入顺序迭代（默认是false）
		在get操作时如果该值为false则不做任何处理，如果该值为true则会将当前访问的对象放到链表的最后
	put操作时，重写了addEntry和createEntry方法，在插入entry后会调用addBefore方法将其链入到双向列表中
	resize过程中重写了transfer方法，重写了get方法通过HashMap的getEntry方法获取entry对象
	通过重写HashMap的迭代器实现了有序性

	在jdk1.8中添加了tail用于存放最后一个元素

4、TreeMap
	comparator：自定义比较器
		如果没有自定义比较器则需要key值对象实现Comparable接口
	root：TreeMap.Entry根节点
		key、value、left、right、parent、color默认BLOCK

	在插入过程中会从根节点开始逐一比较直到找到key值得位置

5、WeakHashMap
	WweakHashMap.Entry继承自WeakReference

6、ConcurrentHashMap
	jdk1.7：
		Segment-->HashEntry table
		首先通过hash获取相应的Segment然后通过hash值获取table中相应的HashEntry然后在该链上插入数据
		数据操作时在segment上加锁
	jdk1.8：
		摒弃了Segment采用数组+链表+红黑树的数据结构
		采用CAS方法支持多线程操作