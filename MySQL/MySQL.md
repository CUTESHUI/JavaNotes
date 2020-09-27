## MySQL

#### 什么是数据库

- 数据库是持久化数据的一种介质，可以理解成用来储存和管理数据的仓库
- 持久化：把数据保存到可掉电式存储设备中以供之后使用。
- 持久化的大多数时候是将内存中的数据存储在数据库中，当然也可以存储在磁盘文件、XML数据文件中



#### 为什么要用数据库

- 可将数据持久化到硬盘
- 可存储大量数据
- 方便检索
- 保证数据的一致性、完整性
- 安全，可共享
- 通过组合分析，可以产生新数据



#### 数据库相关概念

- DB
  - database
  - 数据库
  - 存储数据的“仓库”，它保存了一系列有组织的数据
- DBMS
  - Database Management System 
  - 数据库管理系统
  - 数据库是通过 DBMS 创建和操作的容器
- SQL
  -  Structure Query Language 
  - 结构化查询语言
  - 专门用来与数据库通信的语言



#### 数据库存储数据的特点

- 数据放到表中，表再放到库中 
-  一个数据库中可以有多个表，每个表都有一个的名字，用来标识自己
- 表名具有唯一性
- 表具有一些特性，这些特性定义了数据在表中如何存储，类似 java 中 “类”的设计
-  表由列组成，我们也称为字段。所有表都是由一个或多个列组成的，每一列类似 java 中的”属性”
- 表中的数据是按行存储的，每一行类似于 java 中的“对象”

- 类比
  - 表——类
  -  列，字段——属性
  -  行——对象



#### MySQL介绍

- MySQL是一种开放源代码的关系型数据库管理系统
- 开发者为瑞典MySQL AB公司。在2008年1月16号被Sun公司收购，而2009年，SUN又被Oracle收购
- 目前 MySQL被广泛地应用在Internet上的中小型网站中
- 由于其体积小、速度快、总体拥有成本低，尤其是开放源码这一特点，许多中小型网站为了降低网站总体拥有成本而选择了MySQL作为网站数据库（Facebook, Twitter, YouTube）
- 阿里提出“去IOE”，更多网站也开始选择MySQL



#### MySQL优点

- 成本低：开放源代码，一般可以免费试用 
- 性能高：执行很快 
- 简单：很容易安装和使用 



## SQL

#### SQL是什么

- SQL（Structured Query Language）是“结构化查询语言”，它是对关系型数据库的操作语言。它可以应用到所有关系型数据库中，例如：MySQL、Oracle、SQL Server等。
- SQL标准有：
  - 1986年，ANSI X3.135-1986，ISO/IEC 9075:1986，SQL-86
  - 1989年，ANSI X3.135-1989，ISO/IEC 9075:1989，SQL-89
  - 1992年，ANSI X3.135-1992，ISO/IEC 9075:1992，SQL-92（SQL2）
  - 1999年，ISO/IEC 9075:1999，SQL:1999（SQL3）
  - 2003年，ISO/IEC 9075:2003，SQL:2003
  - 2008年，ISO/IEC 9075:2008，SQL:2008
  - 2011年，ISO/IEC 9075:2011，SQL:2011
  - 2016年，ISO/IEC 9075:2016，SQL:2016
- 这些标准就与 JDK 的版本一样，在新的版本中总要有一些语法的变化，不同时期的数据库对不同标准做了实现
- 虽然 SQL 可以用在所有关系型数据库中，但很多数据库还都有标准之后的一些语法，我们可以称之为“方言”
- 例如 MySQL 中的 LIMIT 语句就是MySQL独有的方言，其它数据库都不支持！当然，Oracle 或 SQL Server 都有自己的方言



#### SQL语法要求

- SQL语句可以单行或多行书写，以 " ; " 结尾
- 可以用空格和缩进来来增强语句的可读性
- 关键字不区别大小写，建议使用大写



#### 分类

- DDL
  - Data Definition Language
  - 数据定义语言
  - 用来定义数据库对象：库、表、列等
  - create / drop / alter

- DML
  - Data Manipulation Language
  - 数据操作语言
  - 用来定义数据库记录 / 数据
  - insert / update / delete

