# hashCode和equals

## hashCode

>hashcode方法返回该对象的哈希码值。支持该方法是为哈希表提供一些优点，例如，java.util.Hashtable 提供的哈希表。 
>
>hashCode 的常规协定是： 
>在 Java 应用程序执行期间，在同一对象上多次调用 hashCode 方法时，必须一致地返回相同的整数，前提是对象上 equals 比较中所用的信息没有被修改。从某一应用程序的一次执行到同一应用程序的另一次执行，该整数无需保持一致。 
>如果根据 equals(Object) 方法，两个对象是相等的，那么在两个对象中的每个对象上调用 hashCode 方法都必须生成相同的整数结果。 
>以下情况不 是必需的：如果根据 equals(java.lang.Object) 方法，两个对象不相等，那么在两个对象中的任一对象上调用 hashCode 方法必定会生成不同的整数结果。但是，程序员应该知道，为不相等的对象生成不同整数结果可以提高哈希表的性能。 
>实际上，由 Object 类定义的 hashCode 方法确实会针对不同的对象返回不同的整数。（这一般是通过将该对象的内部地址转换成一个整数来实现的，但是 JavaTM 编程语言不需要这种实现技巧。） 
>
>当equals方法被重写时，通常有必要重写 hashCode 方法，以维护 hashCode 方法的常规协定，该协定声明相等对象必须具有相等的哈希码。

## equals

用于判断两个对象是否相等的方法。Object.equals()是判断内存地址是否相等。



## 总结

1. 两个对象相等，hashcode必须相同
2. hashcode值相同，对象不一定相同
3. 如果 hashCode 不重写，那一个堆上的不同对象产生的 hashCode 都是唯一的。如果业务需求中要保证 hash 表中某字段的唯一性，那不重写 hashCode 会造成无法满足需求。

所以

- equals 方法被重写，必须重写 hashCode。不然不能保证两个对象相等，hashCode 也相同

- hashcode 重写，equals 通常也要重写，Object.equals()是判断内存地址，如果你对需求中只要某个字段相同就当成相等的话，默认的 equals 方法却一定是返回 false 的。必须保证在 hash 表中此字段唯一则不但要重写 hashCode 还要重写 equals 



- 总的来说，你要用 hash表 那你最好重写 hashCode 和 equals

