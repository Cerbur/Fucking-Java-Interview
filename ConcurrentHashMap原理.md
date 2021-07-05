# ConcurrentHashMap原理

## 总结

在 jdk8 中， 使用了 synchronized + CAS操作 + 链表 + 红黑树，Node 节点的 val 和 next 使用了 volatile 修饰，以保证可见性。

查找、替换、赋值操作使用 CAS 以提高效率。

并发操作时，锁住 head 节点，不影响其他元素的读写，锁的粒度细，效率就更高，扩有时候 synchronized 阻塞所有的读写操作、并发扩容。

读操作无锁：

Node 的 val 和 next 使用 volatile 修饰，读写线程对该变量互相可见。

数组用 volatile 修饰，保证扩容时被读线程感知。