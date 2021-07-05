# ArrayList和LinkedList的区别

## ArrayList

ArrayList 基于动态数组，需要连续的内存空间来存储，所以适合下标访问（随机访问）。

扩容机制：数组是固定的长度，当数组的长度超出存储数据需要的长度时，会新建一个旧长度 **1.5** 倍的新数组，并讲老数据拷贝到新数组。在 Java 源码中我们可以看到，新数组就是旧数组的 1.5 倍。

性能问题：在中间部分删除插入的时候，会有大量的数据移动，会有性能问题。超过数组长度时，需要扩容，完成扩容后也需要复制数组，造成性能损耗。

如何解决性能问题/如何优化/使用场景：

- 尽量避免在中部和前部位进行插入删除操作，删除的下标越靠前性能问题越大
- 预估一下使用的容量，提前初始化好减少扩容的可能
- 尽量只在尾部进行插入和删除操作，此操作甚至性能会比 LinkedList 优秀，LinkedList 插入需要创建节点。同样的也会消耗更多的内存。

```java
// from openjdk-11
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
{
    // ...省略无关
    transient Object[] elementData;

    private int newCapacity(int minCapacity) {
            // overflow-conscious code
				int oldCapacity = elementData.length;
				int newCapacity = oldCapacity + (oldCapacity >> 1);          
    }
  
  	public E remove(int index) {
        Objects.checkIndex(index, size);
        final Object[] es = elementData;

        @SuppressWarnings("unchecked") E oldValue = (E) es[index];
        fastRemove(es, index);

        return oldValue;
    }
  	private void fastRemove(Object[] es, int i) {
        modCount++;
        final int newSize;
        if ((newSize = size - 1) > i)
            System.arraycopy(es, i + 1, es, i, newSize - i);
        es[size = newSize] = null;
    }
}

```



## LinkedList

LinkedList 基于双向链表，可以存储在分散的内存中，适合做数据的插入和删除操作，不适合查询。

遍历 LinkedList 要使用 iterator，fori 操作通过 get(i) 每次都需要从 list 头或者尾进行遍历，性能损耗非常大。

get(i) 会在当 i < (size/2) 的时候从头查找，否则从尾查找。

```java
public class LinkedList<E>
    extends AbstractSequentialList<E>
    implements List<E>, Deque<E>, Cloneable, java.io.Serializable
{
  	public E get(int index) {
        checkElementIndex(index);
        return node(index).item;
    }
  
  	Node<E> node(int index) {
        // assert isElementIndex(index);
        if (index < (size >> 1)) {
            Node<E> x = first;
            for (int i = 0; i < index; i++)
                x = x.next;
            return x;
        } else {
            Node<E> x = last;
            for (int i = size - 1; i > index; i--)
                x = x.prev;
            return x;
        }
    }
}
```



## 总结

- 当出现在中间部分需要做大量的删除和插入操作时候使用 LinkedList
- 查询偏多且是随机访问便多也建议使用 ArrayLIst
- 经常要获取某个下标，在对下标操作不建议使用 LinkedList。首先需要一次遍历获取下标地址，第二去操作这个下标地址的时候仍然需要一次顺序访问到该下标地址才能进行操作
- 在尾部进行大量的插入和删除操作时候建议使用 ArrayList。此时 ArrayList 性能优于 LinkedList