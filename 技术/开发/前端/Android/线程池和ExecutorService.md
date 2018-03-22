# 为什么要引入线程池
1. new Thread()的缺点
<pre>
    (1) 每次new Thread()耗费性能；

    (2) 调用new Thread()创建的线程缺乏管理，被称为野线程，而且可以无限制地创建，之间相互竞争，会导致过多地占用系统资源导致系统瘫痪；

    (3) 不利于扩展，比如定时执行、定期执行和线程中断等；
</pre>
2. 线程池的优点
<pre>
    (1) 重用存在的线程，减少对象创建、消亡的开销，性能好；

    (2) 可有效控制最大并发线程数，提高系统资源的使用率，同时避免过多地资源竞争，避免堵塞；

    (3) 提供定时执行、定期执行、单线程和并发数控制等功能；
</pre>

# ExecutorService和Excutors
ExecutorService是一个接口，继承了Executor接口，定义了一些声明周期方法；

Executors工厂类提供了四种线程池，newFixedThreadPool、newCachedThreadPool、newSingleThreadExecutor和newScheduledThreadPool；
<pre>
    (1) newFixedThreadPool创建一个可重用固定线程数的线程池，以共享的无界队列方式来运行这些线程。当固定数量的线程都处于活动状态，再次提交的任务会加入队列等待其他线程运行结束，而当线程处于空闲状态时会被下一个任务复用；

    (2) newCachedThreadPool创建一个可缓存的线程池，如果线程池长度超过处理需要，可灵活回收空闲空间，通常用于执行一些生存期短的异步型任务；

    (3) newScheduledThreadPool创建一个定长线程池，支持定时以及周期性任务执行；

    (4) newSingleThreadExecutor创建一个单线程化的线程池，用唯一的工作线程来执行任务，保证所有任务按照指定顺序(FIFO, LIFO，优先级)执行；
</pre>

# 参考：

1. https://www.cnblogs.com/whoislcj/p/5607734.html

