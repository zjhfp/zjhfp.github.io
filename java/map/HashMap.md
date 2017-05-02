以下内容基于JAVA8

HashMap是基于哈希表的map实现，以键值对的形式存在

### 数据结构
HashMap 数组+链表+红黑树（JDK1.8增加了红黑树部分）。
```
Node<K,V>[] table：存储数据的数组
	Node：实现了Map.Entry的类
		final int hash;    //用来定位数组索引位置
	    final K key;
	    V value;
	    Node<K,V> next;   //链表的下一个node
```
索引计算：数组长度-1 & 对key值进行hash算法后得到的值
当数据发生hash碰撞时会在当前节点上往后挂新的数据（e.next=newNode）,当链表数据大于8（默认大小）时采用红黑树代替链表

构造函数：

	public HashMap(int initialCapacity, float loadFactor)
	initialCapacity：初始容量（默认16，最大1<<30）
	loadFactor：加载因子（默认0.75f）

函数：
	1、hash
	
		static final int hash(Object key) {
	        int h;
	        return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
	    }
	    ^：按位亦或操作符（只有在两个比较的位不同时其结果是1，否则结果为0）
	    >>>：无符号右移
	    对于null值返回值为0，
	    高位运算的算法：h ^ (h >>> 16) 高16位与底16位进行亦或运算
	    这么做可以在数组table的length比较小的时候，也能保证考虑到高低Bit都参与到Hash的计算中，同时不会有太大的开销。
	2、put
	
	    public V put(K key, V value) {
			//对key的hashCode做hash操作
	        return putVal(hash(key), key, value, false, true);
	    }

	    final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
	                   boolean evict) {
	        Node<K,V>[] tab; Node<K,V> p; int n, i;
	        // 1）判断键值对数组table是否为空或为null，否则执行resize()进行扩容；
	        if ((tab = table) == null || (n = tab.length) == 0)
	            n = (tab = resize()).length;
	        // 2）根据键值key计算hash值得到插入的数组索引i，如果table[i]==null，直接新建节点添加，转向（6），如果table[i]不为空，转向（3）；
	        if ((p = tab[i = (n - 1) & hash]) == null)
	            tab[i] = newNode(hash, key, value, null);
	        else {
	            Node<K,V> e; K k;
	            // 3）判断table[i]的首个元素是否和key一样，如果相同直接覆盖value，否则转向（4），这里的相同指的是hashCode以及equals；
	            if (p.hash == hash &&
	                ((k = p.key) == key || (key != null && key.equals(k))))
	                e = p;
	            // 4）判断table[i] 是否为treeNode，即table[i] 是否是红黑树，如果是红黑树，则直接在树中插入键值对，否则转向（5）；
	            else if (p instanceof TreeNode)
	                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
	            else {
	            	// 5）遍历table[i]，判断链表长度是否大于8，大于8的话把链表转换为红黑树，在红黑树中执行插入操作，否则进行链表的插入操作；遍历过程中若发现key已经存在直接覆盖value即可；
	                for (int binCount = 0; ; ++binCount) {
	                    if ((e = p.next) == null) {
	                        p.next = newNode(hash, key, value, null);
	                        // 链表长度大于8转换为红黑树进行处理
	                        if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
	                            treeifyBin(tab, hash);
	                        break;
	                    }
	                    // key已经存在直接覆盖value
	                    if (e.hash == hash &&
	                        ((k = e.key) == key || (key != null && key.equals(k))))
	                        break;
	                    p = e;
	                }
	            }
	            if (e != null) { // existing mapping for key
	                V oldValue = e.value;
	                if (!onlyIfAbsent || oldValue == null)
	                    e.value = value;
	                afterNodeAccess(e);
	                return oldValue;
	            }
	        }
	        ++modCount;
	        // 6）插入成功后，判断实际存在的键值对数量size是否超多了最大容量threshold，如果超过，进行扩容。
	        if (++size > threshold)
	            resize();
	        afterNodeInsertion(evict);
	        return null;
	    }
	3、resize（扩容）
	
		final Node<K,V>[] resize() {
	        Node<K,V>[] oldTab = table;
	        int oldCap = (oldTab == null) ? 0 : oldTab.length;
	        int oldThr = threshold;
	        int newCap, newThr = 0;
	        if (oldCap > 0) {
	        	// 1）如果之前的容量已经大于等于最大容量2^30则会将容量设置成2^31-1，之后将不在扩容
	            if (oldCap >= MAXIMUM_CAPACITY) {
	                threshold = Integer.MAX_VALUE;
	                return oldTab;
	            }
	            // 2）否则设置新的容量为之前容量的两倍
	            else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
	                     oldCap >= DEFAULT_INITIAL_CAPACITY)
	                newThr = oldThr << 1; // double threshold
	        }
	        else if (oldThr > 0) // initial capacity was placed in threshold
	            newCap = oldThr;
	        else {               // zero initial threshold signifies using defaults
	            newCap = DEFAULT_INITIAL_CAPACITY;
	            newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
	        }
	        if (newThr == 0) {
	            float ft = (float)newCap * loadFactor;
	            newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
	                      (int)ft : Integer.MAX_VALUE);
	        }
	        threshold = newThr;
	        // 创建新的数组
	        @SuppressWarnings({"rawtypes","unchecked"})
	            Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
	        table = newTab;
	        if (oldTab != null) {
	            for (int j = 0; j < oldCap; ++j) {
	                Node<K,V> e;
	                if ((e = oldTab[j]) != null) {
	                	// 首先将之前该位置的的数组置空
	                    oldTab[j] = null;
	                    // 如果这个位置没有发生hash碰撞，则直接将该值设置到新的数组上
	                    if (e.next == null)
	                        newTab[e.hash & (newCap - 1)] = e;
	                    // 如果这个位置是一颗红黑树，则重新处理该红黑树
	                    else if (e instanceof TreeNode)
	                        ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
	                    // 如果这个位置是链表，则处理链表
	                    // e.hash & cap-1为下标算法，e.hash不变
	                    // cap-1的二进制数值为之前值高位加1,所以新的下标值可以根据新增高位的值获取
	                    // 如果该值为0，则下标不变，如果该值为1则下标为oldIndex+oldCap
	                    // 也就是说之前的链表最多会分成两个链表，一个还是在原位置，一个在原位置加扩容的大小
	                    else { // preserve order
	                        Node<K,V> loHead = null, loTail = null;
	                        Node<K,V> hiHead = null, hiTail = null;
	                        Node<K,V> next;
	                        do {
	                            next = e.next;
	                            if ((e.hash & oldCap) == 0) {
	                                if (loTail == null)
	                                    loHead = e;
	                                else
	                                    loTail.next = e;
	                                loTail = e;
	                            }
	                            else {
	                                if (hiTail == null)
	                                    hiHead = e;
	                                else
	                                    hiTail.next = e;
	                                hiTail = e;
	                            }
	                        } while ((e = next) != null);
	                        if (loTail != null) {
	                            loTail.next = null;
	                            newTab[j] = loHead;
	                        }
	                        if (hiTail != null) {
	                            hiTail.next = null;
	                            newTab[j + oldCap] = hiHead;
	                        }
	                    }
	                }
	            }
	        }
	        return newTab;
	    }
	4、keySet/valueSet
		通过暴露HashMap的内部类KeySet并重写iterator方法，该方法返回一个自定义的Iterator对象KeyIterator（同样为HashMap的内部类），
		并重写Iterator中的方法直接访问HashMap中的table实现数据的可见性
