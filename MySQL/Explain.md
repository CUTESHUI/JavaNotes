## Explain

#### Explain命令的基本使用

```mysql
mysql> explain select * from students;
+----+-------------+---------+------+---------------+------+---------+------+------+-------+
| id | select_type | table   | type | possible_keys | key  | key_len | ref  | rows | Extra |
+----+-------------+---------+------+---------------+------+---------+------+------+-------+
|  1 | SIMPLE      | students | ALL  | NULL          | NULL | NULL    | NULL |    1 | NULL  |
+----+-------------+---------+------+---------------+------+---------+------+------+-------+
1 row in set (0.03 sec)
```



#### id 

- 用来顺序标识整个查询中 SELELCT 语句
- 在嵌套查询中 id 越大的语句越先执行
- 该值可能为 NULL，那这一行说明的是其他行的联合结果



#### select_type

- SIMPLE
  - 简单子查询，不包含子查询和 UNION
- PRIMARY
  - 包含 UNION 或者子查询，最外层的部分标记为 PRIMARY
- SUBQUERY
  - 子查询中的第一个语句
- DERIVED
  - 派生表一一该临时表是从子查询派生出来的，位于 DERIVED

- UNION
  - 位于 UNION 中第二个及其以后的子查询被标记为 UNION
- UNION RESULT
  - 用来从匿名临时表里检索结果的 SELECT 被标记为 UNION RESULT
- DEPENDTENT UNION
  - 顾名思义，首先需要满足 UNION 的条件，及 UNION 中第二个以及后面的 SELECT 语句，同时该语句依赖外部的查询
- DEPENDENT SUBQUERY
  - 和 DEPENDENT UNION 相对 UNION 一样



#### table 

- 对应行正在访问哪一个表，表名或者别名
- 关联优化器会为查询选择关联顺序，左侧深度优先
- 当 from 中有子查询的时候，表名是 derivedN 的形式，N 指向子查询，即 explain 结果的下一列
- 当有 union result 的时候，表名是 union 1,2 等的形式，1,2 表示参与 union 的 query id
- 注意
  - MySQL 对待这些表和普通表一样，但是这些“临时表”是没有任何索引的



### type

- 显示的是访问类型，是较为重要的一个指标
- 结果值从好到坏依次是：system > const > eq_ref > ref > fulltext > ref_or_null > index_merge > unique_subquery > index_subquery > range > index > ALL 
- 一般来说，得保证查询至少达到 range 级别，最好能达到 ref



#### possible_keys 

- 显示查询使用了哪些索引，表示该索引可以进行高效地查找
- 但列出来的索引对于后续优化过程可能是没有用的



### key

- 显示 MySQL 实际决定使用的键（索引）
- 如果没有选择索引，键是 NULL
- 要想强制 MySQL 使用或忽视 possible_keys 列中的索引，在查询中使用 FORCE INDEX、USE INDEX 或者 IGNORE INDEX。



### key_len

- 显示 MySQL 决定使用的键长度
- 使用索引的长度
- 如果键是 NULL，则长度为 NULL
- 在不损失精确性的情况下，长度越短越好 



#### ref

- 显示使用哪个列或常数与 key 一起从表中选择行



#### rows

- rows 列显示 MySQL 认为它执行查询时必须检查的行数
- 这是一个预估值



#### Extra

- EXPLAIN 输出中另外一个很重要的列
- 显示 MySQL 在查询过程中的一些详细信息
- MySQL 查询优化器执行查询的过程中对查询计划的重要补充信息