# MySQL ACID如何保证

A 原子性： 使用 undo log 保证，他记录了要回滚的数据。

C 一致性：通过其他3大特性保证，一个是二阶段提交。

I 隔离性： MVCC

D 持久性：redo log，binlog。WAL，先写日志。宕机可以从redolog恢复。

redo log 写盘，事务进入 prepare 状态

prepare 成功 binlog 写盘

redo log commit

二阶段提交

