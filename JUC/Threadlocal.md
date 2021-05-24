### ThreadLocal

#### 理解

* 保证多线程安全的另外一种机制
* 对于一个共享变量，每个线程都复制一份，每个线程有这个变量的副本（==在源码中，可以看到Thread类里有**`ThreadLocalMap`**这个属性，它就是用来保存副本的==）





#### 实现原理

* 首先Tread类

  * ```java
    public class Thread implements Runnable {
     ......
    //与此线程有关的ThreadLocal值。由ThreadLocal类维护
    ThreadLocal.ThreadLocalMap threadLocals = null;
    
    //与此线程有关的InheritableThreadLocal值。由InheritableThreadLocal类维护
    ThreadLocal.ThreadLocalMap inheritableThreadLocals = null;
     ......
    }
    ```

  * 可以看到真正实现是使用**ThreadLocalMap**，并且它的创建是通过Threadlocal的set,get方法实现的

  * ThreadLocalMap本身是个map，key是Threadlocal，value是是存的变量。

* 其次Treadlocal

  * ```
        public void set(T value) {
            Thread t = Thread.currentThread();//
            ThreadLocalMap map = getMap(t);
            if (map != null)
                map.set(this, value);
            else
                createMap(t, value);
        }
        ThreadLocalMap getMap(Thread t) {
            return t.threadLocals;
        
        }
    ```

  *  当你执行set时，先获取当前操作的线程，然后拿到该线程的ThreadlocalMap，然后插入键值对
  * **最终的变量是放在了当前线程的 `ThreadLocalMap` 中，并不是存在 `ThreadLocal` 上，`ThreadLocal` 可以理解为只是`ThreadLocalMap`的封装，传递需要共享的变量值**

