1. 😍DelayQueue是Java并发包下面的延时队列，对内元素**不到时间，绝不消费**。
2. 💥核心特性：
	1. ✔ **延迟获取**：队列里的元素，时间不到？概不发放！
	2. ✔ **无界队列**：理论上可以无限添加（但小心OOM！）
	3. ✔ **线程安全**：多线程并发操作？稳得一匹！内部实现了线程安全的访问机制。
	4. ✔ **内部排序**：基于优先队列 `PriorityQueue` 实现，确保最快到期的元素在队列头部！
3. 要使用`DelayQueue`，元素必须实现 `Delayed` 接口，该接口继承自 `Comparable` 接口：
	1. 重写两个方法：
		1.  - `getDelay(TimeUnit unit)`：返回剩余的延迟时间，**通过这个方法判断元素是否应该被消费，返回值 <0 则消费，>0则说明还没过期。**
			1. 标准写法：
				1. @Override public long getDelay(TimeUnit unit){ 
					   long diff = startTime - System.currentTimeMillis(); 
					   return unit.convert(diff, TimeUnit.MILLISECONDS); 
				 }
		2.  -`compareTo(Delayed o)`：用于比较两个元素的顺序（DelayQueue 内部使用），谁先到期谁先走
			1. 标准写法：
				1. @Override public int compareTo(Delayed o) { 
					 if (this.startTime < ((DelayedTask) o).startTime) { 
						 return -1; }
					 if (this.startTime > ((DelayedTask) o).startTime) { 
						 return 1; } 
					return 0;
					 }
			2. 时间小 -> 排前面（-1）
			3. 时间大 -> 排后面（1）
5. 💫核心方法：
	1. **boolean add(E e)**：添加元素，等价于 offer( )
	2. **boolean offer(E e)**：添加元素，总是返回 true（无界队列）
	3. **void put(E e)**：添加元素（阻塞直到成功，实际上不会阻塞，因为队列无界）
	4. **E take( )**：获取并**移除队列**头部元素，等待直到有元素延迟时间到期
	5. **E poll( )**：获取并移除队列头部元素，如果没有到期元素则返回 null
	6. **E poll(long timeout, TimeUnit unit)**：获取并移除队列头部元素，必要时指定等待的时间
	7. **E peek( )**：获取但不移除队列头部元素

6. ❗❗❗注意：内存溢出问题，因为队列是无限的