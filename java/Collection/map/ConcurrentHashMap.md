以下内容基于JAVA8

## ConcurrentHashMap：数组+链表+红黑树（JDK1.8增加了红黑树部分）

### 通过Node进行节点划分实现分段锁，在进行写操作时采用synchronized对节点进行加锁
### 索引计算：数组长度-1 & 对key值进行hash算法后得到的值

	private transient volatile int sizeCtl;
		-1代表正在初始化
		-N 表示有N-1个线程正在进行扩容操作
		0代表hash表还没有被初始化
	static final int MOVED     = -1; // hash值是-1，表示这是一个forwardNode节点
    	static final int TREEBIN   = -2; // hash值是-2  表示这时一个TreeBin节点
	使用treeBin包装treeNode实现红黑树的操作

	size值是一个估值，并非准确的真实值

### 确保原子性操作的三个函数
	// ASHIFT、ABASE为常量
	// 向table上put数据，使用Volatile语义确保数据在修改后能被其他线程获取
	static final <K,V> void setTabAt(Node<K,V>[] tab, int i, Node<K,V> v) {
        U.putObjectVolatile(tab, ((long)i << ASHIFT) + ABASE, v);
    }

    // cas操作，确保线程安全
    static final <K,V> boolean casTabAt(Node<K,V>[] tab, int i,
                                        Node<K,V> c, Node<K,V> v) {
        return U.compareAndSwapObject(tab, ((long)i << ASHIFT) + ABASE, c, v);
    }

    // 确保获取最新的数据（Volatile）
	static final <K,V> Node<K,V> tabAt(Node<K,V>[] tab, int i) {
        return (Node<K,V>)U.getObjectVolatile(tab, ((long)i << ASHIFT) + ABASE);
    }
