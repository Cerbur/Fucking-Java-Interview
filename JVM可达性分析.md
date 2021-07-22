# JVM可达性分析

https://juejin.cn/post/6844904038027247623

有空看看这篇文章

![](https://raw.githubusercontent.com/Cerbur/pic/main/20210716183017.png)

这个算法的基本思路就是通过一系列的称为“GC Roots”的对象作为起始点，从这些节点开始向下搜索，搜索所走过的路径称为引用链（Reference Chain），当一个对象到GC Roots没有任何引用链相连时，则证明此对象是不可用的。

**作为GC Roots的对象包括下面几种：**

♦  **1.虚拟机栈（栈帧中的本地变量表）中的对象。**（方法中的参数，方法体中的局部变量）

♦  **2.方法区中 类静态属性的对象。** （static)

♦  **3.方法区中 常量的对象。**  （final static）

♦  **4.本地方法栈中 JNI（即一般说的Native方法）的对象。**

