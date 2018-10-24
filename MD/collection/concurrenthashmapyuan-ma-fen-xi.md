## 一、什么是ConcurrentHashMap 

1. ConcurrentHashMap基于双数组和链表的Map接口的同步实现
2. ConcurrentHashMap中元素的key是唯一的、value值可重复
3. ConcurrentHashMap不允许使用null值和null键
4. ConcurrentHashMap是无序的

## 二、为什么使用ConcurrentHashMap 

我们在之前的博文中了解到关于HashMap和Hashtable这两种集合，HashMap是非线程安全的，当我们只有一个线程在使用HashMap的时候，自然不会有问题，但如果涉及到多个线程，并且有读有写的过程中，HashMap就会fail-fast。要解决HashMap同步的问题，我们的解决方案有

1. Hashtable
2. Collections.synchronizedMap\(hashMap\)

这两种方式基本都是对整个hash表结构加上同步锁，这样在锁表的期间，别的线程就需要等待了，无疑性能不高，所以我们引入ConcurrentHashMap，既能同步又能多线程访问

## 三、ConcurrentHashMap的数据结构 

ConcurrentHashMap的数据结构为一个Segment数组，Segment的数据结构为HashEntry的数组，而HashEntry存的是我们的键值对，可以构成链表。可以简单的理解为数组里装的是HashMap

