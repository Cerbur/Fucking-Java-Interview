# ==和equals.md

## ==

基本变量类型比较变量值。引用类型比较内存地址。

## equals

调用 equals 函数，具体比较个啥，看 equals 函数。

```java
public boolean equals(Object anObject) {
        if (this == anObject) {
            return true;
        }
        if (anObject instanceof String) {
            String aString = (String)anObject;
            if (coder() == aString.coder()) {
                return isLatin1() ? 
                  StringLatin1.equals(value, aString.value)
                : StringUTF16.equals(value, aString.value);
            }
        }
        return false;
    }
```

Java11 中 String 对象的 equals 代码。

值得一提的是，当用 String 的 equals 对比一个字符串常量应该是 

```java
"字符串常量".equals(string);
```

而不是

```java
string.equals("字符串常量");
```

因为后者可能造成空指针异常。



## 总结

没什么好说的，就你学 Java 就应该知道的。

