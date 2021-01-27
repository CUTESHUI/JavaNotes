## Oracle

### CRUD

#### 查询

```
select *|列名|表达式 from 表名 where 条件 order by 列名;
```

- 备份查询数据

  - Oracle 可以利用create table（建表）的方式对 select 查询的结果进行快速备份

  - create table 表名 as select 语句;



#### 插入

```
insert into 表名（列名1,列名2,列名3.....）values（值1,值2,值3.....）;
```

- insert插入一个select的结果集

  - 在 Oracle 中，一个 INSERT 命令可以把一个 select 结果集一次性插入到一张表中

  ```
  insert into 表名（列名1,列名2,列名3.....）values（值1,值2,值3.....）;
  ```

  

#### 更新

```
update 表名 set 列名1=值1,列名2=值2,列名3=值3..... where 条件
```

- update 利用另外一张表关联更新本表数据的命令结构如下：

  ```
  update 表1 set 列名=（select 列名 from 表2 where 表1.列名=表2.列名）    where exists（select 1 from  表2 where 表1.列名=表2.列名)；
  ```

  

#### 删除

```
delete from 表名 where 条件
```

---

### 集合运算

- 集合运算就是把多个查询结果组合成一个查询结果

- INTERSECT（交集）

  - 返回两个查询共有的记录

  ```
  select * from 表1 
  intersect
  select * from 表2;
  ```

- UNION ALL（并集重复）

  - 返回各个查询的所有记录，包括重复记录

  ```
  select * from 表1 
  union all
  select * from 表2;
  ```

- UNION（并集不重复）

  - 返回各个查询的所有记录，不包括重复记录 （重复的记录只取一条）

  ```
  select * from 表1 
  union
  select * from 表2;
  ```

- MINUS（补集）

  - 返回第一个查询检索出的记录减去第二个查询检索出的记录之后剩余的记录

  ```
  select * from 表1 
  minus
  select * from 表2;
  ```

---

### 条件查询

- 条件查询时经常使用 =、IN、LIKE、BETWEEN...AND 来作为条件查询的操作符

- =
  - 表示列值等于一个固定值所查询出的结果
- IN
  - 查询其列值在指定的列表中的查询结果
- LIKE
  - 通过字符匹配检索出所需要的数据行
  - 字符匹配操作可以使用通配符“%”和“_”
- BETWEEN..AND
  - 查询列值包含在指定区间内的查询结果

---

### 子查询

- 子查询就是嵌套查询，他把select 查询的结果作为另外一个 select、update 或 delete 语句的条件，它的本质就是 where 条件查询中的一个条件表达式



#### 单行

- 向外部返回的结果为空或者返回一行
- 利用 where 条件 “=” 关联查询结果的

#### 多行

- 向外部返回的结果为空、一行、或者多行
- 可以用 IN 关键字来接收子查询的多行结果
- 可以用量化关键字 ANY、ALL 和关系运算符 >、>=、=、<、<= 来组合使用

---

### 运算符

#### 算术运算符

- 包括+、-、*、/四个，其中/获得的结果是浮点数



#### 关系运算符

| 符号 | 解释 | 符号     | 解释         |
| ---- | ---- | -------- | ------------ |
| =    | 等于 | <>或者!= | 不等于       |
| >    | 大于 | >=       | 大于或者等于 |
| <    | 小于 | <=       | 小于或者等于 |



#### 逻辑运算符

- AND、OR、NOT

---

### 伪列

- Oracle表在存储的过程中或查询的过程中，表会有一些附加列，称为伪列

- 伪列就像表中的字段一样，但是表中并不存储

- 伪列只能查询，不能增删改。

- Oracle的伪列有：ROWID、ROWNUM

- ORACLE ROWID

  - Oracle表中的每一行在数据文件中都有一个物理地址， ROWID 伪列返回的就是该行的物理地址
  - 使用 ROWID 可以快速的定位表中的某一行
  -  ROWID 值可以唯一的标识表中的一行
  - 通过 Oracle select 查询出来的 ROWID，返回的就是该行数据的物理地址

  ```
  select t.*,t.rowid from stuinfo t ;
  select t.*,t.rowid from stuinfo t where t.rowid='AAAShjAAEAAAAEFAAD';
  ```

