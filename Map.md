#### HashMap

##### 1.==HashMap细节==

* HashMap实现了Map接口，存键值对，键不可重复
* put(key ,value)，先用hashcode算出key的code，然后填入数组里，数组冲突了，就加入链表，**如果key已经存在，则用新的value值，替换点旧的value值**
* get()，会通过键的hashcode找到数组的位置，然后调用equals来对比，对象是否相同
* 数组里存的不是value，是==键+值==。



##### 2.==hashMap数据结构==

* 数组是entry数组

* 数组+链表 +红黑树（jdk1.8以后，之前只是数组+链表）

##### 3.hashmap.put()

* put(key ,value)，先用hash()算出key的hashcode

* 然后再通过hash函数**==hash()==**得到hashcode，然后通过indexFor（），找到下标，填入数组里，数组冲突了，就加入链表，**如果key已经存在，则用新的value值，替换点旧的value值**

* ```java
  public V put(K key, V value) {
          //如果table数组为空数组{}，进行数组填充（为table分配实际内存空间），入参为threshold，
          //此时threshold为initialCapacity 默认是1<<4(24=16)
          if (table == EMPTY_TABLE) {
              inflateTable(threshold);
          }
         //如果key为null，存储位置为table[0]或table[0]的冲突链上
          if (key == null)
              return putForNullKey(value);
          int hash = hash(key);//对key的hashcode进一步计算，确保散列均匀
          int i = indexFor(hash, table.length);//获取在table中的实际位置
          for (Entry<K,V> e = table[i]; e != null; e = e.next) {
          //如果该对应数据已存在，执行覆盖操作。用新value替换旧value，并返回旧value
              Object k;
              if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {
                  V oldValue = e.value;
                  e.value = value;
                  e.recordAccess(this);
                  return oldValue;
              }
          }
          modCount++;//保证并发访问时，若HashMap内部结构发生变化，快速响应失败
          addEntry(hash, key, value, i);//新增一个entry
          return null;
      }
  ```

* 初始化hashmap数组长度时，都是2的几次幂，这么做是为了，扩容后数组一些位置不会浪费了

* indexFor()

  * ```JAVA
      static int indexFor(int h, int length) {
            return h & (length-1);
        }
    ```

    

##### 4.hashmap.resize()

* 数组默认长度为16，当达到扩容因子0.75，也就是填充的键值对个数达到了数组长度*扩充因子时，就会扩容，采取扩充机制，变为两倍

* 创建新的数组，然后把旧的数组，从头遍历，在每个节点，还要顺着链表遍历，使用indexfor得到新的下标，使用头插法，插入新数组

* ```JAVA
  void transfer(Entry[] newTable, boolean rehash) {
          int newCapacity = newTable.length;
  　　　　　//for循环中的代码，逐个遍历链表，重新计算索引位置，将老数组数据复制到新数组中去（数组不存储实际数据，所以仅仅是拷贝引用而已）
          for (Entry<K,V> e : table) {
              while(null != e) {
                  Entry<K,V> next = e.next;
                  if (rehash) {
                      e.hash = null == e.key ? 0 : hash(e.key);
                  }
                  int i = indexFor(e.hash, newCapacity);
                  //将当前entry的next链指向新的索引位置,newTable[i]有可能为空，有可能也是个entry链，如果是entry链，直接在链表头部插入。
                  e.next = newTable[i];
                  newTable[i] = e;
                  e = next;
              }
          }
      }
  ```

* ==hashmap不安全表现==：resize上，扩容上（一个线程完成了扩容，而另外一个没有完成扩容）。



##### hashmap.get()

* get方法通过key值返回对应value，如果key为null，直接去table[0]处检索。

* 计算hashcode，找到下标，比对key值，相同再取出，比较相同的的方式有== 或者equals，二者其一为真，表示相同

* ```java
  final Entry<K,V> getEntry(Object key) {
              
          if (size == 0) {
              return null;
          }
          //通过key的hashcode值计算hash值
          int hash = (key == null) ? 0 : hash(key);
          //indexFor (hash&length-1) 获取最终数组索引，然后遍历链表，通过equals方法比对找出对应记录
          for (Entry<K,V> e = table[indexFor(hash, table.length)];
               e != null;
               e = e.next) {
              Object k;
              if (e.hash == hash && 
                  ((k = e.key) == key || (key != null && key.equals(k))))//比较相同
                  return e;
          }
          return null;
      }    
  ```



#### ConcurrentHashMap

* Java7 中 ConcurrentHashMap 使用的分段锁，也就是每一个 Segment 上同时只有一个线程可以操作，每一个 Segment 都是一个类似 HashMap 数组的结构，它可以扩容，它的冲突会转化为链表。但是 Segment 的个数一但初始化就不能改变。
  * put操作
    * 先根据hash值获取segement，若segement没有初始化就初始化
    * 执行segement的put
    * trylock，尝试获得这个锁
    * 计算 put 的数据要放入的 index 位置，然后获取这个位置上的 HashEntry 。如果没这个hashEntry就头插，如果有就替换新的值，然后需要扩容再扩容
* Java8 中的 ConcurrentHashMap 使用的 Synchronized 锁加 CAS 的机制。结构也由 Java7 中的 **Segment 数组 + HashEntry 数组 + 链表** 进化成了 **Node 数组 + 链表 / 红黑树**，Node 是类似于一个 HashEntry 的结构。它的冲突再达到一定大小时会转化成红黑树，在冲突小于一定数量时又退回链表。
  * 同步的方法是在put操作时，找到key的数组位置，如果是null的话，会采取cas，cas成功就更新
  * 还有一个是如果要扩容了，就先扩容
  * 除了以上情况，就给 Synchronized给找到的数组元素加锁，然后加入或覆盖节点。



#### LinkedHashMap

* 是hashmap与双向链表的结合体，最大的用处是来实现LRU算法（最近最久未使用算法）
* LHM的entry这个数据结构增加了Befor和After这两指针，用来实现双向链表，链表有两种顺序，一是按==插入顺序==，二是按==访问顺序==（最近访问放末尾），LHM使用accessOrder标志位来决定使用哪种，true为访问顺序，false为插入顺序，
* 在put元素时，首先会像hashmap一样插入数组，然后再链接到链表末尾
* 在get元素时，