- DCL

  - Data Control Language

  - 数据控制语言

  - 用来定义访问权限和安全级别

- lDQL

  - Data Query Language

  - 数据查询语言
  - 用来查询记录（数据）
  - select



#### DQL

- DQL就是数据查询语言
- 数据库执行DQL语句不会对数据进行改变，而是让数据库发送结果集给客户端
- 语法：

  - SELECT 
    - selection_list 
    - 要查询的列名称

  - FROM 
    - table_list 
    - 要查询的表名称

  - WHERE 
    - condition 
    - 行条件

  - GROUP BY 
    - grouping_columns 
    - 对结果分组

  - HAVING 
    - condition 
    - 分组后的行条件

  - ORDER BY
    - sorting_columns 
    - 对结果分组

  - LIMIT 
    - offset_start，row_count 
    - 结果限定



#### 查询

- 语法

  - select 查询列表 from 表名;
- 特点
  - 查询的结果集是一个虚拟表
  - select 类似于 System.out.println( );
  - 查询列表可以由多个部分组成，中间用 “ ，” 隔开
    - select 字段1, 字段2, ... , 表达式 from 表名;
  - 防止表名、列名等与关键字重复，可在名上加：`` ( 着重号 )
  - 非数值类型的常量在查询时，都用 ‘ ’ 
- 执行顺序
  1. from
  2. select
- 查询列表
  - 字段
  - 表达式
  - 常量
  - 函数



- **基本查询**

  - 查询常量

    - select 100;  
    - // 100

  - 查询表达式

    - select 100 + 1;
    - // 101

  - 查询单个字段

    - select id from stuinfo;

  - 查询多个字段

    - select id, name from stuinfo;

  - 查询所有字段

    - select * from stuinfo;

  - 查询函数

    - 用法：select 函数;

  - database( )：显示当前use的数据库

    - version( )：显示当前数据库的版本
    - user( )：显示

  - 起别名

    1. 使用 as
       - select user( ) as 用户名 from 表名;
       - select user( ) as "用户名 from 表名";
       - select user( ) as "用户名 from 表名";

    2. concat( ) as
       - select concat ( id, name ) as "info" from 表名

    - 当起的名字会引发歧义，就一定要用 " " / ' '
    - 就近字段

  - 查询表达结构

    - desc 表名;
    - show column from 表名;



- **条件查询**
- 语法

  - select 查询列表 from 表名 where 条件;

- 条件

  1. 按关系表达式筛选
     -  != / <>、 <、 <=、 >、 >=
     -  可以用 !=，但不建议

  2. 按逻辑表达式筛选
     - AND、 OR、 NOT

  3. 模糊筛选
     - BETWEEN…AND、 IN(set)、 IS NULL



- 模糊查询

  1. like

  - 一般和通配符一起使用，对字符型数据进行部分匹配查询
  - 通配符

    - _  ：任意单个字符
    - % ：任意多个字符

  - 举例

    1. 查询name含字符a的员工信息
       - select * form employees where like '%a%';
       - '%a%'：因为a是字符，用单引号。不确定a前后有几个字符，所以a前后都用%
    2. 查询name第一个含字符a的员工信息
       - select * from employees where like 'a%';
       - 'a%'：不确定a后面有几个字符，a又作为第一个，所以a前面空着 后面用%
    3. 查询name第三个含字符a的员工信息
       - select * from employees where like '___a%';
       - '___a%'：第三个，所以a前面三个 _
    4. 查询name第二个为_的员工信息
       - select * from employees where like '_ $ _' escape $;
       - '_ $ _' escape $：令$为转义字符，代表$后的 _ 除了通用符也代表下划线本身的意思

  2. in

  - 查询谋字段后的值是否属于指定的列表内
  - a in， a not in ( 常量1, 常量2, ... )
  - 举例
    1. 查询部门编号为30、40、50的员工名和部门编号
       - select last_name, department_id from employees where department_id in (30, 40, 50 );
    2. 查询部门编号不为30的员工信息
       - select * from employees where department_id not in ( 30 );
       - select * from employees where not (department_id = 30 );

  3. between...and

  - 判断某个字段的值是否介于...与...之间
  - between... and / not between... and
  - 举例
    1. 查询部门编号为30-90的员工信息
       - select * from employee where between 30 and 90;

  4. is null / is not null

  - is 和 =
    - = 只能判断普通内容
    - is 只能判断 null

  - 举例
    1. 查询没有奖金的员工
       - select * from employee where commission_pct is null;