- ORACLE ROWNUM

  - ORACLE ROWNUM 表示的 Oracle 查询结果集的顺序，ROWNUM 为每个查询结果集的行标识一个行号，第一行返回1，第二行返回2，依次顺序递增
  - 经常用来限制查询的结果返回的行数，求前几行或前几名的数据

  ```
  select t.stuid,t.stuname,t.sex,t.classno,t.stuaddress ,rownum  from stuinfo t ;
  ```

---

### 函数

#### 常用单行函数

- 字符串函数
  - 对字符串进行操作
  - 例如：TO_CHAR()、SUBSTR()、DECODE()等
- 数值函数
  - 对数值进行计算或操作，返回一个数字
  - 例如：ABS()、MOD()、ROUND()等
- 转换函数
  - 将一种数据类型转换成另外一种类型：
  - 例如：TO_CHAR()、TO_NUMBER()、TO_DATE()等
- 日期函数
  - 对时间和日期进行操作的函数
  - 例如：TRUNC()、SYSDATE()、ADD_MONTHS()等



#### 字符型函数

- 用来处理字符串类型的函数，通过接收字符串参数，然后经过操作返回字符串结果的函数



#### 日期型函数

- 操作日期、时间类型的相关数据，返回日期时间类型或数字类型结果
- 常用的有：SYSDATE()、ADD_MONTHS（）、LAST_DAY（）、TRUNC()、ROUND()等



#### 数值型函数

- 输入一个数值，并返回一个数值的函数



#### 转换函数

- 进行不同数据类型转换的函数，是我们平常数据库开发过程当中用的最多的内置函数
- 常用的有：to_char()、to_number()、to_date()等



#### 聚合函数

- 聚合函数同时可以对多行数据进行操作，并返回一个结果

- 常用的有：AVG()、SUM()、MIN()、MAX()等



#### 分析函数

- 专门用来解决具有复杂统计需求的函数
- 可以对数据进行分组然后基于组中数据进行分析统计，最后在每组数据集中的每一行中返回这个统计值
- 分析函数不同于分组统计（group by），group by 只能按照分组字段返回一个固定的统计值，但是不能在原来的数据行上带上这个统计值，而 Oracle 分析函数正是 Oracle 专门解决这类统计需求所开发出来的函数
- Oracle 分析函数都会带上一个开窗函数 over()

```
select table.column, 
Analysis_function()OVER(
[partition by 字段]
[order by 字段 [windos]]
) as 统计值
from table
```

- 分析
  - Analysis_function
    - 指定分析函数名，常用的分析函数有 sum、max、first_value、last_value、rank、row_number 等

- 常用的分析函数
  - first_value：返回组中数据窗口的第一个值
  - last_value：返回组中数据窗口的最后一个值
  - max：返回组中的最大值
  - min：返回组中的最小值



#### 开窗函数

```
over(partition by xxx order by yyy rows between zzz)
```

- 开窗函数名，partition by 指定进行数据分组的字段，order by 指定进行排序的字段，windos 指定数据窗口（即指定分析函数要操作的行数）

---

### synonym 同义词

- Oracle synonym 同义词是数据库当前用户通过给另外一个用户的对象创建一个别名，然后可以通过对别名进行查询和操作，等价于直接操作该数据库对象
- 按照访问权限分为私有同义词、公有同义词
  - 私有同义词
    - 只能当前用户可以访问，前提：当前用户具有 create synonym 权限
  - 公有同义词
    - 只能具有DBA用户才能进行创建，所有用户都可以访问

```
CREATE [OR REPLACE] [PUBLIC] SYSNONYM [当前用户.] synonym_name
 
FOR [其他用户.] object_name;
```

- 分析
  - create [or replace] 
    - 当前用户下同义词对象名已经存在的时候，就会删除原来的同义词，用新的同义词替代
  - [public]
    - 创建的是公有同义词，在实际开发过程中比较少用，因为创建就代表着任何用户都可以通过自己用户访问操作该对象，一般我们访问其他用户对象时，需要该用户进行授权给我们
  - 用户名.object_name
    - oralce 用户对象的权限都是自己用户进行管理的，需要其他用户的某个对象的操作权限，只能通过对象拥有者（用户）进行授权给当前用户
    - 当前用户具有系统管理员权限（DBA），即可通过用户名.object_name 操作该对象

