### 锁的分类

![img](C:\Users\ZHW\Desktop\笔记\IMG\watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2F4aWFvYm9nZQ==,size_16,color_FFFFFF,t_70)

### JVM锁优化

​	偏向锁，==当出现其他线程开始竞争锁时==，偏向锁就会转换为轻量级锁，这时候线程就会尝试执行CAS，==若自旋次数超过一定数量==，就会再次触发锁升级，升级为重量级锁。未获取锁的线程将进入阻塞状态，



### CAS

* CAS 即比较并替换，实现并发算法时常用到的一种技术。CAS操作包含三个操作数——内存位置、预期原值及新值。执行CAS操作的时候，将内存位置的值与预期原值比较，如果相匹配，那么处理器会自动将该位置值更新为新值，否则，处理器不做任何操作。

* ```java
   public final int getAndAddInt(Object var1, long var2, int var4) {
          int var5;
          do {
              var5 = this.getIntVolatile(var1, var2);
          } while(!this.compareAndSwapInt(var1, var2, var5, var5 + var4));
    
          return var5;
      }
  ```
  
* compareAndSwapInt(var1, var2, var5, var5 + var4)也就是具体的cas，var1是这个对象，var2地址偏移，var5，预期值，修改后的值

  

### volatile

volatile在多处理器开发中保证了共享变量的“ 可见性”。可见性的意思是当一个线程修改一个共享变量时，另外一个线程能读到这个修改的值。(共享内存，私有内存)

aqs



#### synchronized原理

* 是靠jvm实现的锁
* 在需要线程同步的代码块，在字节码里面会有两条指令**monitorenter**，**monitorexit**，代表执行这个命令会尝试获得**monitor**控制权，获得了就代表获得了这把锁
* **Monitor对象存在于每个Java对象的对象头Mark Word中地址所指的地方**，每个对象都带一把锁
* synchronized在JVM里的实现都是 基于进入和退出Monitor对象来实现方法同步和代码块同步，进入的话，就把**owner**置为当前线程，count加1

```java
ObjectMonitor() {
    _header       = NULL;
    _count        = 0; // 记录个数
    _waiters      = 0,
    _recursions   = 0;
    _object       = NULL;
    _owner        = NULL;
    _WaitSet      = NULL; // 处于wait状态的线程，会被加入到_WaitSet
    _WaitSetLock  = 0 ;
    _Responsible  = NULL ;
    _succ         = NULL ;
    _cxq          = NULL ;
    FreeNext      = NULL ;
    _EntryList    = NULL ; // 处于等待锁block状态的线程，会被加入到该列表
    _SpinFreq     = 0 ;
    _SpinClock    = 0 ;
    OwnerIsThread = 0 ;
  }
```

