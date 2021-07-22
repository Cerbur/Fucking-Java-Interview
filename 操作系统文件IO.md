# 操作系统文件IO

## 缓冲与非缓冲IO

缓冲 I/O，利⽤的是标准库的缓存实现⽂件的加速访问，⽽标准库再通过系统调⽤访问⽂件。 

⾮缓冲 I/O，直接通过系统调⽤访问⽂件，不经过标准库缓存。

## 直接与非直接IO

直接 I/O，不会发⽣内核缓存和⽤户程序之间数据复制，⽽是直接经过⽂件系统访问磁盘。 

⾮直接 I/O，读操作时，数据从内核缓存中拷⻉给⽤户程序，写操作时，数据从⽤户程序拷⻉给内核 缓存，再由内核决定什么时候写⼊数据到磁盘。

## 阻塞与非阻塞IO

阻塞式IO

![](https://raw.githubusercontent.com/Cerbur/pic/main/20210723041500.png)





非阻塞IO

![](https://raw.githubusercontent.com/Cerbur/pic/main/20210723041518.png)

## 同步与异步IO

非阻塞IO需要一直轮训是否完成数据读取，为了解决这种傻乎乎轮询⽅式，于是 **I/O 多路复⽤技术**就出来了，如 select、poll，它是通过 I/O 事件分 发，当内核数据准备好时，再以事件通知应⽤程序进⾏操作。

![](https://raw.githubusercontent.com/Cerbur/pic/main/20210723041721.png)



异步IO

![image-20210723041827818](/Users/cerbur/Library/Application Support/typora-user-images/image-20210723041827818.png)

## 总结

![](https://raw.githubusercontent.com/Cerbur/pic/main/20210723041841.png)

