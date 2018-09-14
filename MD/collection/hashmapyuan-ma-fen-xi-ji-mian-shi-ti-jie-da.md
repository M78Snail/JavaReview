## 一、什么是HashMap {#一、什么是HashMap}

1. HashMap是基于哈希表的Map接口的非同步实现
2. HashMap中元素的key是唯一的、value值可重复
3. HashMap允许使用null值和null键
4. HashMap中的元素是无序的

## 二、HashMap的数据结构 {#二、HashMap的数据结构}

HashMap是一个“链表散列”的数据结构，即数组和链表的结合体，如图所示

![](/assets/import_hashmap.png)

从图中看出，HashMap底层就是一个数组结构，数组中的每一项又是一个链表，当新建一个 HashMap 的时候，就会初始化一个数组，我们可以查看HashMap源码，在构造函数中

```
public HashMap(int initialCapacity, float loadFactor) {
    if (initialCapacity < 0)
        throw new IllegalArgumentException("Illegal initial capacity: " +
                                           initialCapacity);
    if (initialCapacity > MAXIMUM_CAPACITY)
        initialCapacity = MAXIMUM_CAPACITY;
    if (loadFactor <= 0 || Float.isNaN(loadFactor))
        throw new IllegalArgumentException("Illegal load factor: " +
                                           loadFactor);
    // Find a power of 2 >= initialCapacity
    int capacity = 1;
    while (capacity < initialCapacity)
        capacity <<= 1;
    this.loadFactor = loadFactor;
    threshold = (int)Math.min(capacity * loadFactor, MAXIMUM_CAPACITY + 1);
    table = new Entry[capacity];
    useAltHashing = sun.misc.VM.isBooted() &&
            (capacity >= Holder.ALTERNATIVE_HASHING_THRESHOLD);
    init();
}
```

第18行代码`table = new Entry[capacity];`创建了一个 Entry 的数组，也就是图中的table数组，那么 Entry 又是什么结构呢？

```
static class Entry<K,V> implements Map.Entry<K,V> {
    final K key;
    V value;
    Entry<K,V> next;
    final int hash;
    ……
}
```

Entry是一个static class，其中包含了 key 和 value，也就是键值对，另外还包含了一个next的Entry指针。我们可以总结出Entry就是数组中的元素，每个Entry其实就是一个key-value对，它持有一个指向下一个元素的引用，这就构成了链表

## 三、HashMap的存储 {#三、HashMap的存储}

知道HashMap的数据结构之后，下面就来研究如何把元素存进HashMap中

```
/**
 * Associates the specified value with the specified key in this map.
 * If the map previously contained a mapping for the key, the old
 * value is replaced.
 *
 * @param key key with which the specified value is to be associated
 * @param value value to be associated with the specified key
 * @return the previous value associated with <tt>key</tt>, or
 *         <tt>null</tt> if there was no mapping for <tt>key</tt>.
 *         (A <tt>null</tt> return can also indicate that the map
 *         previously associated <tt>null</tt> with <tt>key</tt>.)
 */
public V put(K key, V value) {
    //其允许存放null的key和null的value，当其key为null时，调用putForNullKey方法，放入到table[0]的这个位置
    if (key == null)
        return putForNullKey(value);
    //通过调用hash方法对key进行哈希，得到哈希之后的数值。该方法实现可以通过看源码，其目的是为了尽可能的让键值对可以分不到不同的桶中
    int hash = hash(key);
    //根据上一步骤中求出的hash得到在数组中是索引i
    int i = indexFor(hash, table.length);
    //如果i处的Entry不为null，则通过其next指针不断遍历e元素的下一个元素。
    for (Entry<K,V> e = table[i]; e != null; e = e.next) {
        Object k;
        if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {
            V oldValue = e.value;
            e.value = value;
            e.recordAccess(this);
            return oldValue;
        }
    }
    modCount++;
    addEntry(hash, key, value, i);
    return null;
}
```

在官方注释中提到：当调用我们put方法的时候

