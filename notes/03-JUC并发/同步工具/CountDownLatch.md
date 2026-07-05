1. **😀作用**：CountDownLatch又称门闩，它的主要作用就是等待其他线程完成任务，然后对所有任务进行汇总，最后主线程再继续往下执行代码。创建CountDownLatch对象时需要传入一个int类型参数，当这个数减少到0时，主程序才会继续执行代码。
2. **🀄用法**：
	1. countDown（）：用来让计数器 -1 ，一般是执行任务的线程调用
	2. await（）：用来阻塞程序执行，它需要等待计数器的数字减少为0时才会然代码执行，一般是主线程调用。如果多个线程同时执行 await() 方法，那么这几个线程都将处于等待状态，并且以共享模式享有同一个锁。
	3. await（number，TimeUnit）：设置等待时间，如果超出等待时间，主程序直接向下执行，不会因为某个子线程执行太久导致主程序等待时间太久。如果等待到计数器归0，返回值true，反之false
3. **❗❗注意事项**：
	1. *异常处理*：确保是在finally里面进行 countDown（），而不是在try或者catch当中 countDown（）。
	2. *超时控制*：用带过期时间的 await()，避免子线程执行时间过长
	3. *线程池配合*​：与ExecutorService一起使用时，注意正确关闭线程池
	4. *计数器设置*​：初始计数器值应与实际任务数量一致，避免设置过大导致无法唤醒或过小导致提前唤醒
	5. ​*不可重用性*​：CountDownLatch是一次性的，若需重复使用考虑CyclicBarrier
4. *💥使用案例*：[CountDownLatch详解与项目实战CountDownLatch是Java并发编程中的一个重要同步工具类，它允许一 - 掘金](https://juejin.cn/post/7561264740875943999)
	1. **电商订单处理系统**：在电商系统中，处理订单时需要并行执行库存检查、用户验证和支付验证，全部通过后才能完成订单处理。
	2. **游戏服务器启动初始化**：游戏服务器启动时需要并行初始化多个组件（数据库、缓存、网络模块等），全部初始化成功后才能开放服务。
	3. **大数据处理中的分片计算**：在大数据处理场景中，将大数据集分割为多个分片并行处理，最后汇总结果。

**案例示例**：
public class Main {  
    public static void main(String[] args) throws InterruptedException {  
        CountDownLatch latch = new CountDownLatch(3);  
  
        //库存检查  
        new Thread(new Runnable() {  
            @Override  
            public void run() {  
                try {  
                    System.out.println("开始检查库存...");  
                    Thread.sleep(1000);  
                    System.out.println("库存检查完毕！！");  
                } catch (InterruptedException e) {  
                    throw new RuntimeException("库存检查出现异常，请重试");  
                } finally {  
                    latch.countDown();  
                }  
            }  
        }).start();  
  
        //用户验证  
        new Thread(() -> {  
            try {  
                System.out.println("开始用户验证...");  
                Thread.sleep(1000);  
                System.out.println("用户状态验证成功！！");  
            } catch (InterruptedException e) {  
                throw new RuntimeException("用户状态验证失败！");  
            } finally {  
                latch.countDown();  
            }  
        }).start();  
  
        //支付验证  
        new Thread(new Runnable() {  
            @Override  
            public void run() {  
                try {  
                    System.out.println("正在进行支付环节验证");  
                    Thread.sleep(5000);  
                    System.out.println("环境验证成功！！");  
                } catch (InterruptedException e) {  
                    throw new RuntimeException("支付环境验证失败！！");  
                } finally {  
                    latch.countDown();  
                }  
            }  
        }).start();  
  
        //主线程等待业务执行完成  
        System.out.println("等待所有验证完成...");  
        try {  
            boolean await = latch.await(10, TimeUnit.SECONDS);  
            if (await){  
                System.out.println("订单处理完成！！");  
            }else{  
                System.out.println("订单处理失败");  
            }  
        } catch (InterruptedException e) {  
            throw new RuntimeException("处理异常！");  
        }  
    }  
}