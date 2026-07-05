**public static final ExecutorService EXECUTOR = new ThreadPoolExecutor(  
        corePoolSize,  maximumPoolSize,  keepAliveTime,  TimeUnit.MILLISECONDS,  
        new LinkedBlockingDeque<>(10),  
        new ThreadFactory() {  
            int count = 1;  
            @Override  
            public Thread newThread(Runnable r) {  
                return new Thread(r, "thread-" + count++);  
            }  
        },  
        new ThreadPoolExecutor.CallerRunsPolicy());

7大参数：
		 corePoolSize：核心线程数，核心线程处于空闲状态也不会被销毁
		 maximumPoolSize：最大线程数
		 keepAliveTime：线程存活时间，当线程池中的线程数量超过核心线程数时，多余的空闲线程最多能存活多久。
		 timeUnit：时间单位
		 workQueue：工作队列（等待队列）
		 threadFactory：线程工厂，统一管理线程池中的线程创建逻辑，给创建的线程进行自定义，可以直接new，也可以实现threadFactory接口
		 rejectedExecutionHandler：拒绝策略，当线程池无法接收新提交的任务时（线程池满 + 队列满），触发的兜底处理逻辑
			 ![[拒绝策略分类.png|585]]

😀：**Executors.newFixedThreadPool(10)： 能创建出固定包含 10 个线程的线程池pool。
	public static ExecutorService newFixedThreadPool(int nThreads) {  
	    return new ThreadPoolExecutor(nThreads, nThreads,  
                                  0L, TimeUnit.MILLISECONDS,  
                                  new LinkedBlockingQueue<Runnable>());  }
	😡：不安全，任务队列的大小为Integer.MaxSize！还有其他类型的线程池也是一样不安全！
	🤠：开发时一般都是自定义线程池（上面例子），这样更安全！

Callable<T> 进行有参数返回的任务提交
	第一种方式：实现Callable<T>接口，重写call方法
		![[Callable第一种方式.png]]
	第二种方式：直接 new Callable<T>，但是自由度没第一种高
		![[Callable第二种方式.png]]

Future<T> 获取线程submit(Callable<T>)后的返回值，用Future.get()方法获取实际的数据
	 //提交任务到线程池，获取所有Future对象  
	    Future<Integer> submit = EXECUTOR.submit(task);  
	    Integer result = submit.get(); 

线程池用完记得关闭，一般用
	shutdown（）：关闭新任务的接收，但会处理完队列里的任务
或者
	shutdownnow（）：强制关闭任务，拒接新任务，中断旧任务的执行


[代码示例说明如何通过线程池实现有返回值的多线程编程-腾讯云开发者社区-腾讯云](https://cloud.tencent.com/developer/article/2586273)