1. 如果key存在，key不会覆盖，新的value会代替旧的value，该方法返回的是旧的 value
2. 如果key不存在，该方法返回的是null

在源代码中可以看出：当我们往HashMap中put元素的时候

1. 根据key的hashCode重新计算hash值，根据hash值得到这个元素在数组中的位置（即下标）
2. 如果数组该位置上已经存放有其他元素，那么在这个位置上的元素将以链表的形式存放，新加入的放在链头，最先加入的放在链尾
3. 如果数组该位置上没有元素，就直接将该元素放到此数组中的该位置上

addEntry\(hash, key, value, i\)方法的作用：

1. 根据计算出的hash值，找到数组table对应的i索引位置，取出数组table的i索引处的key-value（旧的）
2. 将新的key-value放在数组table的i索引处，并将新的key-value的next指向旧的key-value，从而形成链表

```
void addEntry(int hash, K key, V value, int bucketIndex) {
	//如果数组大小不够，会扩充数组大小，后面会讲到
    if ((size >= threshold) && (null != table[bucketIndex])) {
        resize(2 * table.length);
        hash = (null != key) ? hash(key) : 0;
        bucketIndex = indexFor(hash, table.length);
    }
    createEntry(hash, key, value, bucketIndex);
}
void createEntry(int hash, K key, V value, int bucketIndex) {
    // 获取指定 bucketIndex 索引处的 Entry
    Entry<K,V> e = table[bucketIndex];
    // 将新创建的 Entry 放入 bucketIndex 索引处，并让新的 Entry 指向原来的 Entr
    table[bucketIndex] = new Entry<>(hash, key, value, e);
    size++;
}
```

当系统决定存储HashMa 中的key-value时，完全没有考虑Entry中的value，仅仅只是根据key来计算并决定每个Entry的存储位置。我们完全可以把Map集合中的value当成key的附属，当系统决定了key的存储位置之后，value随之保存在那里

## 四、HashMap的读取 {#四、HashMap的读取}

```
public V get(Object key) {
    if (key == null)
        return getForNullKey();
    Entry<K,V> entry = getEntry(key);
    return null == entry ? null : entry.getValue();
}
final Entry<K,V> getEntry(Object key) {
    int hash = (key == null) ? 0 : hash(key);
    for (Entry<K,V> e = table[indexFor(hash, table.length)];
         e != null;
         e = e.next) {
        Object k;
        if (e.hash == hash &&
            ((k = e.key) == key || (key != null && key.equals(k))))
            return e;
    }
    return null;
}
```

从上面的源代码中可以看出：

1. 从HashMap中get元素时，首先计算key的hashCode，找到数组中对应位置的某一元素
2. 通过key的equals方法在对应位置的链表中找到需要的元素

## 五、HashMap简单归纳 {#五、HashMap简单归纳}

简单地说，HashMap在底层将key-value当成一个整体进行处理，这个整体就是一个Entry对象。HashMap底层采用一个 Entry\[\]数组来保存所有的key-value，当需要存储一个Entry对象时，会根据 hash 算法来决定其在数组中的存储位置，在根据equals方法决定其在该数组位置上的链表中的存储位置；当需要取出一个Entry时，也会根据hash算法找到其在数组中的存储位置，再根据equals方法从该位置上的链表中取出该Entry

## 六、HashMap的hash算法 {#六、HashMap的hash算法}

在HashMap的存储和读取中，都用到了hash\(\)这个算法，hash\(int h\)方法是根据key的hashCode重新计算一次散列。此算法加入了高位计算，防止低位不变，高位变化时，造成的hash冲突

```
final int hash(Object k) {
    int h = 0;
    if (useAltHashing) {
        if (k instanceof String) {
            return sun.misc.Hashing.stringHash32((String) k);
        }
        h = hashSeed;
    }
    //得到k的hashcode值
    h ^= k.hashCode();
    //进行计算
    h ^= (h >>> 20) ^ (h >>> 12);
    return h ^ (h >>> 7) ^ (h >>> 4);
}
```

