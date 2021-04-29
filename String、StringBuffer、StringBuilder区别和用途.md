# String、StringBuffer、StringBuilder区别和用途

### tag

腾讯

## String 字符串

我们查看一下String的源代码

```java
// openjdk-11
public final class String
      implements java.io.Serializable, Comparable<String>, CharSequence {
    @Stable
    private final byte[] value;
  
    public String replace(char oldChar, char newChar) {
        if (oldChar != newChar) {
            String ret = isLatin1() ? StringLatin1.replace(value, oldChar, newChar)
                                    : StringUTF16.replace(value, oldChar, newChar); // 这里只看第二个就可以了
            if (ret != null) {
                return ret;
            }
        }
        return this;
    }
  //... 省略无关
}


```

我们可以发现  value 是被 **private final** 修饰的，是**私有的、不可变**的，即使 final 的 修改的数字其具体数值可以改变，但 private 后的 value 无法被外部访问。所以你没办法直接操作 value 数组。也就是说，一旦一个 String 对象被创建之后，包含在这个对象中的字符序列是不可改变的，直到这个对象被销毁。

而 String 对象在诸如 substring()、replace() 方法通过查看原码可以看出来都是不改变 value 数组本身的，而是返回一个新 String 对象。

```java
// openjdk-11
public final class String
      implements java.io.Serializable, Comparable<String>, CharSequence {
  
    //... 省略无关
    // 观察一下 replace 函数
    public String replace(char oldChar, char newChar) {
        if (oldChar != newChar) {
            String ret = isLatin1() ? StringLatin1.replace(value, oldChar, newChar)
                                    : StringUTF16.replace(value, oldChar, newChar); // 这里只看第二个就可以了
            if (ret != null) {
                return ret;
            }
        }
        return this;
    }
}

final class StringUTF16 {
  // ...略
  	public static String replace(byte[] value, char oldChar, char newChar) {
        int len = value.length >> 1;
        int i = -1;
        while (++i < len) {
            if (getChar(value, i) == oldChar) {
                break;
            }
        }
        if (i < len) {
            byte buf[] = new byte[value.length];
            for (int j = 0; j < i; j++) {
                putChar(buf, j, getChar(value, j)); // TBD:arraycopy?
            }
            while (i < len) {
                char c = getChar(value, i);
                putChar(buf, i, c == oldChar ? newChar : c);
                i++;
           }
           // Check if we should try to compress to latin1
           if (String.COMPACT_STRINGS &&
               !StringLatin1.canEncode(oldChar) &&
               StringLatin1.canEncode(newChar)) {
               byte[] val = compress(buf, 0, len);
               if (val != null) {
                   return new String(val, LATIN1);
               }
           }
           return new String(buf, UTF16);
        }
        return null;
    }
}
```

所以 String 对象具体的值无法通过除了反射以外的方式改变。当你对字符串操作如 str1 + str2 是会生成一个新的对象，频繁 new String 内存的占用较多，造成资源浪费。所以一般String字符串用作简单的字符串的存储和处理。

为什么这么设计呢？首先 Java 的所有字符串都是存放在字符串常量池中。字符串常量池是 Java 堆内存中一个特殊的存储区域, 当创建一个 String 对象时,假如此字符串值已经存在于常量池中,则不会创建一个新的对象,而是引用已经存在的对象。具体更多的 String 我们就不深入了，可以单独去了解。



## StringBuilder

由于源码套娃套的比较多，我们就不看代码了。

StringBuilder 继承自 AbstractStringBuilder。StringBuilder 对象可以代表一个字符系列可变的字符串。他提供 append()、insert()、replace()、delete()、setCharAt()等字符串操作，底层维护的也是一个数组，超过长度后会扩容。最后通过调用 toString() 方法来返回字符串。

当我们进行频繁的字符串操作的时候应当使用 StringBuilder，但是 StringBuilder 是线程不安全的。



## StringBuffer

StringBuffer 也是继承自 AbstractStringBuilder。当我们查看 StringBuffer 的时候能发现他的基本的方法都是被 synchronized 所修饰的，也就是说 StringBuffer 对象的操作是线程安全的，基本使用也和 StringBuilder 类似。



## 总结

|           String           | StringBuilder | StringBuffer |
| :------------------------: | :-----------: | :----------: |
| 不可变，操作会产生新的对象 | 可变、速度快  | 可变、速度快 |
|                            |  线程不安全   |   线程安全   |

性能： StringBuilder > StringBuffer > String

- Q：我们需要经常改变字符串内容时候，使用什么字符串类型呢？

  A：我们优先使用 StringBuilder ，当遇到多线程使用共享变量时使用 StringBuffer。





## 参考资料

[Java中的String为什么是不可变的？ -- String源码分析](https://blog.csdn.net/zhangjg_blog/article/details/18319521)

[为什么String要设计成不可变的?](https://blog.csdn.net/renfufei/article/details/16808775)

