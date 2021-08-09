# MySQL执行计划

使用 explain 指令

核心看 type 字段 

- const通过索引命中一行数据、一次命中
- system 表中只有一行数据
- eq_ref 使用唯一索引
- ref 非唯一索引，且可能回表
- range 一般用于between、 >、 < 使用一个索引来选择行
- index 只遍历索引树 （避免
- ALL 全表扫描 （避免

possible_key 可能会走的索引

key 真正走的索引

key_len 联合索引 看多少字段命中索引

ref 命中的索引字段名

rows 读取多少行数据

filtered 过滤，读取行数与最终结果的百分比

extra 对结果集排序的时候走没走索引 using index 使用了覆盖索引