我们知道HashMap中要找到某个元素时，需要根据key的hash值来求得对应数组中的位置。如何计算这个位置就是hash算法。前面说过HashMap的数据结构是数组和链表的结合，所以我们当然希望这个HashMap里面的元素位置尽量分布均匀些，尽量使得每个位置上的元素数量只有一个，那么当我们用hash算法求得这个位置的时候，马上就可以知道对应位置的元素就是我们要读取的，而不用再去遍历链表，这样就大大优化了查询的效率

## 七、HashMap的容量 {#七、HashMap的容量}

对于任意给定的对象，只要它的hashCode\(\)返回值相同，那么程序调用hash\(int h\)方法所计算得到的hash码值总是相同的。我们首先想到的就是把hash值对数组长度取模运算，这样一来，元素的分布相对来说是比较均匀的。但是，“模”运算的消耗还是比较大的，在HashMap中是这样做的，调用 indexFor\(int h, int length\) 方法来计算该对象应该保存在table数组的哪个索引处

```
static int indexFor(int h, int length) {  
    return h & (length-1);
}
```

这个方法非常巧妙，它通过 h & \(table.length -1\) 来得到该对象的保存位，而HashMap底层数组的长度总是 2 的 n 次方，这是HashMap在速度上的优化。在HashMap构造器中有如下代码：

```
int capacity = 1;
    while (capacity < initialCapacity)  
        capacity <<= 1;
```

**这段代码保证初始化时HashMap的容量总是 2 的 n 次方，即底层数组的长度总是为 2 的 n 次方。**

当length总是 2 的 n 次方时，h&\(length-1\)运算等价于对 length 取模，也就是 h%length，但是 &比 % 具有更高的效率。这看上去很简单，其实比较有玄机的，我们举个例子来说明，假设数组长度分别为 15 和 16，优化后的 hash 码分别为 8 和 9，那么 &运算后的结果如下：

| h&\(table.length-1\) | hash |  | table.length-1 | 结果 |
| :---: | :---: | :---: | :---: | :---: |
| 8&\(15-1\) | 0100 | & | 1110 | =0100 |
| 9&\(15-1\) | 0101 | & | 1110 | =0100 |
| 8&\(16-1\) | 0100 | & | 1111 | =0100 |
| 9&\(16-1\) | 0101 | & | 1111 | =0101 |

从上面的例子中可以看出：当它们和 15-1（1110）“与”的时候，产生了相同的结果，也就是说它们会定位到数组中的同一个位置上去，这就产生了碰撞，8 和 9 会被放到数组中的同一个位置上形成链表，那么查询的时候就需要遍历这个链表，得到8或者9，这样就降低了查询的效率。同时，我们也可以发现，当数组长度为 15 的时候，hash 值会与 15-1（1110）进行“与”，那么最后一位永远是 0，而 0001，0011，0101，1001，1011，0111，1101 这几个位置永远都不能存放元素了，空间浪费相当大，更糟的是这种情况中，数组可以使用的位置比数组长度小了很多，这意味着进一步增加了碰撞的几率，减慢了查询的效率！而当数组长度为16时，即为2的n次方时，2n-1 得到的二进制数的每个位上的值都为 1，这使得在低位上&时，得到的和原 hash 的低位相同，加之 hash\(int h\)方法对 key 的 hashCode 的进一步优化，加入了高位计算，就使得只有相同的 hash 值的两个值才会被放到数组中的同一个位置上形成链表。所以说，当数组长度为 2 的 n 次幂的时候，不同的 key 算得得 index 相同的几率较小，那么数据在数组上分布就比较均匀，也就是说碰撞的几率小，相对的，查询的时候就不用遍历某个位置上的链表，这样查询效率也就较高了

## 八、HashMap的resize（rehash） {#八、HashMap的resize（rehash）}

当 HashMap 中的元素越来越多的时候，hash冲突的几率也就越来越高，因为数组的长度是固定的，所以为了提高查询的效率，就要对HashMap的数组进行扩容，在HashMap数组扩容之后，最消耗性能的点是：原数组中的数据必须重新计算其在新数组中的位置，并放进去，这就是 resize（rehash）

