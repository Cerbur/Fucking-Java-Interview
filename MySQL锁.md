# MySQL锁

## 通过属性划分

共享锁，排他锁

## 粒度划分

行锁

表锁

记录锁

​	行锁的一种，命中条件唯一索性

页锁

​	表锁行锁之间

间隙锁 

​	next 行锁的一种，锁的是区间 (1,4]

临键锁

​	next-key [1,4]

## 状态

意向共享锁，意向排他锁

