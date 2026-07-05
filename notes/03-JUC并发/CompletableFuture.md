👍CompletableFuture：是一个结果容器，通过调用获取参数的方法执行回调函数来获取参数值
1. Future 的升级版本
	1. **主动完成**：可以手动设置任务结果
	2. **函数式编程**：支持链式调用和函数组合
	3. **异常处理**：提供完善的异常处理机制
	4. **任务组合**：支持多个异步任务的组合操作
	5. **非阻塞**：提供非阻塞的结果获取方式
2. 常用方法
	1. supplyAsync：创建带有返回值的异步任务
		1. public static <U> CompletableFuture<U> supplyAsync(Supplier<U> supplier)
				1. 使用默认线程池（ForkJoinPool.commonPool( )) 
		   2.  public static <U> CompletableFuture<U> supplyAsync(Supplier<U> supplier, Executor executor)
				1. 可以使用自定义线程池
	   2. runAsync：创建没有返回值的异步任务
			1. public static CompletableFuture<Void> runAsync(Runnable runnable)
				1. 使用默认线程池.
			2. public static CompletableFuture<Void> runAsync(Runnable runnable, Executor executor)
				1. 使用自定义线程池.
3. 获取任务结果方法
	1. get( )
	2. get(long timeOut，TimeUnit unit)：最大时间等待结果
	3. join( )：可以获取带返回值的CompletableFuture
	4. getNow(Integer valueIfAbsent)：立即获取，没完成返回valueIfAbsent
4. 回调函数：不阻塞，异步串联
	1. thenRun / thenRunAsync：
		1. 无入参、无返回值，仅执行后续逻辑
	2. thenAccept / thenAcceptAsync：
		1. 接收上一个任务的结果，无返回值
	3. thenApply / thenApplyAsync：
		1. 接收上一个任务的结果，有返回值（最常用）
5. 异常处理
	1. exceptionally
		1. 捕获异常，返回默认值
	2. handle / handleAsync
		1. 无论成功/异常，都会执行（全能处理）
6. 多任务组合
	1. thenCompose
		1. 串行执行：第一个任务结果 → 作为第二个任务入参
			1. 调用函数get（）后才会触发里面的代码，不调用不触发
	2. thenCombine
		1. 并行执行：两个任务都完成后，合并结果
	3. allof / anyof
		1. allOf`：所有任务都完成才执行后续
		2. anyOf：任意一个任务完成就执行后续
			1. // 等待所有任务完成 
				1. CompletableFuture.allOf(future1, future2, future3).join(); 
			2. // 等待任意一个任务完成                            CompletableFuture<Object>anyFuture=CompletableFuture.anyOf(future1, future2);