- 同义词删除
  - 只能通过同义词拥有者的用户或者具有 DBA 权限的用户才能删除

```
DROP [PUBLIC] SYNONYM [用户.]sysnonym_name;
```

---

### 序列

- Oracle 序列 Sequence 是用来生成连续的整数数据的对象，它经常用来作为业务中无规则的主键
- Oracle 序列可以是升序列、降序列

```
CREATE SEQUENCE sequence_name
[MAXVALUE num|NOMAXVALUE]
[MINVALUE num|NOMINVALUE]
[START WITH num]
[INCREMENT BY increment]
[CYCLE|NOCYCLE]
[CACHE num|NOCACHE]
```

- 分析
  - MAXVALUE/MINVALUE
    - 指定的是序列的最大值和最小值
  - NOMAXVALUE/NOMINVALUE
    - 不指定序列的最大值和最小值，使用系统的默认选项
    - 升序的最大值：10^27次方，降序是-1
    - 升序最小值：1，降序的最小值：-10^26
  - START WITH
    - 指定从某一个整数开始，升序默认是1，降序默认是-1
  - CYCLE | NOCYCLE
    - 表示序列达到最大值或者最小值的时候，是否重新开始
    - CYCLE：重新开始，NOCYCLE：不重新开始
  - CACHE
    - 使用 CACHE 选项时，该序列会根据序列规则预生成一组序列号。保留在内存中，当使用下一个序列号时，可以更快的响应
    - 当内存中的序列号用完时，系统再生成一组新的序列号，并保存在缓存中，这样可以提高生成序列号的效率 
  - NOCACHE
    - 不预先在内存中生成序列号

---

### 视图

- oracle 视图可以理解为数据库中一张虚拟的表，他是通过一张或者多张基表进行关联查询后组成一个虚拟的逻辑表
- 查询视图，本质上是对表进行关联查询

- 作用
  - 使数据简单化
  - 表结构设计的补充
  - 增加安全性



#### 物化视图

- 基于 selec t查询结果的数据副本，简单来讲就是数据的复制表，但是它可以按照特定的刷新策略刷新复制表中的数据

- 作用
  - 用来当做远程数据表的本地化，可以用来做数据同步用
  - 用来做本地单表或多表数据的汇总
  - 可以指定刷新的间隔来执行定时刷新物化表的数据

---

### 索引

- 什么时候创建
  
- 
  
- 类别

  - b-tree 索引
    - Oracle 数据中最常见的索引，就是 b-tree 索引，create index 创建的 normal 就是 b-tree 索引，没有特殊的必须应用在哪些数据上
  - bitmap 位图索引
    - 位图索引经常应用于列数据只有几个枚举值的情况
    - 比如性别字段，或者我们经常开发中应用的代码字段这个时候使用 bitmap 位图索引，查询效率将会最快
  - 函数索引
    - 比如经常对某个字段做查询的时候经常是带函数操作的，那么此时建一个函数索引就有价值了
  - hash 索引
    - hash 索引可能是访问数据库中数据的最快方法，但它也有自身的缺点
    - 创建hash索引必须使用hash集群，相当于定义了一个 hash 集群键，通过这个集群键来告诉 oracle 来存储表。因此，需要在创建 hash 集群的时候指定这个值
    - 存储数据时，所有相关集群键的行都存储在一个数据块当中，所以只要定位到 hash 键，就能快速定位查询到数据的物理位置
  - reverse 反向索引
    - 这个索引不经常使用到，但是在特定的情况下，是使用该索引可以达到意想不到的效果。如：某一列的值为{10000,10001,10021,10121,11000,....}，假如通过b-tree索引，大部分都密集发布在某一个叶子节点上，但是通过反向处理后的值将变成{00001,10001,12001,12101,00011,...}，很明显的发现他们的值变得比较随机，可以比较平均的分部在各个叶子节点上，而不是之前全部集中在某一个叶子节点上，这样子就可大大提高检索的效率
  - 分区索引和分区表的全局索引

