## HashSet
### 数据结构：
	private transient HashMap<E,Object> map;

	// Dummy value to associate with an Object in the backing Map
    private static final Object PRESENT = new Object();

    public boolean add(E e) {
        return map.put(e, PRESENT)==null;
    }
    
    
## CopyOnWriteArraySet
### 数据结构：
	private final CopyOnWriteArrayList<E> al;

	// 不存在则添加
	public boolean add(E e) {
        return al.addIfAbsent(e);
    }
