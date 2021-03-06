# 操作系统CPU指令

## CPU 和 内存一些功能的组成

![](https://raw.githubusercontent.com/Cerbur/pic/main/20210723002437.png)

## CPU 的指令周期

CPU 从程序计数器读取指令、到执⾏、再到下⼀条指令，这个过程会不断循环，直到程序执⾏结束，这个 不断循环的过程被称为 CPU 的指令周期。



## 一条指令的执行过程

程序在运行时，内存被划分成了正文段、和数据段。正文段用来存储指令，数据段用来存储数据。

a = 1 + 2 执⾏具体过程

汇编语言下 a = 1 + 2

![](https://raw.githubusercontent.com/Cerbur/pic/main/20210723002902.png)

![](https://raw.githubusercontent.com/Cerbur/pic/main/20210723002923.png)

## 机器码指令

CPU 有着不同的指令集，也就对应着不同的机器码，在最简单的 MIPS 指令集中，看看机器码是如何生成的。

![](https://raw.githubusercontent.com/Cerbur/pic/main/20210723003032.png)

- R 指令，⽤在算术和逻辑操作，⾥⾯由读取和写⼊数据的寄存器地址。如果是逻辑位移操作，后⾯还 有位移操作的「位移量」，⽽最后的「功能码」则是再前⾯的操作码不够的时候，扩展操作码来表示 对应的具体指令的；

- I 指令，⽤在数据传输、条件分⽀等。这个类型的指令，就没有了位移量和操作码，也没有了第三个 寄存器，⽽是把这三部分直接合并成了⼀个地址值或⼀个常数；
-  J 指令，⽤在跳转，⾼ 6 位之外的 26 位都是⼀个跳转后的地址；

接下来，我们把前⾯例⼦的这条指令：「 add 指令将寄存器 R0 和 R1 的数据相加，并把结果放⼊到 R2 」，翻译成机器码。

![](https://raw.githubusercontent.com/Cerbur/pic/main/20210723003120.png)



加和运算 add 指令是属于 R 指令类型：

- add 对应的 MIPS 指令⾥操作码是 000000 ，以及最末尾的功能码是 100000 ，这些数值都是固定 的，查⼀下 MIPS 指令集的⼿册就能知道的； 
- rs 代表第⼀个寄存器 R0 的编号，即 00000 ； 
- rt 代表第⼆个寄存器 R1 的编号，即 00001 ； 
- rd 代表⽬标的临时寄存器 R2 的编号，即 00010 ； 
- 因为不是位移操作，所以位移量是 00000
- 把上⾯这些数字拼在⼀起就是⼀条 32 位的 MIPS 加法指令了，那么⽤ 16 进制表示的机器码则是 0x00011020 。

## 指令周期

![](https://raw.githubusercontent.com/Cerbur/pic/main/20210723003209.png)

四个阶段的具体含义：

1. CPU 通过程序计数器读取对应内存地址的指令，这个部分称为 Fetch（取得指令）；
2. CPU 对指令进⾏解码，这个部分称为 Decode（指令译码）； 
3. CPU 执⾏指令，这个部分称为 Execution（执⾏指令）； 
4. CPU 将计算结果存回寄存器或者将寄存器的值存⼊内存，这个部分称为 Store（数据回写）； 

上⾯这 4 个阶段，我们称为指令周期（Instrution Cycle），CPU 的⼯作就是⼀个周期接着⼀个周期，周 ⽽复始。

## 指令的类型

指令从功能⻆度划分，可以分为 5 ⼤类：

- 数据传输类型的指令，⽐如 store/load 是寄存器与内存间数据传输的指令，mov 是将⼀个内存地址的数据移动到另⼀个内存地址的指令； 
- 运算类型的指令，⽐如加减乘除、位运算、⽐较⼤⼩等等，它们最多只能处理两个寄存器中的数据； 
- 跳转类型的指令，通过修改程序计数器的值来达到跳转执⾏指令的过程，⽐如编程中常⻅的 ifelse 、 swtich-case 、函数调⽤等。 
- 信号类型的指令，⽐如发⽣中断的指令 trap ； 
- 闲置类型的指令，⽐如指令 nop ，执⾏后 CPU 会空转⼀个周期；

## 优化程序执行的时间

![](https://raw.githubusercontent.com/Cerbur/pic/main/20210723003714.png)