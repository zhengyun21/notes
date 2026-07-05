1. Semaphore本质上就是一个信号计数器，用于限制同一时间的最大访问数量
2. 构造函数：
	1. `Semaphore(int permits)`: 创建具有指定许可证数量的 Semaphore，非公平模式。
	2. `Semaphore(int permits, boolean fair)`: 第二个参数为布尔值，指示是否使用公平模式。
		1. **公平模式就是调用 acquire 的顺序就是获取许可证的顺序，遵循 FIFO。 非公平模式是抢占式的，也就是有可能一个新的获取线程恰好在一个许可证释放时得到了这个许可证，而前面还有等待的线程，就是不排队。**
3. 常用方法：
	1. `void acquire(int permits) throws InterruptedException`: 获取一个许可证，若无可用许可证，则线程进入等待，阻塞
		1. 这个 permits可有可无，表示每当acquire时，将会占领多少个锁。
	2. `void release(int permits)`: 归还一个许可证。
		1.  这个 permits可有可无，表示每当release时，将会释放多少个锁。
	3. `int availablePermits()`: 返回当前可用的许可证数量。
	4. `boolean tryAcquire()`: 尝试获取一个许可证，若获取成功则返回 true，否则返回 false。
4. 最佳实践：
	1. **选择合适的公平策略**：非公平的 Semaphore 通常具有更高的吞吐量，但公平策略能避免线程饥饿，需要根据具体需求选择。
	2. **确保许可证的释放**：在 finally 块中释放许可证，以确保即使发生异常也能正确释放。
	3. **避免过多等待线程**：许可证过少可能导致大量线程长期等待，降低系统性能，需要合理设置初始许可证数量。
5. 例子：
	1. 