- **排序查询**
- 语法
  - select 查询列表 from 表名 where 条件 order by 排序列表;
  - 执行顺序
    1. from
    2. where
    3. select
    4. order by
  - 排序列表
    - 单个字段、多个字段、表达式、函数、列数、以及以上的组合
    - 升序：asc， 默认
    - 降序：desc

- 按单个字段排序
  - 将员工工资大于12000的按升序排列
  - select * from employees where salary > 12000 order by salary asc;
  - 按单个字段排序
    - 先按工资升序，再按部门编号降序
    - select * from employees order by salary asc, department_id desc;
  - 按表达式排序
    - 对有奖金的员工，按年薪升序排列
    - select * from employees where commission_pec is not null order by salary*12 *(1+ ifnull(commission_pec, "")) asc;
  - 按函数排序
    - 按姓名的字符长度进行升序排列
    - select length(last_name), last_ name from employees order by length(last_name) asc;
  - 按列数排序
    - 按第2列升序排列
    - select * from employees order by 2;




- MySQL 的 + 的作用
  - 加法运算
  - 两个操作数都是数值型，正常运算
    - 100 + 2
    - // 102
  - 其中一个操作数是字符型，将字符型数据强制转换为数值型，如果无法转换，直接当 0 处理
    - 100 + “Tom" 
    - // 100
    - 100 + "a"
    - // 197
  - 其中一个操作数是 null，结果都为 null
    - null + null / 100
    - // null



- **分组查询**

  - 当需要分组查询时需要使用GROUP BY子句，例如查询每个部门的工资和，这说明要使用部分来分组
  - 每个、各个
  - 分组前的筛选：where，就是 from where select  的执行顺序
  - 分组后的筛选：having，就是 from select having 的执行顺序
  - 排序在分组后
  - 语法
    - select 查询列表 from 表名 where 条件 group by 分组列表
      1. 查询列表：一般是 分组函数 和 分组列表
      2. 分组列表
  - 执行顺序
    1. from 
    2. where
    3. group by
    4. having
    5. select
    6. order by
  - 举例
    1. 查询每个工种的员工平均工资
       - select avg(salary) avg_salary, job_id from employees group by job_id;
    2. 查询邮箱中包含a字符的每个部门的最高工资
       - select max(salary) max_salary, department_id from employees where email like '%a%'  group by department_id;
    3. 查询有奖金的每个领导手下员工的平均工资
       - select avg(salary) avg_salary, manager_id from employees where commission_pct is not null group by manager_id; 
    4. 查询哪个部门的员工个数 >5
       - select count(*), department_id from employees group by department_id having count( *) > 5;
    5. 每个工种有奖金的员工的最高工资 >12000的工种编号和最高工资
       - select max(salary) max_salary, job_id from employees where commission_pec is not null group by job_id having max(salary) > 12000;
    6. 领导编号>102的每个领导手下的最低工资 >5000的领导编号和最低工资
       - select manager_id, min(salary) from employees where manager_id > 102 group by manager_id having min(salary)  > 5000;
    7. 每个工种有奖金的员工的最高工资>6000的工种编号和最高工资，按最高工资升序
       - select job_id, max(salary) max_salary from employees where commission_pec is not null group by job_id order by max(salary);

  



