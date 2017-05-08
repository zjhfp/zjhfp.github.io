### CopyOnWrite：

	    CopyOnWrite容器即写时复制的容器。通俗的理解是当我们往一个容器添加元素的时候，不直接往当前容器添加，
  而是先将当前容器进行Copy，复制出一个新的容器，然后新的容器里添加元素，添加完元素之后，再将原容器的引用指向新的容器。
  这样做的好处是我们可以对CopyOnWrite容器进行并发的读，而不需要加锁，因为当前容器不会添加任何元素。
  所以CopyOnWrite容器也是一种读写分离的思想，读和写不同的容器。
### 数据结构：
```
	private transient volatile Object[] array;
```
### 缺点：
1、内存占用：
	因为是通过对容器进行copy操作实现的，所以存在内存消耗的问题（两份），并且每次写操作都会产生一份垃圾，容易造成full GC

## 函数：
	### 1、add
  ```
	public boolean add(E e) {
        final ReentrantLock lock = this.lock;
        lock.lock();
        try {
            Object[] elements = getArray();
            int len = elements.length;
            // copy出一份新的数组，并对新数组进行操作（内存消耗点）
            Object[] newElements = Arrays.copyOf(elements, len + 1);
            newElements[len] = e;
            // 让原数组对象指向新数组实例
            setArray(newElements);
            return true;
        } finally {
            lock.unlock();
        }
    }
   ``` 
