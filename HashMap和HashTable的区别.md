# HashMap和HashTable的区别

## 区别

- HashMap 的方法没用使用 synchronize 修饰，线程不是安全的，而 HashTable 使用 synchronize 修饰为线程安全的。
- HashMap 允许 key 为 null，HashTable 不允许。

 

## 底层实现

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



## HashMap源码分析

HashMap 如果是不带参数初始化，是不会初始化容量的，而是在第一次插入的时候进行容量的初始化，默认值为 **16** 。

```java
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16
```



HashMap 从 jdk8 开始，当链表高度到 **8** 且数组长度超过 **64** 的时候，链表会红黑树化，而元素数量有小于**6**时，会又转化为链表。

源码太长了就不贴了，可以去看 HashMap.treeifyBin() 和 putVal() 的函数。

```java
    static final int TREEIFY_THRESHOLD = 8;

    static final int UNTREEIFY_THRESHOLD = 6;

    static final int MIN_TREEIFY_CAPACITY = 64;
```