- **连接查询**

  SQL 92 语法

  - 内连接

    1. 语法
       - elect 查询列表 from 表名 where 等值连接条件 and 筛选条件 order by 分组列表 having 分组后筛选条件 order by 排序列表;
    2. 特点：查询结果必须满足条件

  - 等值连接

    1. 语法
       - select 查询列表 from 表名 where 等值连接条件 and 筛选条件;

    2. 特点
       - 为了解决多个表中字段名重名的问题，一般会起别名，提高了语义性
       - 表的顺序无要求
    3. 举例
       - 查询员工名和部门名
       - select last_name, department_name from employees e, departments d where e.department_id = d.department_id;

  - 非等值连接

  - 自连接

  SQL 99 语法

  - 内连接

    1. 语法
       - select 查询列表 表名1 别名 inner join 表名2 别名 join 表名3 别名 ... on 连接条件 where 筛选条件 group by 分组条件 having 分组后筛选条件 order by 排序条件;
       - inner 可以省略
    2. 特点：查询结果必须满足条件
  - 等值连接

    1. 举例
       - 查询员工名和部门名
       - select last_name, department_name from employees e inner join departments d on e.department_id = d.department_id;
  - 非等值连接

    1. 举例
       - 查询部门编号在10-90之间的员工的工资级别，并按级别进行分组
       - select count(*) count, grade from employees e inner join salary_grade g on e,salary between g.min_salary and g.max_salary where e.department_id between 10 and 90 group by g.grade;
  - 外连接
    1. 特点
       - 查询出的结果存在不满足条件的可能
       - 分主从表，两表的顺序不能任意调换
       - 左连接的话，左为主
       - 右连接的话，右为主
    2. 说明
       - SQL99 才有
       - 查询结果为主表中所有的记录，如果从表有匹配项，则显示匹配项，没有显示 null
    3. 应用场景
       - 一般用于查询主表中有，但从表中没有的 记录
    4. 语法
       - select 查询列表 from 表名1 别名 left / right outer join 表名2 别名 on 连接条件 where 筛选条件;
       - outer 可以省略
    5. 举例
       - 查询所有的女神记录，以及对应的男神名字，如果没有显示 null
       - select g.name, b.boyName from beauty g left outer join boys b on g.boyfriend_id = b.id;
       - select g.name, b.boyName from boys b right outer join beauty g on g.boyfriend_id = b.id;
       - 查询哪个女神没有男神
       - select g.*, b.boyName from beauty g left outer join boys b on g.boyfriend_id = b.id where b.id is null;
  - 自然连接
    1. 举例
       - 查询员工名和部门名
       - select last_name, department _name from employees e inner join departments d on e.department_id = d.department_id;




- **子查询**
  - 当一个查询中又嵌套了另一个完整的 select 语句，则被嵌套的被称为子查询 / 内查询
  - 外面的称 主查询 / 外查询
  - 子查询不一定必须出现在 select 语句内部
  - 分类
    - select 后面：子查询的结果为单行单列 ( 标量子查询 )
    - from 后面：子查询可以为多行多列
    - where、having 后面：子查询结果为单列
    - exists 后面：子查询结果为单列 ( 相关子查询 )
  - 特点
    - 子查询放在条件中，必须放在条件的右侧
    - 子查询一般放在小括号中
    - 子查询的执行一般优于主查询
    - 单行子查询对应了单行操作符：>、<、>=、<=、=、<>
    - 多行子查询对应了多行操作符：in 、any 、all 、not in
    - 子查询中写完不用 “ ；”
    - LIMIT 用来限定查询结果的起始行，以及总行数
  - 多行操作符
    - in ：判断某字段是否在指定列表中
    - any ：判断某字段的值是否满意指定列表中之一
    - all ：判断某字段的值是否满意指定列表中全部



- **分页查询**

  - 当页面上的数据，一页显示不全，需要分页显示

  - 分页查询的SQL命令请求数据库服务器 —> 服务器响应查到的多条数据 —> 前台页面

  - 特点

    - 起始条目如果不写，默认为0
    - 实际要显示的起始-1，比如显示第11条，则 limit 10

  - 语法

    - select 查询列表 from 表名1 别名 join 表名2 别名 on 连接条件 where 筛选条件 group by 分组条件 having 分组后筛选条件order by 排序条件 limit 起始条目索引，显示条目数量;	

  - 执行顺序

    1. from
    2. join
    3. on
    4. where
    5. group by
    6. having
    7. select
    8. order by
    9. limit

  - 举例

    1. 查询员工的前5条

    - select * from employees limit 0,5;
    - select * from employees limit 5;

    2. 查询有奖金且工资较高的第11条——第20条的员工信息
       - select * from employees where commission_pct is not null order by salary desclimit 10,10;
    3. 查询有奖金的工资最高的前三名员工名、工资、奖金、部门编号

