1. CyclicBarrier和CountDownLatch相似，也可以实现线程的相互等待，当所有线程到达屏障之后再执行逻辑，**只是他的计数器可以重置，适合更复杂的业务代码**
2. 两个构造方法：
	1. ![[CyclicBarrier构造方法.png]]
	2. 当所有线程到达屏障后，会先执行 barrierAction ，由最后一个线程到达触发执行
3. 核心方法：
	1. **await( )**：线程调用此方法后会进入等待其他线程的状态，直到所有线程到达
	2. **await(long timeout, TimeUnit unit)**：带超时时间的等待，超时会触发 TimeOutException，屏障会被打破
	3. **reset( )**：重置屏障到初始状态，如果有线程在等待，则会抛出BrokenBarrierException
	4. **isBroken( )**：判断屏障是否被打破（如超时、线程中断会导致屏障破碎）
	5. getNumberWaiting( )：获取当前正在等待的线程数
	6. getParties( )：获取需要到达屏障的线程总数
4. 应用场景：
	1.  CountDownLatch差不多，但是可以做的比它更复杂，例如循环触发业务
5. **示例**
public class Main {  
    public static void main(String[] args) {  
        CyclicBarrier barrier = new CyclicBarrier(5, new Runnable() {  
            @Override  
            public void run() {  
                System.out.println("====所有人已到达，开始闯关！====");  
            }  
        });  

        for (int i = 0; i < 5; i++) {  
            new Thread(new Runnable() {  
                @Override  
                public void run() {  
                    for (int i = 0; i < 2; i++) {  
                        try {  
                            System.out.println(Thread.currentThread().getName() + " 已到达关卡 " + (i + 1));  
                            barrier.await();  
                            System.out.println(Thread.currentThread().getName() + "开始闯关！");  
                        } catch (InterruptedException | BrokenBarrierException e) {  
                            throw new RuntimeException(e);  
                        }  
                    }  
                }  
            }).start();  
        }  }  }

