1. **`executor.submit(Callable<T>)`**：undefined提交有返回值的任务，返回 `Future<T>` 对象。线程池会分配线程执行 `call()` 方法。
2. **`Future<T>.get()`**：
    - 阻塞当前线程，直到任务执行完毕并返回结果。
    - 若任务抛出异常，`get()` 会将异常封装为 `ExecutionException` 抛出。
    - 可使用 `get(long timeout, TimeUnit unit)` 设置超时时间，避免永久阻塞。
3. **`Future<T>.isDone()`**：undefined判断任务是否执行完毕（返回 `true` 表示已完成，包括正常结束、异常终止、被取消）。
4. **`Future<T>.cancel(boolean mayInterruptIfRunning)`**：undefined取消任务执行：
    - 若任务未开始，直接取消；
    - 若任务已开始，`mayInterruptIfRunning` 为 `true` 则中断任务，否则允许任务完成。