- **联合查询**

  - 当查询的结果来自多张表，表之间没有关联，这时候往往使用联合查询 / union 查询
  - 特点
    - union 自动去重
    - union all 可以重复
    - 多条联合查询语句的查询列数必须一致，查询 类型、字段意义最好一致
  - 语法
    - select 查询列表 from 表名1 where 筛选条件 union select 查询列表 from 表名2 where 筛选条;



#### 常见函数

- MySQL数据库提供了很多函数
  - 数学函数
  - 符串函数
  - 日期和时间函数
  - 条件判断函数；流程控制函数
  - 系统信息函数
  - 加密函数
  - 格式化函数

- 数学函数

| 函数名        | 说明                                 |
| ------------- | :----------------------------------- |
| ABS（x）      | 返回x的绝对值                        |
| CEIL（x）     | 返回大于x的最小整数值                |
| FLOOR（x）    | 返回大于x的最大整数值                |
| MOD(x,y)      | 返回x/y的模                          |
| RAND(x)       | 返回0~1的随机值                      |
| ROUND(x,y)    | 返回参数x的四舍五入的有y位的小数的值 |
| TRUNCATE(x,y) | 返回数字x截断为y位小数的结果         |
| SQRT(x)       | 返回x的平方根                        |
| POW(x,y)      | 返回x的y次方字符串函数               |

- 字符串函数

| 函数名                          | 说明                                                         |
| ------------------------------- | ------------------------------------------------------------ |
| CONCAT(S1,S2,......,Sn)         | 连接S1,S2,......,Sn为一个字符串                              |
| CONCAT(s, S1,S2,......,Sn)      | 同CONCAT(s1,s2,...)函数，但是每个字符串之间要加上s           |
| CHAR_LENGTH(s)                  | 返回字符串s的字符数                                          |
| LENGTH(s)                       | 返回字符串s的字节数，和字符集有关，一般：一个字符占3个字节   |
| INSERT(str, index , len, instr) | 将字符串str从第index位置开始，len个字符长的子串替换为字符串instr |
| UPPER(s) 或 UCASE(s)            | 将字符串s的所有字母转成大写字母                              |
| LOWER(s)  或LCASE(s)            | 将字符串s的所有字母转成小写字母                              |
| LEFT(s,n)                       | 返回字符串s最左边的n个字符                                   |
| RIGHT(s,n)                      | 返回字符串s最右边的n个字符                                   |
| LPAD(str, len, pad)             | 用字符串pad对str最左边进行填充，直到str的长度为len个字符     |
| RPAD(str ,len, pad)             | 用字符串pad对str最右边进行填充，直到str的长度为len个字符     |
| LTRIM(s)                        | 去掉字符串s左侧的空格                                        |
| RTRIM(s)                        | 去掉字符串s右侧的空格                                        |
| TRIM(s)                         | 去掉字符串s开始与结尾的空格                                  |
| TRIM(【BOTH 】s1 FROM s)        | 去掉字符串s开始与结尾的s1                                    |
| TRIM(【LEADING】s1 FROM s)      | 去掉字符串s开始处的s1                                        |
| TRIM(【TRAILING】s1 FROM s)     | 去掉字符串s结尾处的s1                                        |
| REPEAT(str, n)                  | 返回str重复n次的结果                                         |
| REPLACE（str, a, b）            | 用字符串b替换字符串str中所有出现的字符串a                    |
| STRCMP(s1,s2)                   | 比较字符串s1, s2                                             |
| SUBSTRING(s,index,len)          | 返回从字符串s的 index位置其len个字符，SQL的起始索引为1       |

- 时间日期函数

