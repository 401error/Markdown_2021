# JAVA线程



#### 线程创建方法



1.继承Tread类

* 自定义类继承Tread类
* 重写run()方法
* 开启线程，要用start()

2.实现Runnable接口

* 自定义类扩展Runnable
* 重写run()方法
* 创建Tread对象，并丢入实现了Runnable接口的对象

```java
Class A implement Runnabe{
    
    void run()
    {
        ......
    }
    
    public static void main(){
        
        A a =new A()；
        Tread b =new Tread(a);
        b.start();
    }
}
```

* 此方法也可以直接使用lamda表达式来写run方法，不需要再拿一个类来实现Runnable，函数式接口



#### 线程停止

* 最好的方法是，让线程自己停止，在实现类里，设置一个标志位，改变标志位，就让线程里的方法结束。



#### 线程休眠

* Tread.sleep(.....)，这个方法阻塞当前线程，时间写在括号里





#### 线程礼让

* Tread.yield()
*  礼让不一定成功00，只是让当前线程回到就绪态 ，然后cpu重新调度



#### 线程强制执行

* Tread.join()
*  强制执行当前线程，直到这个线程结束，其他线程都得阻塞等待。



#### 线程状态

* Tread.getState;



#### 线程的同步

* 使用同步方法synchronized，标在方法上
* 使用同步代码块，synchronized（object）{},锁某一个对象
* Reentrantlock



#### 线程通讯

* Tread.wait()//线程等待，并释放锁
* Tread.notify()//唤醒在等待的线程。



#### 线程池

* 线程容器，可以使得线程复用，减少线程创建的开销

  

  |      Executor      | 线程池顶级接口。                                 |
  | :----------------: | ------------------------------------------------ |
  |  ExecutorService   | 继承Executor，包含更多方法，例如，线程提交，关闭 |
  | ThreadPoolExecutor | ExecutorService的默认实现。构造函数复杂          |



* 线程池的主要实现类为**ThreadPoolExecutor**
* 核心参数有：
  * corePoolSize：指定了线程池中的线程数量
  * maximumPoolSize：指定了线程池中的最大线程数量
  * keepAliveTime：线程池维护线程所允许的空闲时间
  * unit: keepAliveTime 的单位。
  * workQueue：任务队列，被提交但尚未被执行的任务。
  * threadFactory：线程工厂，用于创建线程，一般用默认的即可。
  * handler：拒绝策略。当任务太多来不及处理，如何拒绝任务。

* 因为创建需要参数很复杂，所以java提供了工厂类**Executors**，能够方便的创建四种线程池

  | SingleThreadExecutor | 创建一个==单线程==的线程池。这个线程池只有一个线程在工作，也就是相当于单线程串行执行所有任务。如果这个唯一的线程因为异常结束，那么会有一个新的线程来替代它。此线程池保证所有任务的执行顺序按照任务的提交顺序执行。 |
  | -------------------- | ------------------------------------------------------------ |
  | FixedThreadPool      | 创建==固定大小==的线程池。每次提交一个任务就创建一个线程，直到线程达到线程池的最大大小。线程池的大小一旦达到最大值就会保持不变，如果某个线程因为执行异常而结束，那么线程池会补充一个新线程。 |
  | CachedThreadPool     | 创建一个可==缓存==的线程池。如果线程池的大小超过了处理任务所需要的线程，那么就会回收部分空闲（60秒不执行任务）的线程，当任务数增加时，此线程池又可以[智能](http://lib.csdn.net/base/aiplanning)的添加新线程来处理任务。此线程池不会对线程池大小做限制，线程池大小完全依赖于[操作系统](http://lib.csdn.net/base/operatingsystem)（或者说JVM）能够创建的最大线程大小。 |
  | ScheduledThreadPool  | 创建一个大小无限的线程池。此线程池支持定时以及周期性执行任务的需求 |

  

* 执行 execute()方法和 submit()方法的区别是什么呢？
* **`execute()`方法用于提交不需要返回值的任务，所以无法判断任务是否被线程池执行成功与否；**
* **`submit()`方法用于提交需要返回值的任务。线程池会返回一个 `Future` 类型的对象，通过这个 `Future` 对象可以判断任务是否执行成功**，并且可以通过 `Future` 的 `get()`方法来获取返回值，`get()`方法会阻塞当前线程直到任务完成，而使用 `get（long timeout，TimeUnit unit）`方法则会阻塞当前线程一段时间后立即返回，这时候有可能任务没有执行完。



```java
  public static void main(String[] args) {
        // TODO Auto-generated method stub
 
        //创建一个可重用固定线程数的线程池
        ExecutorService pool=Executors.newSingleThreadExecutor();
        
        //创建实现了Runnable接口对象，Thread对象当然也实现了Runnable接口;
        
        
        Thread t1=new MyThread();
        
        Thread t2=new MyThread();
        
        Thread t3=new MyThread();
        
        Thread t4=new MyThread();
        
        Thread t5=new MyThread();
        
        //将线程放到池中执行；
        
        pool.execute(t1);

        pool.execute(t2);

        pool.execute(t3);

        pool.execute(t4);

        pool.execute(t5);
        
        //关闭线程池
        
       pool.shutdown();
```



线程池拒绝策略（线程池满了）

* 丢弃，抛异常。
* 丢弃，不跑异常。

* 丢弃队列最前面的任务，然后重新提交被拒绝的任务
* 提交任务的线程直接执行被丢弃的任务的。