![](https://github.com/M78Snail/JavaReview/blob/master/MD/collection/assets/import_conhashmap.png)

从上面的结构我们可以了解到，ConcurrentHashMap定位一个元素的过程需要进行两次Hash操作，第一次Hash定位到Segment，第二次Hash定位到元素所在的链表的头部，因此，这一种结构的带来的副作用是Hash的过程要比普通的HashMap要长，但是带来的好处是写操作的时候可以只对元素所在的Segment进行加锁即可，不会影响到其他的Segment。正是因为其内部的结构以及机制，ConcurrentHashMap在并发访问的性能上要比Hashtable和同步包装之后的HashMap的性能提高很多。在理想状态下，ConcurrentHashMap 可以支持 16 个线程执行并发写操作（如果并发级别设置为 16），及任意数量线程的读操作

## 四、ConcurrentHashMap的成员变量

ConcurrentHashMap的成员变量有

1. Segment的数组（final Segment&lt;K,V&gt;\[\] segments），Segment 是ConcurrentHashMap的内部类
2. Segment 类中，包含了一个HashEntry的数组（transient volatile HashEntry&lt;K,V&gt;\[\] table）， HashEntry也是ConcurrentHashMap的内部类
3. HashEntry中，包含key和value以及next指针（类似于HashMap中 Entry），所以HashEntry可以构成一个链表

① Segment类继承于ReentrantLock类，使得Segment对象可以充当锁的角色

```
static final class Segment<K,V> extends ReentrantLock implements Serializable {
  
    transient volatile HashEntry<K,V>[] table;
    transient int count;
    transient int modCount;
    transient int threshold;
   
    final float loadFactor;
}
```

Segment的成员变量意义

1. table：链表数组，数组中的每个元素代表链表的头部
2. count：Segment中元素的数量
3. modCount：对table大小造成影响的操作数量（比如put和remove）
4. threshold：阈值，Segment里面元素超过这个值就会对Segment进行扩容
5. loadFactor：负载因子，用于确定threshold

② HashEntry类

```
static final class HashEntry<K,V> {
    final int hash;
    final K key;
    volatile V value;
    final HashEntry<K,V> next;
    HashEntry(int hash, K key, V value, HashEntry<K,V> next) {
        this.hash = hash;
        this.key = key;
        this.value = value;
        this.next = next;
    }
    ...
    ...
}
```

可以看到HashEntry

1. 除了value以外，其他几个变量都是final的，这样做为了防止链表结构被破坏，出现ConcurrentModification的情况。
2. next域被final修饰，说明每次往链表添加元素时，只能添加在链头，这样next才能不够被修改

## 五、ConcurrentHashMap的存储 

在ConcurrentHashMap中，当执行put方法的时候，会需要加锁来完成，且ConcurrentHashMap不允许空值

```
@SuppressWarnings("unchecked")
public V put(K key, V value) {
    Segment<K,V> s;
    if (value == null)
        throw new NullPointerException();
    int hash = hash(key);
    int j = (hash >>> segmentShift) & segmentMask;
    if ((s = (Segment<K,V>)UNSAFE.getObject          // nonvolatile; recheck
         (segments, (j << SSHIFT) + SBASE)) == null) //  in ensureSegment
        s = ensureSegment(j);
    return s.put(key, hash, value, false);
}
```

我们可以看到，首先有一个Segment的引用，然后会通过hash\(\)方法对key进行计算，得到哈希值，确定元素在Segment的位置后，通过调用Segment的put\(\)方法，将元素存储到该Segment的HashEntry数组中

```
final V put(K key, int hash, V value, boolean onlyIfAbsent) {
    //加锁，这里是锁定的Segment而不是整个ConcurrentHashMap
    HashEntry<K,V> node = tryLock() ? null :scanAndLockForPut(key, hash, value);
    V oldValue;
    try {
        HashEntry<K,V>[] tab = table;
        //得到hash对应的table中的索引index
        int index = (tab.length - 1) & hash;
        //找到hash对应的是具体的哪个桶，也就是哪个HashEntry链表
        HashEntry<K,V> first = entryAt(tab, index);
        for (HashEntry<K,V> e = first;;) {
            if (e != null) {
                K k;
                if ((k = e.key) == key ||
                    (e.hash == hash && key.equals(k))) {
                    oldValue = e.value;
                    if (!onlyIfAbsent) {
                        e.value = value;
                        ++modCount;
                    }
                    break;
                }
                e = e.next;
            }
            else {
                if (node != null)
                    node.setNext(first);
                else
                    node = new HashEntry<K,V>(hash, key, value, first);
                int c = count + 1;
                if (c > threshold && tab.length < MAXIMUM_CAPACITY)
                    rehash(node);
                else
                    setEntryAt(tab, index, node);
                ++modCount;
                count = c;
                oldValue = null;
                break;
            }
        }
    } finally {
        //解锁
        unlock();
    }
    return oldValue;
}
```

需要注意的是

加锁操作是针对的 hash 值对应的某个Segment，而不是整个ConcurrentHashMap，因为put操作只是在这个Segment中完成，所以并不需要对整个ConcurrentHashMap加锁。此时，其他的线程也可以对另外的Segment进行put操作，因为虽然该Segment被锁住了，但其他的Segment并没有加锁。

## 六、ConcurrentHashMap的读取 

ConcurrentHashMap中的读方法不需要加锁，所有的修改操作在进行结构修改时都会在最后一步写count 变量，通过这种机制保证get操作能够得到几乎最新的结构更新

```
V get(Object key, int hash) { 
    if(count != 0) {       // 首先读 count 变量
        HashEntry<K,V> e = getFirst(hash); 
        while(e != null) { 
            if(e.hash == hash && key.equals(e.key)) { 
                V v = e.value; 
                if(v != null)            
                    return v; 
                // 如果读到 value 域为 null，说明发生了重排序，加锁后重新读取
                return readValueUnderLock(e); 
            } 
            e = e.next; 
        } 
    } 
    return null; 
}
```

ConcurrentHashMap 的高并发性主要来自于三个方面：

1. 用分离锁实现多个线程间的更深层次的共享访问，减小了请求同一个锁的频率
2. 用HashEntery对象的不变性来降低执行读操作的线程在遍历链表期间对加锁的需求
3. 通过对同一个Volatile变量的写 / 读访问，协调不同线程间读 / 写操作的内存可见性

## 七、ConcurrentHashMap与HashTable最大的区别 

![](/assets/import_table_hash.png)