| 函数名                                                       | 说明                                                   |
| ------------------------------------------------------------ | ------------------------------------------------------ |
| CURDATE() 或 CURRENT_DATE()                                  | 返回当前日期                                           |
| CURTIME() 或 CURRENT_TIME()                                  | 返回当前时间                                           |
| NOW() SYSDATE()CURRENT_TIMESTAMP()LOCALTIME()LOCALTIMESTAMP() | 返回当前系统日期时间                                   |
| YEAR(date)MONTH(date)DAY(date)HOUR(time)MINUTE(time)SECOND(time) | 返回具体的时间值                                       |
| WEEK(date)WEEKOFYEAR(date)                                   | 返回一年中的第几周                                     |
| DAYOFWEEK()                                                  | 返回周几，注意：周日是1，周一是2，。。。周六是7        |
| WEEKDAY(date)                                                | 返回周几，注意，周1是0，周2是1，。。。周日是6          |
| DAYNAME(date)                                                | 返回星期：MONDAY,TUESDAY.....SUNDAY                    |
| MONTHNAME(date)                                              | 返回月份：January，。。。。。                          |
| DATEDIFF(date1,date2)TIMEDIFF(time1, time2)                  | 返回date1 - date2的日期间隔返回time1 - time2的时间间隔 |
| DATE_ADD(datetime, INTERVALE  expr  type)                    | 返回与给定日期时间相差INTERVAL时间段的日期时间         |
| DATE_FORMAT(datetime ,fmt)                                   | 按照字符串fmt格式化日期datetime值                      |
| STR_TO_DATE(str, fmt)                                        | 按照字符串fmt对str进行解析，解析为一个日期             |

DATE_ADD(datetime,INTERVAL  expr  type)

| SELECT DATE_ADD(NOW(), INTERVAL 1 YEAR);SELECT DATE_ADD(NOW(), INTERVAL -1 YEAR);  #可以是负数SELECT DATE_ADD(NOW(), INTERVAL '1_1' YEAR_MONTH);  #需要单引号 |               |
| ------------------------------------------------------------ | ------------- |
| 表达式类型                                                   | YEAR_MONTH    |
| YEAR                                                         | DAY_HOUR      |
| MONTH                                                        | DAY_MINUTE    |
| DAY                                                          | DAY_SECOND    |
| HOUR                                                         | HOUR_MINUTE   |
| MINUTE                                                       | HOUR_SECOND   |
| SECOND                                                       | MINUTE_SECOND |

DATE_FORMAT(datetime ,fmt)和STR_TO_DATE(str, fmt)

| 格式符 | 说明                                                        | 格式符 | 说明                                                        |
| ------ | ----------------------------------------------------------- | ------ | ----------------------------------------------------------- |
| %Y     | 4位数字表示年份                                             | %y     | 表示两位数字表示年份                                        |
| %M     | 月名表示月份（January,....）                                | %m     | 两位数字表示月份（01,02,03。。。）                          |
| %b     | 缩写的月名（Jan.，Feb.，....）                              | %c     | 数字表示月份（1,2,3,...）                                   |
| %D     | 英文后缀表示月中的天数（1st,2nd,3rd,...）                   | %d     | 两位数字表示月中的天数(01,02...)                            |
| %e     | 数字形式表示月中的天数（1,2,3,4,5.....）                    |        |                                                             |
| %H     | 两位数字表示小数，24小时制（01,02..）                       | %h和%I | 两位数字表示小时，12小时制（01,02..）                       |
| %k     | 数字形式的小时，24小时制(1,2,3)                             | %l     | 数字形式表示小时，12小时制（1,2,3,4....）                   |
| %i     | 两位数字表示分钟（00,01,02）                                | %S和%s | 两位数字表示秒(00,01,02...)                                 |
| %W     | 一周中的星期名称（Sunday...）                               | %a     | 一周中的星期缩写（Sun.，Mon.,Tues.，..）                    |
| %w     | 以数字表示周中的天数(0=Sunday,1=Monday....)                 |        |                                                             |
| %j     | 以3位数字表示年中的天数(001,002...)                         | %U     | 以数字表示年中的第几周，（1,2,3。。）其中Sunday为周中第一天 |
| %u     | 以数字表示年中的第几周，（1,2,3。。）其中Monday为周中第一天 |        |                                                             |
| %T     | 24小时制                                                    | %r     | 12小时制                                                    |
| %p     | AM或PM                                                      | %%     | 表示%                                                       |

- 流程函数

| 函数名                                                       | 说明                                         |
| ------------------------------------------------------------ | -------------------------------------------- |
| IF(value,t ,f)                                               | 如果value是真，返回t，否则返回f              |
| IFNULL(value1, value2)                                       | 如果value1不为空，返回value1，否则返回value2 |
| CASE WHEN 条件1 THEN 结果1WHEN 条件2 THEN 结果2 ...ELSE 结果 END | 相当于 Java 的 if...else if...               |
| CASE  expr WHEN 常量值1 THEN 值1WHEN 常量值1 THEN 值1....[ELSE 值n]END | 相当于 Java 的 switch                        |

