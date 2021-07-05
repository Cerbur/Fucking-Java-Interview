#   JKD、JRE、JVM

这节介绍一下这三个基础东西，我们自顶向下讲

## JVM

Java Virtual Machine ( Java 虚拟机 )为 Java 程序运行环境的一部分。JVM 是运行 Java 字节码文件的虚拟机。Java 作为一个跨平台的语言，需要一个媒介将编译好的字节码程序翻译为机器码，而 JVM 就是这个媒介。JVM 负责**解释执行** Java 编译器生成的与操作系统平台无关的字节码。

## JRE

Java Runtime Environment ( Java 运行时环境 )。JRE 包含了 Java 虚拟机和 Java 的基础类库。

## JDK

Java Development Kit ( Java 开发工具 )。 JDK 包含了 JRE 和 一堆编写 Java 语言时所需要的工具，比如 Java 编译器 javac。



## 总结

JDK 包括 JRE，JRE 包括 JVM。