那么HashMap什么时候进行扩容呢？当HashMap中的元素个数超过（数组大小_loadFactor）时，就会进行数组扩容，loadFactor指的是负载因子，从字面上理解就是HashMap能够承受住自身负载（大小或容量）的因子，loadFactor的默认值为 0.75，这是一个折中的取值。也就是说，默认情况下，数组大小为 16，那么当HashMap中元素个数超过 16_0.75=12 的时候，就把数组的大小扩展为 2\*16=32，即扩大一倍，然后重新计算每个元素在数组中的位置，而这是一个非常消耗性能的操作，所以如果我们已经预知 HashMap 中元素的个数，那么预设元素的个数能够有效的提高 HashMap 的性能

负载因子 loadFactor 衡量的是一个散列表的空间的使用程度，负载因子越大表示散列表的装填程度越高，反之愈小。对于使用链表法的散列表来说，查找一个元素的平均时间是 O\(1+a\)，因此如果负载因子越大，对空间的利用更充分，然而后果是查找效率的降低；如果负载因子太小，那么散列表的数据将过于稀疏，对空间造成严重浪费

## 九、HashMap的构造方法 {#九、HashMap的构造方法}

HashMap 包含如下几个构造器：

1. HashMap\(\)：构建一个初始容量为 16，负载因子为 0.75 的 HashMap
2. HashMap\(int initialCapacity\)：构建一个初始容量为 initialCapacity，负载因子为 0.75 的 HashMap
3. HashMap\(int initialCapacity, float loadFactor\)：以指定初始容量、指定的负载因子创建一个 HashMap

HashMap 的实现中，通过 threshold 字段来判断 HashMap 的最大容量：

```
threshold = (int)(capacity * loadFactor);
```

threshold就是在loadFactor和capacity对应下允许的最大元素数目，默认的的负载因子 0.75 是对空间和时间效率的一个平衡选择，当容量超出此最大容量时，就会发生resize，resize后的 HashMap 容量是容量的两倍，这点可以从addEntry方法看出

```
void addEntry(int hash, K key, V value, int bucketIndex) {
	//如果数组大小不够，会扩充数组大小
    if ((size >= threshold) && (null != table[bucketIndex])) {
        resize(2 * table.length);
        hash = (null != key) ? hash(key) : 0;
        bucketIndex = indexFor(hash, table.length);
    }
    createEntry(hash, key, value, bucketIndex);
}
```

## 十、HashMap的Fail-Fast机制 {#十、HashMap的Fail-Fast机制}

HashMap不是线程安全的，因此在使用迭代器的过程中有其他线程修改了map，那么将抛出 ConcurrentModificationException，这就是所谓 fail-fas机制，fail-fast机制是 java 集合\(Collection\)中的一种错误机制，当多个线程对同一个集合的内容进行操作时，就可能会产生fail-fast事件。这一机制在源码中的实现是通过modCount域（修改次数），对 HashMap 内容进行修改时都将增加这个值，那么在迭代器初始化过程中会将这个值赋给迭代器的 expectedModCount

```
HashIterator() {
    expectedModCount = modCount;
    if (size > 0) { // advance to first entry
    Entry[] t = table;
    while (index < t.length && (next = t[index++]) == null)  
        ;
    }
}
```

在迭代过程中，判断 modCount 跟 expectedModCount 是否相等，如果不相等就表示已经有其他线程修改了 Map。同时， modCount 声明为 volatile，保证了线程之间修改的可见性

```
final Entry<K,V> nextEntry() {
    if (modCount != expectedModCount)
        throw new ConcurrentModificationException();
```

在 HashMap 的 API 中指出：

由所有 HashMap 类的“collection 视图方法”所返回的迭代器都是快速失败的：在迭代器创建之后，如果从结构上对映射进行修改，除非通过迭代器本身的 remove 方法，其他任何时间任何方式的修改，迭代器都将抛出 ConcurrentModificationException。因此，面对并发的修改，迭代器很快就会完全失败，而不冒在将来不确定的时间发生任意不确定行为的风险