- 分组 / 聚合函数
  - 聚合函数是用来做纵向运算的函数
  - 函数
    - COUNT( )：统计指定列不为NULL的记录行数
    - MAX( )：计算指定列的最大值，如果指定列是字符串类型，那么使用字符串排序运算
    - MIN()：计算指定列的最小值，如果指定列是字符串类型，那么使用字符串排序运算
    - SUM( )：计算指定列的数值和，如果指定列类型不是数值类型，那么计算结果为0
    - AVG( )：计算指定列的平均值，如果指定列类型不是数值类型，那么计算结果为0
  - COUNT
    - 当需要纵向统计时可以使用COUNT( )
    - 查询emp表中记录数
    - 查询emp表中有佣金的人数
    - 注意，因为count()函数中给出的是comm列，那么只统计comm列非NULL的行数
    - 查询emp表中月薪大于2500的人数
    - 统计月薪与佣金之和大于2500元的人数
    - 查询有佣金的人数，以及有领导的人数：
  - SUM和AVG
    - 当需要纵向求和时使用sum()函数
    - 查询所有雇员月薪和
    - 查询所有雇员月薪和，以及所有雇员佣金和
    - 查询所有雇员月薪+佣金和
    - 统计所有员工平均工资：

  - MAX和MIN
    - 查询最高工资和最低工资



#### DDL

- 用于对数据库和表的管理操作

- 操作数据库

  - 查看所有数据库名称

    - SHOW DATABASES;

  - 切换数据库

    - USE 数据库名;

  - 创建数据库

    - CREATE DATABASE 数据库名;

    - CREATE DATABASE IF NOT EXISTS 数据库名;

      注：IF NOT EXISTS，如果不存在就创建

  - 删除数据库

    - DROP DATABASE 数据库名;
    - DROP DATABASE IF NOT EXISTS 数据库名;

- 操作表

  - 查看表结构
    - DESC 表名;

  - 添加列
    - ALTER TABLE 表名 ADD CLOUMN 列名 列类型 约束;

  - 修改列类型
    - ALTER TABLE 表名 MODIFY CLOUMN 列名 新列类型 约束;

  - 修改列名
    - ALTER TABLE 表名 CHANGE CLOUMN 列名 新列名 新列类型 约束;
  - 删除列
    - ALTER TABLE 表名 DROP CLOUMN 列名 列名;

  - 修改表名称
    - ALTER TABLE 表名 RENAME TO 新表名;
  - 删除表
    - DROP TABLE IF EXISTS 表名;
  - 复制表
    1. 仅仅复制表的结构
       - CREATE TABLE 表名 LIKE 被复制的表名;
    2. 完整复制
       - CREATE TABLE 表名 SELECT * FROM 被复制的表名;
    3. 举例
       - 复制数据库 employee 的表 jobs 的 job_id 到新表 new，不复制数据
       - create new select job_id from employee.job_id where 1=2;
       - 其中，1=2 永远不成立，那么啥也筛选不出来，也就只有表结构被复制

- 数据类型

  - int：整型
  - double / float：浮点型，例如double(5,2)表示最多5位，其中必须有2位小数，即最大值为999.99；
  - decimal：浮点型，在表示钱方面使用该类型，因为不会出现精度缺失问题
  - char：固定长度字符串类型；char(4) 范围是0-255
  - varchar：可变长度字符串类型
  - text：字符串类型；表示存储较长文本

  |         |     意思     | 格式       | n的解释                     | 特点                                | 效率 |
  | ------- | :----------: | :--------- | :-------------------------- | :---------------------------------- | :--: |
  | Char    | 固定长度字符 | Char(n)    | 最大的字符个数，可选默认：1 | 不管实际存储，开辟的空间都是n个字符 |  高  |
  | Varchar | 可变长度字符 | Varchar(n) | 最大的字符个数，必选        | 根据实际存储决定开辟的空间          |  低  |

  - blob：图片类型：jpg、mp3、avi...
  - date：日期类型，格式为：yyyy-MM-dd
  - time：时间类型，格式为：hh:mm:ss
  - timestamp / datetime：时间戳类型；日期+时间 yyyyMMdd hhmmss 

  |           |      保存范围       | 所占字节 |
  | --------- | :-----------------: | :------: |
  | Datetime  |   1900-1-1~xxxx年   |    8     |
  | Timestamp | 1970-1-1~2038-12-31 |    4     |



