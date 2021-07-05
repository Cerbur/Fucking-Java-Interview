# HashMap和HashTable的区别

## 区别

- HashMap 的方法没用使用 synchronize 修饰，线程不是安全的，而 HashTable 使用 synchronize 修饰为线程安全的。
- HashMap 允许 key 为 null，HashTable 不允许。

 

## 底层实现

### 插入

1. 通过 hash() 计算 key 的 hash 值，让后对数组长度进行取模，获得到对应的下标位置。

   ```java
   // openjdk-11 HashMap.hash
   static final int hash(Object key) {
     	int h;
   		return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
   }
   ```

2. 如果没有产生 hash 冲突时候(下标无元素)，则直接创建节点并存入

3. 如果产生 hash 冲突，先进行 equals 比较，如果相同就取代该元素，如果不同，就判断链表高度来插入链表，当链表高度达到 **8** 的时候，并且数组长度到 **64** 的时候转化为红黑树，当长度低于 **6** 时，转回链表。

4. 源码可以看出 null 数下标为 0。 



### 数组扩容

但容量超过某个阈值时候，hash 冲突的概率会增加，为了保证查询效率需要对数组进行扩容以保证查询效率。而我们知道数组是一个定义后就不可变的，也就是需要创建新数组，并移动旧数组的数据，这里就涉及到重新 hash（因为长度改变）。而 jdk8 中是对数组进行扩容后需要 resize() 来重新分配各个元素所在的位置。

具体操作就是当 hashmap 中的元素个数超过 数组大小 * loadFactor 时，就会进行数组扩容，loadFactor 的默认值为 0.75，也就是说，默认情况下，数组大小为 16，那么当hashmap中元素个数超过 16 * 0.75 = 12 的时候，就把数组的大小扩展为 2 * 16 = 32，即扩大一倍。如何把旧数组的数据复制到新数组，之后对新数组的个元素位置进行分配。

此外 resize() 扩容和是原来 2 倍，和尽量保证容量是 2 的 整数都是有原因的。为了方便扩容的时候尽量减少重新 hash 的数量。这部分可以看[HashMap的扩容机制---resize()](https://blog.csdn.net/pange1991/article/details/82347284)。

## HashMap源码分析

HashMap 如果是不带参数初始化，是不会初始化容量的，而是在第一次插入的时候进行容量的初始化，默认值为 **16** 。

```java
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16
```



HashMap 从 jdk8 开始，当链表高度到 **8** 且数组长度超过 **64** 的时候，链表会红黑树化，而元素数量有小于**6**时，会又转化为链表。

源码太长了就不贴了，可以去看 HashMap.treeifyBin() 和 putVal() 的函数。

```java
    // 树化链表长度
		static final int TREEIFY_THRESHOLD = 8;

		// 链表化长度
    static final int UNTREEIFY_THRESHOLD = 6;

		// 树化数组长度
    static final int MIN_TREEIFY_CAPACITY = 64;
		
		// 默认负载因子
    static final float DEFAULT_LOAD_FACTOR = 0.75f;
```



```java
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
               boolean evict) {
    HashMap.Node<K,V>[] tab; HashMap.Node<K,V> p; int n, i;
    if ((tab = table) == null || (n = tab.length) == 0)
        n = (tab = resize()).length;
    if ((p = tab[i = (n - 1) & hash]) == null)
        tab[i] = newNode(hash, key, value, null);
    else {
        HashMap.Node<K,V> e; K k;
        if (p.hash == hash &&
                ((k = p.key) == key || (key != null && key.equals(k))))
            e = p;
        else if (p instanceof HashMap.TreeNode)
            e = ((HashMap.TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
        else {
            for (int binCount = 0; ; ++binCount) {
                if ((e = p.next) == null) {
                    p.next = newNode(hash, key, value, null);
                    if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                        treeifyBin(tab, hash);
                    break;
                }
                if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                    break;
                p = e;
            }
        }
        if (e != null) { // existing mapping for key
            V oldValue = e.value;
            if (!onlyIfAbsent || oldValue == null)
                e.value = value;
            afterNodeAccess(e);
            return oldValue;
        }
    }
    ++modCount;
    if (++size > threshold)
        resize();
    afterNodeInsertion(evict);
    return null;
}
```

我们一步一步分析一下put的过程吧。

```java
if ((tab = table) == null || (n = tab.length) == 0)
        n = (tab = resize()).length;
```

第一段的 if 是用来判断 HashMap 是否初始化了的，因为如果构造函数不传值是默认不做 table 的初始化的，而在第一次插入，也就是这里进行初始化擦操作。

```java
if ((p = tab[i = (n - 1) & hash]) == null)
            tab[i] = newNode(hash, key, value, null);
```

第二句 if 是判断这个筒有没有位置的，如果 table 数组上这个位置为空的话，直接创建一个节点填上去就可以了。

```java
else {
        HashMap.Node<K,V> e; K k;
        if (p.hash == hash &&
                ((k = p.key) == key || (key != null && key.equals(k))))
            e = p;
```

如果这里不为空，就判断 key 的 hash 于这个位置的是否相等，key 和 新的 key 是否相等，这就是为什么 equals 函数和 hashCode 函数要重写的原因。如果都相等，我们就直接覆盖掉这里的 value。

```java
for (int binCount = 0; ; ++binCount) {
    if ((e = p.next) == null) {
        p.next = newNode(hash, key, value, null);
        if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
            treeifyBin(tab, hash);
        break;
    }
    if (e.hash == hash &&
            ((k = e.key) == key || (key != null && key.equals(k))))
        break;
    p = e;
}
if (e != null) { // existing mapping for key
    V oldValue = e.value;
    if (!onlyIfAbsent || oldValue == null)
        e.value = value;
    afterNodeAccess(e);
    return oldValue;
}
```

else if 我们跳过，看重点的 else 里面的内容。我们进入循环的时候就开始计算链表的节点数量。第一个 if 就是判断，如果我们一直到了节点的末尾，那就直接插进入，假如这时候链表高度到 **8** 的，那我们就要执行树化。假如在遍历链表的时候遇到了 key 相等，那我们就覆盖 value。如果这时候 e 为不为 null 说明是覆盖旧值，HashMap 的大小不会发生变化我们就不要多处理了。

```java
if (++size > threshold)
    resize();
    afterNodeInsertion(evict);
    return null;
```

如果为 null 说明是新的 key，我们需要对 size 进行 + 1。同时我们还要和扩容大小进行比较，假如我们的 size 已经大于了扩容大小，我们就需要进行扩容。然后结束了。

## 总结

当链表高度到 **8** ，链表会红黑树化，而元素数量有小于**6**时，会又转化为链表。记住插入时候做的事情，扩容的原因。