- 创建

  ```
  create[unique]|[bitmap] index index_name --UNIQUE表示唯一索引、BITMAP位图索引
  on table_name(column1,column2...|[express])--express表示函数索引
  [tablespace tab_name] --tablespace表示索引存储的表空间
  [pctfree n1]    --索引块的空闲空间n1
  [storage         --存储块的空间
   (
      initial 64K  --初始64k
      next 1M
      minextents 1
      maxextents unlimited
  )];
  ```
  
- 分析
    - UNIQUE：指定索引列上的值必须是唯一的，称为唯一索引
    - BITMAP：位图索引
    - index_name：指定索引名
    - tabl_name：指定要为哪个表创建索引
    - column_name：指定要对哪个列创建索引。我们也可以对多列创建索引，这种索引称为组合索引，也可以是函数表达式，这就是函数索引
  
- 修改

  - 重命名索引
  - 合并索引、重新构造索引

  ```
  alter index index_name coalesce; --合并索引
  alter index index_name rebuild;  --重新构造
  ```

- 删除

  ```
  drop index index_name;
  ```

- 查看

  ```
  select t.INDEX_NAME,--索引名字
  	t.index_type,--索引类型
    t.TABLESPACE_NAME,--表空间
    t.status,--状态
    t.UNIQUENESS--是否唯一索引
  from all_indexes T 
  where  t.INDEX_NAME='index_name';
  ```

---

### 分区

- 作用
  - 改善大型表的查询性能
    - 可以通过查询对应分区表中对应的数据，而不需要查询整个表
  - 表更容易管理，
    - 分区表的数据存储在各个分区中，所以可以按照分区建，来管理对应分区当中的数
    - 可以按照分区加载和删除其中的数据，比在不分区情况下，更容易管理数据
    - 在特定的事故情况下，通过备份好的分区，可以快速恢复对应分区当中的数据，也不需要对全表数据进行恢复
- 创建分区
  - Oracle 分区也是通过 create table 命令组成，但是对表进行分区时，得考虑一个字段作为分区建，通常按值的范围来划分分区

---

### merge into

- “有则更新，无则插入”
- 在实际开发过程中，我们会经常遇到这种通过两表互相关联匹配更新其中一个表的某些字段的业务，有时还要处理不匹配的情况下的业务。这个时候你会发现随着表的数据量增加，类似这种业务场景的执行效率会比较慢，那是因为你需要多次重复查询两表中的数据

```
merge into A 
using B 
on (A.id = B.id)
when matched then
  update set A.col=B.col
when not matched then
  insert 语句;
```

- 分析
  - 利用 B表通过 A.id=B.id 的条件来匹配A表，当满足条件时，可以对 A表进行更新，当不满足条件时，可以利用 inert 语句插入相关数据

---

### 行转列

- 把某一个字段的值作为唯一值，然后另外一个字段的行值转换成它的列值
- Oracle11g之后提供了自带函数 PIVOT 可以完美解决这个行转列的需求

```
SELECT * FROM （数据查询集）
PIVOT 
(
 SUM(Score/*行转列后 列的值*/) FOR 
 coursename/*需要行转列的列*/ IN (转换后列的值)
)
```



### 列转行

- 把一行当中的列的字段按照行的唯一值转换成多行数据
- Oracle11g之后提供了自带函数 UNPIVOT 可以完美解决这个行转列的需求

```
select 字段 from 数据集
unpivot（自定义列名/*列的值*/ for 自定义列名 in（列名））
```

---

#### 细节

```ora
进入回收站
drop table users;

查询回收站中的对象
show recyclebin;

闪回，即将回收站还原
flashback table 表名 to before drop;
flashback table 表名 to before drop rename to  新表名;

彻底删除users表
drop table users purge;

清空回收站
purge recyclebin;

为emp表增加image列，alter table 表名 add 列名 类型(宽度) 
alter table emp
add image blob;

修改ename列的长度为20个字节，alter table 表名 modify 列名 类型(宽度) 
alter table emp
modify ename varchar2(20);

删除image列，alter table 表名 drop column 列名
alter table emp
drop column image;

重名列名ename为username，alter table 表名 rename column 原列名 to 新列名
alter table emp
rename column ename to username;

将emp表重命名emps，rename 原表名 to 新表名
rename emp to emps;
```
