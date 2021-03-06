

# Jvm垃圾回收



#### 垃圾回收区域

* 方法区
* 堆（主要）
* 程序计数器



#### 垃圾判断方式

* ==程序计数器法==：记录一个对象被引用的次数，次数为0 ，则回收

  * 缺点，无法解决循环应用

    ```java
    Class A{
        
        public object;
        
        public static void main()
        {
            A a =new A();
            A b =new A();
            
            a.object=b;
            b.object=a;
            
            a=null;
            b=null
                //此时，a,b对象计数都为1，但外界已无法访问到了，为垃圾，却无法回收。
        }
    }
    ```

*  ==可达性分析==：这种方法，主要采用GCroot节点，从这个节点出发搜索，形成引用链，若一个对象到Gcroot无应用链，则判断为垃圾

  * Gcroot节点选取：
    * 栈帧上的对象引用所指对象
    * 方法区的 静态，常量对象
    * 本地方法栈的引用对象

  * ==引用链理解==： 引用链上的对象，都是从根节点这个对象的属性里所包含的对象，往下接着搜索

```java
Class A{
    public object;

	public static void main(){
    	A a =new A();
    	A b =new A();
    
    	a.object=b;
    	b.object=a;
    
   		a=null;
    	b=null;
         //此时栈里无对象ab引用，但依然存在a->b,b->a的引用链，但此时ab已经不是gcroot，所以就回收了。
	}   
}
    
```





#### 垃圾回收算法



 **1.标记-清除算法** ：标记要清除对象，然后最后一起清楚，会有内存碎片

 **2.复制算法** ：内存分两块，每次使用一块，一块满了，就把存活的复制到另一块里面，然后再使用空的这块，解决内存碎片，但只能使用一半内存空间

**3.标记-整理算法**：标记清除对象，然后把存活的放另一端，同时清除垃圾

4.分带算法：

* 新生区采用标记-清除（对新生区动手  Minor GC ）
* 幸存者区采用 复制算法
* 老年区，采用标记整理 （对老年区动手叫majarGC）





#### 垃圾回收器

 serial 标记复制

 prenew 多线程版的serial

parallel 注重吞吐量

serial old 老年代

cms 

* 一阶段，首先是短时间的stw ,标记
* 二阶段，并发标记
* 三阶段，再重新标记
* 第四阶段，并发清除
* 缺点，浮动垃圾无法清除 ，标记清除会有碎片

G1 ：

* 使用多核处理器来并发标记，缩短stw ,

* 使用标记复制
* 并且会优先回收最有价值的区域

