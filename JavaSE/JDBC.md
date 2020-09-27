### JDBC

#### 前言

现在开发一般都是 Mybatis，也有公司用的 Hibernate 或者 Spring Data JPA。

很多时候，不同的项目由不同的程序员开发，在公司层面可能没有将技术完全统一起来，一个项目用 Mybatis，一个项目用 Hibernate都是很有可能的。

不管用的是什么 `ORM` 框架，都是在 JDBC 上封装了一层嘛，所以JDBC还是需要好好学习的。



#### 什么是ORM？

Object_Relative DateBase-Mapping，在Java对象与关系数据库之间建立某种映射，以实现直接存取Java对象。`



#### JDBC基础知识

1. JDBC全称为：Java Data Base Connectivity，它是**可以执行SQL语句的 Java API**。

2. 每种数据库都有自己的图形界面，我都可以在里边操作执行数据库相关的事，为什么我们要用JDBC？

- 数据库里的数据是给程序用的。
- 我们用的是 Java程序语言，所以需要用Java程序去链接数据库来访问数据。
- 市面上有非常多的数据库，本来我们是需要根据不同的数据库学习不同的 API，sun公司为了简化这个操作，定义了`JDBC API`。对于我们来说，操作数据库都是在`JDBC API`上，使用不同的数据库，只要用数据库厂商提供的数据库驱动程序即可。

3. 简单来说，“JDBC，把 API 定义出来，你给我实现就完事了！” 这样无论数据库怎么变，都用的同一套 API



#### 学习 API

Connection、Statement、ResultSet。

- Conneion： 获取与数据库链接的 对象
- Statement：获取执行 SQL 语句的 对象
- ResultSet：  执行 SQL 语句，获取结果集



小白必学的 Java 连接数据库的代码：

1. 导入 MySQL 或者 Oracle 驱动包
2. 装载数据库驱动程序
3. 获取到与数据库连接
4. 获取可以执行SQL语句的对象
5. 执行SQL语句
6. 关闭连接

```java
Connection connection = null;
Statement statement = null;
ResultSet resultSet = null;

try {

    /*
    * 加载驱动有两种方式
    *
    * 1：会导致驱动会注册两次，过度依赖于mysql的api，脱离的mysql的开发包，程序则无法编译
    * 2：驱动只会加载一次，不需要依赖具体的驱动，灵活性高
    *
    * 我们一般都是使用第二种方式
    * */

    //1.
    //DriverManager.registerDriver(new com.mysql.jdbc.Driver());

    //2.
    Class.forName("com.mysql.jdbc.Driver");

    //获取与数据库连接的对象-Connetcion
    connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/zhongfucheng", "root", "root");

    //获取执行sql语句的statement对象
    statement = connection.createStatement();

    //执行sql语句,拿到结果集
    resultSet = statement.executeQuery("SELECT * FROM users");

    //遍历结果集，得到数据
    while (resultSet.next()) {

        System.out.println(resultSet.getString(1));

        System.out.println(resultSet.getString(2));
    }

} catch (SQLException e) {
    e.printStackTrace();
} catch (ClassNotFoundException e) {
    e.printStackTrace();
} finally {

    /*
    * 关闭资源，后调用的先关闭
    *
    * 关闭之前，要判断对象是否存在
    * */

    if (resultSet != null) {
        try {
            resultSet.close();
        } catch (SQLException e) {
            e.printStackTrace();
        }

    }
    if (statement != null) {
        try {
            statement.close();
        } catch (SQLException e) {
            e.printStackTrace();
        }

    }
    if (connection != null) {
        try {
            connection.close();
        } catch (SQLException e) {
            e.printStackTrace();
        }

    }

}
```



PreparedStatement API 与 Statement API 的区别，为什么要用PreparedStatement？

1. Statement 对象编译 SQL 语句时，如果 SQL 语句有变量，就需要使用分隔符来隔开，如果变量非常多，就会使 SQL 变得非常复杂。**PreparedStatement 可以使用占位符，简化sql的编写**。
2. Statement 会频繁编译 SQL。**PreparedStatement 可对 SQL 进行预编译，提高效率**。预编译的 SQL 存储在 PreparedStatement 对象中。
3. PreparedStatement 防止 SQL 注入。(Statement通过分隔符`'++'`,编写永等式，可以不需要密码就进入数据库)



#### 数据库连接池

1. 为什么我们要使用数据库连接池？

- 数据库的连接的建立和关闭是非常消耗资源的，频繁地打开、关闭连接造成系统性能低下。

2. 常见的数据库连接池有C3P0、DBCP、**Druid**。



#### 分页

简单说一下 Oracle 和 MySQL 是如何实现分页的，以及对应的解释：

1. Oracle 分页

-  Oracle 的分页依赖于 `ROWNUM` 这个伪列，`ROWNUM` 主要作用就是产生行号。

-    分页原理：
      1：子查询查出前 n 行数据，`ROWNUM `产生前N行的行号
      2：使用子查询产生 `ROWNUM` 的行号，通过外部的筛选出想要的数据

-    例子：
      我现在规定每页显示5行数据【lineSize=5】，我要查询第2页的数据【currentPage=2】

-    实现：
      1：子查询查出前10条数据【ROWNUM<=10】
      2：外部筛选出后面5条数据【ROWNUM>5】
      3：这样我们就取到了后面5条的数据
-  Oracle分页语法：
      lineSize :           每页显示数据行数
      currentPage :   当前所在页

```mysql
SELECT *FROM (
        SELECT 列名,列名,ROWNUM rn
        FROM 表名
        WHERE ROWNUM<=(currentPage*lineSize)) temp
    WHERE temp.rn>(currentPage-1)*lineSize;
```



2. MySQL 分页

-   例子：
    我现在规定每页显示5行数据，我要查询第2页的数据

-    分析：
     第2页的数据其实就是从第6条数据开始，取5条

- ​    实现：
  ​    1：start 为5【偏移量从0开始】
  ​    2：length 为5
- Mysql分页语法：
     start :     偏移量，不设置就是从0开始【也就是(currentPage-1)*lineSize】
     length :  长度，取多少行数据

```mysql
SELECT *
    FROM 表名
    LIMIT [START], length;
```



3. 总结：

- Mysql从`(currentPage-1)*lineSize`开始取数据，取`lineSize`条数据
- Oracle先获取`currentPage**lineSize条数据，从(currentPage-1)**lineSize`开始取数据



