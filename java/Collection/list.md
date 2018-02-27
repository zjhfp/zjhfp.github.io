### 1、AarrayList
	数据结构：数组(随机插入慢，随机读取快)
	非线程安全（Vector线程安全：函数使用synchronized加锁）
	通过System.arraycopy的方式进行扩容和列表数据移除、指定位置添加数据等操作
	当使用Iterator进行列表遍历操作期间如果修改了列表会抛出ConcurrentCodificationException
		原理：Arraylist每次对列表进行写操作（除了set操作，指定位置set）时会将修改次数modCount加一
		Arraylist的Itr实现了Iterator接口在next函数和remove函数中会判断modCount是否发生变化
		如果发生了变化则抛出异常
		强制for循环for(String str : list)
		和iterator循环Iterator i = list.iterator();while(i.hasNext())会导致该异常
		下标循环则不会导致该异常

### 2、Stack继承自Vector出
	先进后
	增加了push（往数组最后添加数据）、pop（弹出最后一个数据）、peek（返回最后一个数据）

### 3、LinkedList
	链表Node（item,prev,next），先进先出（随机插入块，随机读取慢）
	非线程安全

### 4、CopyOnWwriteArrayList
	数据结构：数组
	线程安全：采用ReentrantLock枷锁
	在写操作时先加锁再将原数组拷贝一份，操作完成以后用新数组覆盖原数组

### Collections中的列表
	Collections.CheckedList：执行添加操作之前会先检查数据类型是否正确
	Collections.SynchronizedList：使用synchronized（this）加锁
	Collections.UnmodifiableList：重写了所有写操作（直接抛出异常）