#### DML

- 对表中的数据，增删改查

- CRUD

- 插入数据

  - 语法
    - INSERT INTO 表名 (列名1, 列名2, …) VALUES (值1, 值2);
    - INSERT INTO 表名 VALUES (值1, 值2,…);
      - 因为没有指定要插入的列，表示按创建表时列的顺序插入所有列的值
  - 特点
    - 非数值类型，必须使用 ‘ ’
    - 字段和值列表必须一一对应：列名1—值1
    - 遵循类型和约束
    - 可以为空的字段不用显式插入 

- 修改数据

  - 语法
    - UPDATE 表名 SET 列名1=值1, … 列名n=值n（WHERE 条件);
    - UPDATE 表1 inner  join 表2 on 表 SET 列名1= 新值1,列名2 =新值2（where 筛选条件);

- 删除数据

  - 语法

    - TRUNCATE TABLE 表名;
    - DELETE FROM 表名 WHERE 筛选条件;
    - DELETE FROM 表1 别名1 INNER JOIN 表2 别名2 on 连接条件（AND 筛选条件);

  - TRUNCATE 和 DELETE 

    - DELETE 可以有 WHERE 语句

    - TRUNCATE 没有WHERE 语句，一次性删除所有记录，所以效率高
    - TRUNCATE 其实是 DDL语句，因为它先 DROP TABLE，再 CREATE TABLE
    - TRUNCATE 删除的记录是无法回滚的，从0开始
    - DELETE删除的记录是可以回滚的，从断点开始



#### 约束

- 用于限制表中字段，从而进一步保证数据表的数据一致性、准确性、可靠性

- 常见约束
  - not null
    - 非空
    - 用于限制该字段为必填项
  - default
    - 默认
    - 用于限制该字段没有显式插入值，则显示默认值
  - primary key
    - 主键
    - 用于限制该字段不能重复，不能空
    - 一个表只能有一个主键 
  - unique
    - 唯一
    - 用于限制该字段不能重复
    - 一个表可以有多个唯一
  - check
    - 检查
    - 用于限制该字段必须满足的条件
  - foreign key
    - 外键
    - 用于限制两个表的关系



#### 事务

- 一个事务是由一条或者多条sql语句构成，这一条或者多条sql语句要么全部执行成功，要么全部执行失败

- 事务的四大特性 / ACID

  - 原子性 / Atomicity
    - 事务中所有操作是不可再分割的原子单位
    - 事务中所有操作要么全部执行成功，要么全部执行失败
  - 一致性 / Consistency
    - 事务执行后，数据库状态与其它业务规则保持一致
    - 如转账业务，无论事务执行成功与否，参与转账的两个账号余额之和应该是不变的

  - 隔离性 / Isolation 
    - 在并发操作中，不同事务之间应该隔离开来，使每个并发中的事务不会相互干扰。

  - 持久性 / Durability
    - 一旦事务提交成功，事务中所有的数据操作都必须被持久化到数据库中
    - 即使提交事务后，数据库马上崩溃，在数据库重启时，也必须能保证通过某种机制恢复数据

- 在默认情况下，MySQL每执行一条SQL语句，都是一个单独的事务

- 如果需要在一个事务中包含多条SQL语句，那么需要开启事务和结束事务

  - 开启事务：start transaction

  - 结束事务：commit、rollback

- 事务执行过程

  1. 在执行SQL语句之前，先执行strat transaction，这就开启了一个事务（事务的起点）

  2. 执行多条SQL语句

  3. 最后要结束事务，

     commit，表示提交，即事务中的多条SQL语句所做出的影响会持久化到数据库中

     rollback，表示回滚，即回滚到事务的起点，之前做的所有操作都被撤消了

- 分类

  - 隐式事务：没有明显的开启和结束的标志
  - 显式事务：有明显的开启和结束的标志