注意，迭代器的快速失败行为不能得到保证，一般来说，存在非同步的并发修改时，不可能作出任何坚决的保证。快速失败迭代器尽最大努力抛出 ConcurrentModificationException。因此，编写依赖于此异常的程序的做法是错误的，正确做法是：迭代器的快速失败行为应该仅用于检测程序错误

解决方案：

在上文中也提到，fail-fast 机制，是一种错误检测机制。它只能被用来检测错误，因为 JDK 并不保证 fail-fast 机制一定会发生。若在多线程环境下使用 fail-fast 机制的集合，建议使用“java.util.concurrent 包下的类”去取代“java.util 包下的类”

## 十一、HashMap的两种遍历方式 {#十一、HashMap的两种遍历方式}

第一种，效率高，建议使用

```
Map map = new HashMap();
Iterator iter = map.entrySet().iterator();
while (iter.hasNext()) {
　　Map.Entry entry = (Map.Entry) iter.next();
　　Object key = entry.getKey();
　　Object val = entry.getValue();
}
```

第二种，效率低，建议不要使用 　　

```
Map map = new HashMap();
Iterator iter = map.keySet().iterator();
while (iter.hasNext()) {
　　Object key = iter.next();
　　Object val = map.get(key);
}
```

## 十二、HashMap的面试题解答 {#十二、HashMap的面试题解答}

1、你用过HashMap吗？什么是HashMap？你为什么用到它？

用过，HashMap是基于哈希表的Map接口的非同步实现，它允许null键和null值，且HashMap依托于它的数据结构的设计，存储效率特别高，这是我用它的原因

2、你知道HashMap的工作原理吗？你知道HashMap的get\(\)方法的工作原理吗？

> 上面两个问题属于同一答案的问题

HashMap是基于hash算法实现的，通过put\(key,value\)存储对象到HashMap中，也可以通过get\(key\)从HashMap中获取对象。当我们使用put的时候，首先HashMap会对key的hashCode\(\)的值进行hash计算，根据hash值得到这个元素在数组中的位置，将元素存储在该位置的链表上。当我们使用get的时候，首先HashMap会对key的hashCode\(\)的值进行hash计算，根据hash值得到这个元素在数组中的位置，将元素从该位置上的链表中取出

3、当两个对象的hashcode相同会发生什么？

hashcode相同，说明两个对象HashMap数组的同一位置上，接着HashMap会遍历链表中的每个元素，通过key的equals方法来判断是否为同一个key，如果是同一个key，则新的value会覆盖旧的value，并且返回旧的value。如果不是同一个key，则存储在该位置上的链表的链头

4、如果两个键的hashcode相同，你如何获取值对象？

遍历HashMap链表中的每个元素，并对每个key进行hash计算，最后通过get方法获取其对应的值对象

5、如果HashMap的大小超过了负载因子\(load factor\)定义的容量，怎么办？

负载因子默认是0.75，HashMap超过了负载因子定义的容量，也就是说超过了（HashMap的大小\*负载因子）这个值，那么HashMap将会创建为原来HashMap大小两倍的数组大小，作为自己新的容量，这个过程叫resize或者rehash

6、你了解重新调整HashMap大小存在什么问题吗？

当多线程的情况下，可能产生条件竞争。当重新调整HashMap大小的时候，确实存在条件竞争，如果两个线程都发现HashMap需要重新调整大小了，它们会同时试着调整大小。在调整大小的过程中，存储在链表中的元素的次序会反过来，因为移动到新的数组位置的时候，HashMap并不会将元素放在LinkedList的尾部，而是放在头部，这是为了避免尾部遍历\(tail traversing\)。如果条件竞争发生了，那么就死循环了

7、我们可以使用自定义的对象作为键吗？

可以，只要它遵守了equals\(\)和hashCode\(\)方法的定义规则，并且当对象插入到Map中之后将不会再改变了。如果这个自定义对象时不可变的，那么它已经满足了作为键的条件，因为当它创建之后就已经不能改变了。

