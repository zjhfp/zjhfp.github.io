### 数据结构：数组
```
transient Object[] elementData;
 ```
	对数据的操作是通过System.arraycopy进行的
### 函数：add
```
	public boolean add(E e) {
		// 确保数组大小
        ensureCapacityInternal(size + 1);  // Increments modCount!!
        elementData[size++] = e;
        return true;
    }

    private void ensureCapacityInternal(int minCapacity) {
    	// 如果未初始化则默认大小为10
        if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
            minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
        }

        ensureExplicitCapacity(minCapacity);
    }

    private void ensureExplicitCapacity(int minCapacity) {
        modCount++;

        // 如果数组不够用，进行扩容
        if (minCapacity - elementData.length > 0)
            grow(minCapacity);
    }

    private void grow(int minCapacity) {
        // 原数组长度
        int oldCapacity = elementData.length;
        // 新数组长度 = 原长度 + 原长度/2
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        // 新建一个指定长度的数组，将原数组拷贝至新数组（此处为扩容的消耗点）
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
```
