## JDBC_SQL

#### 数据的持久化

- 把数据保存到可掉电式存储设备中以供之后使用
- 大多数情况下，特别是企业级应用，数据持久化意味着将内存中的数据保存到硬盘上加以”固化”
- 持久化的实现过程大多通过各种关系数据库来完成
- 持久化的主要应用是将内存中的数据存储在关系型数据库中，当然也可以存储在磁盘文件、XML数据文件中



####  Java中的数据存储技术

- 在Java中，数据库存取技术可分为如下几类：
  - JDBC 直接访问数据库
  - JDO (Java Data Object )技术
  - 第三方O/R工具，如Hibernate、Mybatis

- JDBC是 java 访问数据库的基石，JDO、Hibernate、MyBatis等只是更好的封装了 JDBC



#### 什么是 JDBC

- Java Database Connectivity
- 一个独立于特定数据库管理系统（DBMS）、通用的SQL数据库存取和操作的公共接口 / 一组API 
- JDBC 是 SUN公司提供一套用于数据库操作的接口API，Java程序员只需要面向这套接口编程即可
- 不同的数据库厂商，需要针对这套接口，提供不同实现。不同的实现的集合，即为不同数据库的驱动



#### JDBC 可以做什么

- 查询女神们的基本信息 
- 查询女神们的详细信息 
- 查询心仪女神的电话号码 ，噢耶
- 添加心仪的女神 
- 删除不符合要求的女神 
- 修改女神的信息 
- 查询管理员的所有信息



####  JDBC体系结构

- JDBC接口（API）包括两个层次
  - 面向应用的API
    - Java API，抽象接口，供应用程序开发人员使用
    - 连接数据库，执行SQL语句，获得结果
  - 面向数据库的API
    - Java Driver API，供开发商开发数据库驱动程序用



#### 获取数据库连接

- 要素一：**Driver接口实现类**

  - Driver接口介绍
    - java.sql.Driver 接口是所有 JDBC 驱动程序需要实现的接口
    - 这个接口是提供给数据库厂商使用的，不同数据库厂商提供不同的实现
    - 在程序中不需要直接去访问实现了 Driver 接口的类，由驱动程序管理器类/ java.sql.DriverManager去调用这些 Driver实现
  - Oracle的驱动：oracle.jdbc.driver.OracleDriver
  - mySql的驱动： com.mysql.jdbc.Driver
  - 加载 与 注册 JDBC驱动
    - 加载驱动
      1. 加载 JDBC 驱动需调用 Class 类的静态方法 forName ( )
      2. 向其传递要加载的 JDBC 驱动的类名Class.forName (“com.mysql.jdbc.Driver”);
    - 注册驱动：
      1. DriverManager 类是驱动程序管理器类，负责管理驱动程序
      2. 使用 DriverManager.registerDriver (com.mysql.jdbc.Driver) 来注册驱动
    
  - 通常不用显式调用 DriverManager 类的 registerDriver() 方法来注册驱动程序类的实例，因为   Driver 接口的驱动程序类都包含了静态代码块，在这个静态代码块中，会调用 DriverManager.registerDriver() 方法来注册自身的一个实例

- 要素二：**URL**
  - JDBC URL 用于标识一个被注册的驱动程序，驱动程序管理器通过这个 URL 选择正确的驱动程序，从而建立到数据库的连接
  - JDBC URL的标准由三部分组成，各部分间用冒号分隔
    - jdbc:子协议：子名称
    - 协议：JDBC URL中的协议总是 jdbc 
    - 子协议：子协议用于标识一个数据库驱动程序
    - 子名称：一种标识数据库的方法。子名称可以依不同的子协议而变化，用子名称的目的是为了定位数据库提供足够的信息。包含主机名(对应服务端的ip地址)，端口号，数据库名
  - 几种常用数据库的 JDBC URL
    1. MySQL的连接URL编写方式
       - jdbc:mysql://主机名称:mysql服务端口号/数据库名称?参数=值&参数=值
       - jdbc:mysql://localhost:3306/atguigu
       - jdbc:mysql://localhost:3306/atguigu?useUnicode=true&characterEncoding=utf8（如果JDBC程序与服务器端的字符集不一致，会导致乱码，那么可以通过参数指定服务器端的字符集）
       - jdbc:mysql://localhost:3306/atguigu?user=root&password=123456
    2. Oracle 9i的连接URL编写方式
       - jdbc:oracle:thin:@主机名称:oracle服务端口号:数据库名称
       - jdbc:oracle:thin:@localhost:1521:atguigu
  3. SQLServer的连接URL编写方式
       - jdbc:sqlserver://主机名称:sqlserver服务端口号:DatabaseName=数据库名称
     - jdbc:sqlserver://localhost:1433:DatabaseName=atguigu
  

  - 要素三：**用户名和密码**
    - user、password可以用“属性名=属性值”方式告诉数据库
    - 可以调用 DriverManager 类的 getConnection ( ) 方法建立到数据库的连接




#### 连接方式

- 连接方式一


```java
@Test
public void testConnection1() {
  try {
    // 1.提供java.sql.Driver接口实现类的对象
    Driver driver = null;
    driver = new com.mysql.jdbc.Driver();

    // 2.提供url，指明具体操作的数据
    String url = "jdbc:mysql://localhost:3306/test";

    // 3.提供Properties的对象，指明用户名和密码
    Properties info = new Properties();
    info.setProperty("user", "root");
    info.setProperty("password", "abc123");

    // 4.调用driver的connect()，获取连接
    Connection conn = driver.connect(url, info);
    System.out.println(conn);
  } catch (SQLException e) {
    e.printStackTrace();
  }
}

// 显式出现了第三方数据库的API
```
  - 连接方式二
```java
@Test
public void testConnection2() {
  try {
    // 1.实例化Driver
    String className = "com.mysql.jdbc.Driver";
    Class clazz = Class.forName(className);
    Driver driver = (Driver) clazz.newInstance();

    // 2.提供url，指明具体操作的数据
    String url = "jdbc:mysql://localhost:3306/test";

    // 3.提供Properties的对象，指明用户名和密码
    Properties info = new Properties();
    info.setProperty("user", "root");
    info.setProperty("password", "abc123");

    // 4.调用driver的connect()，获取连接
    Connection conn = driver.connect(url, info);
    System.out.println(conn);

  } catch (Exception e) {
    e.printStackTrace();
  }
}

// 使用反射实例化Driver，不在代码中体现第三方数据库的API。体现了面向接口编程思想。
```

  - 连接方式三

```java
@Test
public void testConnection3() {
  try {
    // 1.数据库连接的4个基本要素：
    String url = "jdbc:mysql://localhost:3306/test";
    String user = "root";
    String password = "abc123";
    String driverName = "com.mysql.jdbc.Driver";

    // 2.实例化Driver
    Class clazz = Class.forName(driverName);
    Driver driver = (Driver) clazz.newInstance();
    // 3.注册驱动
    DriverManager.registerDriver(driver);
    // 4.获取连接
    Connection conn = DriverManager.getConnection(url, user, password);
    System.out.println(conn);
  } catch (Exception e) {
    e.printStackTrace();
  }

}

// 使用DriverManager实现数据库的连接。体会获取连接必要的4个基本要素。
```

  - 连接方式四

```java
@Test
public void testConnection4() {
  try {
    // 1.数据库连接的4个基本要素：
    String url = "jdbc:mysql://localhost:3306/test";
    String user = "root";
    String password = "abc123";
    String driverName = "com.mysql.jdbc.Driver";

    // 2.加载驱动 （①实例化Driver ②注册驱动）
    Class.forName(driverName);

    // 3.获取连接
    Connection conn = DriverManager.getConnection(url, user, password);
    System.out.println(conn);
  } catch (Exception e) {
    e.printStackTrace();
  }

}

// 不必显式的注册驱动了。因为在DriverManager的源码中已经存在静态代码块，实现了驱动的注册。
```
  - 连接方式五、终极版

```java
@Test
public  void testConnection5() throws Exception {
  // 1.加载配置文件
  InputStream is = ConnectionTest.class.getClassLoader().getResourceAsStream("jdbc.properties");
  Properties pros = new Properties();
  pros.load(is);

  // 2.读取配置信息
  String user = pros.getProperty("user");
  String password = pros.getProperty("password");
  String url = pros.getProperty("url");
  String driverClass = pros.getProperty("driverClass");

  // 3.加载驱动
  Class.forName(driverClass);

  // 4.获取连接
  Connection conn = DriverManager.getConnection(url,user,password);
  System.out.println(conn);

}

// 使用配置文件的方式保存配置信息，在代码中加载配置文件
```

其中，配置文件声明在工程的src目录下：jdbc.properties

```properties
user=root
password=abc123
url=jdbc:mysql://localhost:3306/test
driverClass=com.mysql.jdbc.Driver
```



#### 使用PreparedStatement实现CRUD操作

-  操作和访问数据库

  - 数据库连接被用于向数据库服务器发送命令和 SQL 语句，并接受数据库服务器返回的结果
  - 其实一个数据库连接就是一个Socket连接
  - 在 java.sql 包中有 3 个接口分别定义了对数据库的调用的不同方式
    - Statement：用于执行静态 SQL 语句并返回它所生成结果的对象
    - PrepatedStatement：SQL 语句被预编译并存储在此对象中，可以使用此对象多次高效地执行该语句
    - CallableStatement：用于执行 SQL 存储过程

-  Statement

  - 通过调用 Connection 对象的 createStatement( ) 创建该对象，该对象用于执行静态的 SQL 语句，并且返回执行结果
  - Statement 接口中定义了下列方法用于执行 SQL 语句
    - int excuteUpdate (String sql)：执行更新操作INSERT、UPDATE、DELETE
    - ResultSet executeQuery (String sql)：执行查询操作SELEC，返回结果集
  - 弊端
    - 存在拼串操作，繁琐
    - 存在 SQL注入问题
  - SQL 注入是利用某些系统没有对用户输入的数据进行充分的检查，而在用户输入数据中注入非法的 SQL 语句段或命令
  - 对于 Java 而言，要防范 SQL 注入，只要用 PreparedStatement (从Statement扩展而来) 取代 Statement 就可以了

- **PreparedStatement**

  - 可以通过调用 Connection 对象调用 preparedStatement (String sql) 创建该对象

  - PreparedStatement 接口是 Statement 的子接口，也有 Statement 的方法，它表示一条预编译过的 SQL 语句

  - PreparedStatement 对象所代表的 SQL 语句中的参数用 ? 来表示

    PreparedStatement 对象调用 setXXX ( ) / setObject( )来设置这些参数

  - 优点

    - 可以操作 Blob 数据

    - 可以实现更高效的批量操作

    - PreparedStatement 能最大可能提高性能

      DBServer 会对**预编译**语句提供性能优化

      因为预编译语句有可能被重复调用，所以语句在被 DBServer 的编译器编译后的执行代码被缓存下来

      那么下次调用时只要是相同的预编译语句就不需要编译，只要将参数直接传入编译过的语句执行代码中就会得到执行

      在statement语句中，即使是相同操作但因为数据内容不一样，所以整个语句本身不能匹配，没有缓存语句的意义
- Java与SQL对应数据类型转换表


| Java类型           | SQL类型                  |
| ------------------ | ------------------------ |
| boolean            | BIT                      |
| byte               | TINYINT                  |
| short              | SMALLINT                 |
| int                | INTEGER                  |
| long               | BIGINT                   |
| String             | CHAR、VARCHARLONGVARCHAR |
| byte   array       | BINARY、 VAR BINARY      |
| java.sql.Date      | DATE                     |
| java.sql.Time      | TIME                     |
| java.sql.Timestamp | TIMESTAMP                |

- 使用PreparedStatement实现增、删、改操作

```java

// 通用的增、删、改操作（体现一：增、删、改 ； 体现二：针对于不同的表）
public void update(String sql,Object ... args){
  Connection conn = null;
  PreparedStatement ps = null;
  try {
    // 1.获取数据库的连接
    conn = JDBCUtils.getConnection();

    // 2.获取PreparedStatement的实例 (或：预编译sql语句)
    ps = conn.prepareStatement(sql);
    // 3.填充占位符
    for(int i = 0;i < args.length;i++){
      ps.setObject(i + 1, args[i]);
    }

    // 4.执行sql语句
    ps.execute();
  } catch (Exception e) {

    e.printStackTrace();
  }finally{
    // 5.关闭资源
    JDBCUtils.closeResource(conn, ps);

  }
}
```

- 使用PreparedStatement实现查询操作

```javascript
// 通用的针对于不同表的查询:返回一个对象 (version 1.0)
public <T> T getInstance(Class<T> clazz, String sql, Object... args) {	
  Connection conn = null;
  PreparedStatement ps = null;
  ResultSet rs = null;
  try {
    // 1.获取数据库连接
    conn = JDBCUtils.getConnection();

    // 2.预编译sql语句，得到PreparedStatement对象
    ps = conn.prepareStatement(sql);

    // 3.填充占位符
    for (int i = 0; i < args.length; i++) {
      ps.setObject(i + 1, args[i]);
    }

    // 4.执行executeQuery(),得到结果集：ResultSet
    rs = ps.executeQuery();

    // 5.得到结果集的元数据：ResultSetMetaData
    ResultSetMetaData rsmd = rs.getMetaData();

    // 6.1通过ResultSetMetaData得到columnCount,columnLabel；通过ResultSet得到列值
    int columnCount = rsmd.getColumnCount();
    if (rs.next()) {
      T t = clazz.newInstance();
      for (int i = 0; i < columnCount; i++) {// 遍历每一个列

        // 获取列值
        Object columnVal = rs.getObject(i + 1);
        // 获取列的别名:列的别名，使用类的属性名充当
        String columnLabel = rsmd.getColumnLabel(i + 1);
        // 6.2使用反射，给对象的相应属性赋值
        Field field = clazz.getDeclaredField(columnLabel);
        field.setAccessible(true);
        field.set(t, columnVal);
      }
      return t;
    }
  } catch (Exception e) {

    e.printStackTrace();
  } finally {
    // 7.关闭资源
    JDBCUtils.closeResource(conn, ps, rs);
  }

  return null;

}
```



#### ResultSet、ResultSetMetaData

- ResultSet
  - PreparedStatement 对调用 executeQuery ( )，返回一个ResultSet 对象：结果集
  - ResultSet 对象以逻辑表格的形式封装了执行数据库操作的结果集，ResultSet 接口由数据库厂商提供实现
  - ResultSet 返回的实际上就是一张数据表，有一个指针指向数据表的第一条记录的前面
  - ResultSet 对象维护了一个指向当前数据行的游标：相当于 Iterator 对象的 hasNext( ) 和 next( ) 方法的结合体
    - 初始的时候，游标在第一行之前，可以通过 ResultSet 对象的 next ( ) 移动到下一行
    - 调用 next ( ) 检测下一行是否有效，若有效，该方法返回 true，且指针下移
  - 当指针指向一行时, 可以通过调用 getXxx(int index) 或 getXxx(int columnName) 获取每一列的值。
    - 例如：getInt(1)、getString("name")
    - 注意：Java与数据库交互涉及到的相关Java API中的索引都从1开始
  - ResultSet 接口的常用方法
    - boolean next ( )
    - getString()
    - …

-  ResultSetMetaData

  - 可用于获取关于 ResultSet 对象中列的类型和属性信息的对象

  - ResultSetMetaData meta = ResultSet.getMetaData ( );
    - getColumnName (int column)：获取指定列的名称
    - getColumnLabel (int column)：获取指定列的别名
    - getObject (int column)：获取指定列的值
    - getColumnCount ( )：返回当前 ResultSet 对象中的列数
    - getColumnTypeName (int column)：检索指定列的数据库特定的类型名称 
    - getColumnDisplaySize (int column)：指示指定列的最大标准宽度，以字符为单位 
    - isNullable (int column)：指示指定列中的值是否可以为 null
    - isAutoIncrement (int column)：指示是否自动为指定列进行编号，这样这些列仍然是只读



#### 资源的释放

- 释放ResultSet、Statement、Connection
- 数据库连接（Connection）是非常稀有的资源，用完后必须马上释放
- 如果 Connection 不能及时正确的关闭，将导致系统宕机
- Connection的使用原则是尽量晚创建，尽量早的释放
- 可以在finally中关闭，保证及时其他代码出现异常，资源也一定能被关闭



#### JDBC API小结

- 两种思想
  - 面向接口编程的思想
  - ORM思想  / object relational mapping
    - 一个数据表对应一个java类
    - 表中的一条记录对应 java类的一个对象
    - 表中的一个字段对应 java类的一个属性
    - sql是需要结合列名和表的属性名来写。注意起别名。

- 两种技术
  - JDBC结果集的元数据：ResultSetMetaData
    - 获取列数：getColumnCount ( )
    - 获取列的别名：getColumnLabel ( )
  - 通过反射，创建指定类的对象，获取指定的属性并赋值



#### 操作BLOB类型字段

- MySQL中，BLOB是一个二进制大型对象，是一个可以存储大量数据的容器，它能容纳不同大小的数据

- 插入 BLOB 类型的数据必须使用PreparedStatement，因为 BLOB 类型的数据无法使用字符串拼接


- 实际使用中根据需要存入的数据大小定义不同的BLOB类型

- 注意

  - 如果存储的文件过大，数据库的性能会下降

  - 如果报错：xxx too large，那么在mysql的安装目录下，找my.ini文件加上如下的配置参数： max_allowed_packet=16M
  - 修改了my.ini文件之后，需要重新启动mysql服务

- 向数据 中插入大数据类型

```java
// 获取连接
Connection conn = JDBCUtils.getConnection();

String sql = "insert into customers(name,email,birth,photo)values(?,?,?,?)";
PreparedStatement ps = conn.prepareStatement(sql);

// 填充占位符
ps.setString(1, "徐海强");
ps.setString(2, "xhq@126.com");
ps.setDate(3, new Date(new java.util.Date().getTime()));
// 操作Blob类型的变量
FileInputStream fis = new FileInputStream("xhq.png");
ps.setBlob(4, fis);
// 执行
ps.execute();

fis.close();
JDBCUtils.closeResource(conn, ps);
```

- 从数据中读取大数据类型

```java
String sql = "SELECT id, name, email, birth, photo FROM customer WHERE id = ?";
conn = getConnection();
ps = conn.prepareStatement(sql);
ps.setInt(1, 8);
rs = ps.executeQuery();
if(rs.next()){
  Integer id = rs.getInt(1);
  String name = rs.getString(2);
  String email = rs.getString(3);
  Date birth = rs.getDate(4);
  Customer cust = new Customer(id, name, email, birth);
  System.out.println(cust); 
  //读取Blob类型的字段
  Blob photo = rs.getBlob(5);
  InputStream is = photo.getBinaryStream();
  OutputStream os = new FileOutputStream("c.jpg");
  byte [] buffer = new byte[1024];
  int len = 0;
  while((len = is.read(buffer)) != -1){
    os.write(buffer, 0, len);
  }
  JDBCUtils.closeResource(conn, ps, rs);

  if(is != null){
    is.close();
  }

  if(os !=  null){
    os.close();
  }

}
```



#### 批量插入

- 当需要成批插入或者更新记录时，可以采用 Java 的批量更新机制，这一机制允许多条语句一次性提交给数据库批量处理
- JDBC 批量处理语句的方法

  - addBatch (String)：添加需要批量处理的SQL语句或是参数
  - executeBatch ( )：执行批量处理语句
  - clearBatch ( )：清空缓存的数据

- 通常我们会遇到两种批量执行 SQL 语句的情况
    - 多条 SQL 语句的批量处理
    - 一个 SQL 语句的批量传参

- 举例：向数据表中插入20000条数据
  - 数据库中提供一个 goods 表：

```sql
CREATE TABLE goods(
id INT PRIMARY KEY AUTO_INCREMENT,
NAME VARCHAR(20)
);
```

- 方法一：使用Statement

```java
Connection conn = JDBCUtils.getConnection();
Statement st = conn.createStatement();
for(int i = 1;i <= 20000;i++){
  String sql = "insert into goods(name) values('name_' + "+ i +")";
  st.executeUpdate(sql);
}
```

-   方法二：：使用PreparedStatement

```java
long start = System.currentTimeMillis();

Connection conn = JDBCUtils.getConnection();

String sql = "insert into goods(name)values(?)";
PreparedStatement ps = conn.prepareStatement(sql);
for(int i = 1;i <= 20000;i++){
  ps.setString(1, "name_" + i);
  ps.executeUpdate();
}

long end = System.currentTimeMillis();
System.out.println("花费的时间为：" + (end - start));//82340


JDBCUtils.closeResource(conn, ps);
```

-   方法三：


```java
/*
 * 修改1： 使用 addBatch() / executeBatch() / clearBatch()
 * 修改2：mysql服务器默认是关闭批处理的，我们需要通过一个参数，让mysql开启批处理的支持。
 * 		 ?rewriteBatchedStatements=true 写在配置文件的url后面
 * 修改3：使用更新的mysql 驱动：mysql-connector-java-5.1.37-bin.jar
 * 
 */
@Test
public void testInsert1() throws Exception{
  long start = System.currentTimeMillis();

  Connection conn = JDBCUtils.getConnection();

  String sql = "insert into goods(name)values(?)";
  PreparedStatement ps = conn.prepareStatement(sql);

  for(int i = 1;i <= 1000000;i++){
    ps.setString(1, "name_" + i);

    //1.“攒”sql
    ps.addBatch();
    if(i % 500 == 0){
      //2.执行
      ps.executeBatch();
      //3.清空
      ps.clearBatch();
    }
  }

  long end = System.currentTimeMillis();
  System.out.println("花费的时间为：" + (end - start));//20000条：625                                                                         //1000000条:14733  

  JDBCUtils.closeResource(conn, ps);
}
```

- 方法四：

```java
/*
 * 层次四：在层次三的基础上操作
 * 使用Connection 的 setAutoCommit(false)  /  commit()
 */
@Test
public void testInsert2() throws Exception{
  long start = System.currentTimeMillis();

  Connection conn = JDBCUtils.getConnection();

  // 1.设置为不自动提交数据
  conn.setAutoCommit(false);

  String sql = "insert into goods(name)values(?)";
  PreparedStatement ps = conn.prepareStatement(sql);

  for(int i = 1;i <= 1000000;i++){
    ps.setString(1, "name_" + i);

    // 1.“攒”sql
    ps.addBatch();

    if(i % 500 == 0){
      // 2.执行
      ps.executeBatch();
      // 3.清空
      ps.clearBatch();
    }
  }

  // 2.提交数据
  conn.commit();

  long end = System.currentTimeMillis();
  System.out.println("花费的时间为：" + (end - start));//1000000条:4978 

  JDBCUtils.closeResource(conn, ps);
}
```

  

#### 数据库的事务

- 介绍

  - 事务

    - 一组逻辑操作单元，使数据从一种状态变换到另一种状态
    - 一组逻辑操作单元：一个或多个 DML / CRUD 操作

  - 事务处理 / 事务操作

    - 当在一个事务中执行多个操作时：

      要么所有的事务都被 提交 / commit，那么这些修改就永久地保存下来

      要么数据库管理系统将放弃所作的所有修改，整个事务 回滚 / rollback 到最初状态

  - 数据一旦提交，就不可以回滚

  - 哪些操作会导致数据的自动提交

    - DDL 操作一旦执行，都会自动提交
    - DML 默认情况下，一旦执行，就会自动提交。可以通过 set autocommit = false 取消自动提交
    - 默认在关闭连接时，会自动提交

  - 为确保数据库中数据的**一致性**，数据的操纵应当是离散的成组的逻辑单元

    - 当它全部完成时，数据的一致性可以保持
    - 当一部分操作失败，整个事务应全部视为错误，所有从起始点以后的操作应全部回退到开始状态

- 事务的 **ACID**

  1. 原子性 / Atomicity
     - 原子性是指事务是一个不可分割的工作单位
     - 事务中的操作要么都发生，要么都不发生
     
  2. 一致性 / Consistency
     
     - 事务必须使数据库从一个一致性状态变换到另外一个一致性状态
     
  3. 隔离性 / Isolation
     - 事务的隔离性是指一个事务的执行不能被其他事务干扰
     - 一个事务内部的操作及使用的数据，对并发的其他事务，是隔离的
     - 并发执行的各个事务之间不能互相干扰
  
4. 持久性 / Durability
     - 持久性是指一个事务一旦被提交，对数据库中数据的改变就是永久性
     - 接下来的其他操作和数据库故障不应该对其有任何影响
       


- 数据库的并发问题
    - 对于同时运行的多个事务，当这些事务访问数据库中相同的数据时，如果没有采取必要的隔离机制，就会导致各种并发问题

      1. **脏读**
         - 对于两个事务 T1、T2。
         - T1 读取了已经被 T2 更新但还**没有被提交**的字段
         - 之后，若 T2 回滚，T1读取的内容就是临时且无效的

      2. **不可重复读**
         - 对于两个事务T1、T2。
         - T1 读取了一个字段，然后 T2 **更新**了该字段
         - 之后，T1再次读取同一个字段，值就不同了

      3. **幻读**
         - 对于两个事务T1、T2。
         - T1 从一个表中读取了一个字段，然后 T2 在该表中**插入**了一些新的行
         - 之后，如果 T1 再次读取同一个表，就会多出几行

  - 数据库事务的隔离性

    - 数据库系统必须具有隔离并发运行各个事务的能力，使它们不会相互影响, 避免各种并发问题
    
  - 隔离级别
  
    - 一个事务与其他事务隔离的程度
    - 不同隔离级别对应不同的干扰程度，隔离级别越高，数据一致性就越好，但并发性越弱
  
  - 四种隔离级别
  
    - Oracle 支持 2 种事务隔离级别：READ COMMITED ( 默认 )、SERIALIZABLE
  
  
    - Mysql 支持 4 种事务隔离级别：REPEATABLE READ ( 默认 )、

- 在MySql中设置隔离级别

  - 每启动一个 mysql 程序, 就会获得一个单独的数据库连接. 每个数据库连接都有一个全局变量 @@tx_isolation, 表示当前的事务隔离级别。

  - 查看当前的隔离级别

    - SELECT @@tx_isolation;
    
  - 设置当前 mySQL 连接的隔离级别

    - set  transaction isolation level read committed;
    
  - 设置数据库系统的全局的隔离级别:

    - set global transaction isolation level read committed;
    
  - 补充操作：

    - 创建mysql数据库用户：

    ```mysql
    create user tom identified by 'abc123';
    ```

    - 授予权限

    ```mysql
    #授予通过网络方式登录的tom用户，对所有库所有表的全部权限，密码设为abc123.
    grant all privileges on *.* to tom@'%'  identified by 'abc123'; 
    
    #给tom用户使用本地命令行方式，授予atguigudb这个库下的所有表的插删改查的权限。
    rant select,insert,delete,update on atguigudb.* to tom@localhost identified by 'abc123'; 
    ```
    
  

#### DAO及相关实现类


- DAO
    - Data Access Object
    - 访问数据信息的类和接口
    - 包括了对数据的 CRUD ( Create、Retrival、Update、Delete )，而不包含任何业务相关的信息
    - 有时也称作：BaseDAO
- 作用：为了实现功能的模块化，更有利于代码的维护和升级



#### 数据库连接池

- JDBC数据库连接池的必要性

  - 在使用开发基于数据库的 web 程序时，传统的模式基本是按以下步骤　　

    - 在主程序（如servlet、beans）中建立数据库连接
    - 进行sql操作
    - 断开数据库连接

  - 这种模式开发，存在的问题

    1. 普通的 JDBC 数据库连接使用 DriverManager 来获取，每次向数据库建立连接的时候都要将 Connection 加载到内存中，再验证用户名和密码 (得花费0.05s～1s的时间)。需要数据库连接的时候，就向数据库要求一个，执行完成后再断开连接，这样的方式将会消耗大量的资源和时间

    2. 数据库的连接资源并没有得到很好的重复利用。
       - 若同时有几百人甚至几千人在线，频繁的进行数据库连接操作将占用很多的系统资源，严重的甚至会造成服务器的崩溃

    3. 对于每一次数据库连接，使用完后都得断开
       - 否则，如果程序出现异常而未能关闭，将会导致数据库系统中的内存泄漏，最终将导致重启数据库

    4. 这种开发不能控制被创建的连接对象数
       - 系统资源会被毫无顾及的分配出去，如连接过多，也可能导致内存泄漏，服务器崩溃

- 数据库连接池技术

  - 基本思想

    - 为数据库连接建立一个 “缓冲池”
    - 预先在缓冲池中放入一定数量的连接，当需要建立数据库连接时，只需从“缓冲池”中取出一个，使用完毕之后再放回去

  - 数据库连接池负责分配、管理和释放数据库连接：允许应用程序重复使用一个现有的数据库连接，而不是重新建立一个

  - 数据库连接池在初始化时将创建一定数量的数据库连接放到连接池中

    1. 连接数量：最小数据库连接数
       - 无论数据库连接是否被使用，连接池都将一直保证至少拥有这么多的连接数量

    2. 接池能占有的最大连接数：最大数据库连接数量
       - 当应用程序向连接池请求的连接数超过最大连接数量时，这些请求将被加入到等待队列中

  - 优点

    1. 资源重用
       - 由于数据库连接得以重用，避免了频繁创建，释放连接引起的大量性能开销
       - 在减少系统消耗的基础上，另一方面也增加了系统运行环境的平稳性

    2. 更快的系统反应速度
       - 数据库连接池在初始化过程中，往往已经创建了若干数据库连接置于连接池中备用
       - 对于业务请求处理而言，直接利用现有可用连接
       - 避免了数据库连接初始化和释放过程的时间开销
       - 减少了系统的响应时间

    3. 新的资源分配手段
       - 对于多应用共享同一数据库的系统而言，可在应用层通过数据库连接池的配置
       - 实现某一应用最大可用数据库连接数的限制，避免某一应用独占所有的数据库资源

    4. 统一的连接管理，避免数据库连接泄漏
       - 在较为完善的数据库连接池实现中，可根据预先的占用超时设定，强制回收被占用连接
       - 避免了常规数据库连接操作中可能出现的资源泄露

- 多种开源的数据库连接池

  - JDBC 的数据库连接池使用 javax.sql.DataSource 来表示
- DataSource 
    - 是一个接口，该接口通常由服务器 (Weblogic、WebSphere、Tomcat) 提供实现
  - 也被称为数据源，包含连接池和连接池管理，习惯上也经常把 DataSource 称为连接池
    - DataSource 用来取代 DriverManager 来获取 Connection，获取速度快，可以大幅度提高数据库访问速度
- 注意
    - 数据源和数据库连接不同，数据源无需创建多个，它是产生数据库连接的工厂，因此整个应用只需要一个数据源即可
    
  - con.close( ) 并没有关闭数据库的物理连接，它仅仅把数据库连接释放，归还给了数据库连接池
  
  - DBCP
  
    - Apache 提供的数据库连接池
  
  - tomcat 服务器自带 dbcp 数据库连接池
    
  - 速度相对 c3p0 较快，但因自身存在BUG，Hibernate3已不再提供支持
    
  - C3P0
    - 一个开源组织提供的一个数据库连接池
    - 速度相对较慢，稳定性还可以
    - hibernate官方推荐使用
  
  - Proxool
    - sourceforge 下的一个开源项目数据库连接池，有监控连接池状态的功能
    - 稳定性较 c3p0 差一点
    
  - BoneCP
  
    - 是一个开源组织提供的数据库连接池，速度快
    
  - Druid
    - 阿里提供的数据库连接池
    - 据说是集 DBCP 、C3P0 、Proxool 优点于一身的数据库连接池，但是速度不确定是否有 BoneCP 快
    
  



#### C3P0 数据库连接池

- 获取连接方式一
```java
// 使用C3P0数据库连接池的方式，获取数据库的连接：不推荐
public static Connection getConnection1() throws Exception{
  ComboPooledDataSource cpds = new ComboPooledDataSource();
  cpds.setDriverClass("com.mysql.jdbc.Driver"); 
  cpds.setJdbcUrl("jdbc:mysql://localhost:3306/test");
  cpds.setUser("root");
  cpds.setPassword("abc123");

  // cpds.setMaxPoolSize(100);

  Connection conn = cpds.getConnection();
  return conn;
}
```

- 获取连接方式二

```java
// 使用C3P0数据库连接池的配置文件方式，获取数据库的连接：推荐
private static DataSource cpds = new ComboPooledDataSource("helloc3p0");
public static Connection getConnection2() throws SQLException{
  Connection conn = cpds.getConnection();
  return conn;
}
```

- 配置文件为：c3p0-config.xml


```xml
<?xml version="1.0" encoding="UTF-8"?>
<c3p0-config>
  <named-config name="helloc3p0">
    <!-- 获取连接的4个基本信息 -->
    <property name="user">root</property>
    <property name="password">abc123</property>
    <property name="jdbcUrl">jdbc:mysql:///test</property>
    <property name="driverClass">com.mysql.jdbc.Driver</property>

    <!-- 涉及到数据库连接池的管理的相关属性的设置 -->
    <!-- 若数据库中连接数不足时, 一次向数据库服务器申请多少个连接 -->
    <property name="acquireIncrement">5</property>
    <!-- 初始化数据库连接池时连接的数量 -->
    <property name="initialPoolSize">5</property>
    <!-- 数据库连接池中的最小的数据库连接数 -->
    <property name="minPoolSize">5</property>
    <!-- 数据库连接池中的最大的数据库连接数 -->
    <property name="maxPoolSize">10</property>
    <!-- C3P0 数据库连接池可以维护的 Statement 的个数 -->
    <property name="maxStatements">20</property>
    <!-- 每个连接同时可以使用的 Statement 对象的个数 -->
    <property name="maxStatementsPerConnection">5</property>

  </named-config>
</c3p0-config>
```



#### DBCP数据库连接池

- DBCP 是 Apache 软件基金组织下的开源连接池实现，该连接池依赖该组织下的另一个开源系统：Common-pool
- 使用该连接池实现，应在系统中增加如下两个 jar 文件
  - Commons-dbcp.jar：连接池的实现
  - Commons-pool.jar：连接池实现的依赖库
- Tomcat 的连接池正是采用该连接池来实现的
- 该数据库连接池既可以与应用服务器整合使用，也可由应用程序独立使用
- 配置属性说明

| 属性                       | 默认值 | 说明                                                         |
| -------------------------- | ------ | ------------------------------------------------------------ |
| initialSize                | 0      | 连接池启动时创建的初始化连接数量                             |
| maxActive                  | 8      | 连接池中可同时连接的最大的连接数                             |
| maxIdle                    | 8      | 连接池中最大的空闲的连接数，超过的空闲连接将被释放，如果设置为负数表示不限制 |
| minIdle                    | 0      | 连接池中最小的空闲的连接数，低于这个数量会被创建新的连接。该参数越接近maxIdle，性能越好，因为连接的创建和销毁，都是需要消耗资源的；但是不能太大。 |
| maxWait                    | 无限制 | 最大等待时间，当没有可用连接时，连接池等待连接释放的最大时间，超过该时间限制会抛出异常，如果设置-1表示无限等待 |
| poolPreparedStatements     | false  | 开启池的Statement是否prepared                                |
| maxOpenPreparedStatements  | 无限制 | 开启池的prepared 后的同时最大连接数                          |
| minEvictableIdleTimeMillis |        | 连接池中连接，在时间段内一直空闲， 被逐出连接池的时间        |
| removeAbandonedTimeout     | 300    | 超过时间限制，回收没有用(废弃)的连接                         |
| removeAbandoned            | false  | 超过removeAbandonedTimeout时间后，是否进 行没用连接（废弃）的回收 |

- 获取连接方式一：

```java
public static Connection getConnection3() throws Exception {
  BasicDataSource source = new BasicDataSource();

  source.setDriverClassName("com.mysql.jdbc.Driver");
  source.setUrl("jdbc:mysql:///test");
  source.setUsername("root");
  source.setPassword("abc123");

  source.setInitialSize(10);

  Connection conn = source.getConnection();
  return conn;
}
```

- 获取连接方式二：

```java
// 使用dbcp数据库连接池的配置文件方式，获取数据库的连接：推荐
private static DataSource source = null;
static{
  try {
    Properties pros = new Properties();

    InputStream is = DBCPTest.class.getClassLoader().getResourceAsStream("dbcp.properties");

    pros.load(is);
    // 根据提供的BasicDataSourceFactory创建对应的DataSource对象
    source = BasicDataSourceFactory.createDataSource(pros);
  } catch (Exception e) {
    e.printStackTrace();
  }

}
public static Connection getConnection4() throws Exception {

  Connection conn = source.getConnection();

  return conn;
}
```

- 配置文件为：dbcp.properties


```properties
driverClassName=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/test?rewriteBatchedStatements=true&useServerPrepStmts=false
username=root
password=abc123

initialSize=10
#...
```



#### Druid（德鲁伊）数据库连接池

- Druid是阿里巴巴开源平台上一个数据库连接池实现
- 它结合了C3P0、DBCP、Proxool等DB池的优点，同时加入了日志监控
- 可以很好的监控DB池连接和SQL的执行情况，可以说是针对监控而生的DB连接池

```java
package com.atguigu.druid;

import java.sql.Connection;
import java.util.Properties;

import javax.sql.DataSource;

import com.alibaba.druid.pool.DruidDataSourceFactory;

public class TestDruid {
  public static void main(String[] args) throws Exception {
    Properties pro = new Properties();		 pro.load(TestDruid.class.getClassLoader().getResourceAsStream("druid.properties"));
    DataSource ds = DruidDataSourceFactory.createDataSource(pro);
    Connection conn = ds.getConnection();
    System.out.println(conn);
  }
}

```

配置文件为：druid.properties

```java
url=jdbc:mysql://localhost:3306/test?rewriteBatchedStatements=true
username=root
password=123456
driverClassName=com.mysql.jdbc.Driver

initialSize=10
maxActive=20
maxWait=1000
filters=wall
```

- 详细配置参数：

| **配置**                      | **缺省** | **说明**                                                     |
| ----------------------------- | -------- | ------------------------------------------------------------ |
| name                          |          | 配置这个属性的意义在于，如果存在多个数据源，监控的时候可以通过名字来区分开来。   如果没有配置，将会生成一个名字，格式是：”DataSource-” +   System.identityHashCode(this) |
| url                           |          | 连接数据库的url，不同数据库不一样。例如：mysql :   jdbc:mysql://10.20.153.104:3306/druid2      oracle :   jdbc:oracle:thin:@10.20.149.85:1521:ocnauto |
| username                      |          | 连接数据库的用户名                                           |
| password                      |          | 连接数据库的密码。如果你不希望密码直接写在配置文件中，可以使用ConfigFilter。详细看这里：<https://github.com/alibaba/druid/wiki/%E4%BD%BF%E7%94%A8ConfigFilter> |
| driverClassName               |          | 根据url自动识别   这一项可配可不配，如果不配置druid会根据url自动识别dbType，然后选择相应的driverClassName(建议配置下) |
| initialSize                   | 0        | 初始化时建立物理连接的个数。初始化发生在显示调用init方法，或者第一次getConnection时 |
| maxActive                     | 8        | 最大连接池数量                                               |
| maxIdle                       | 8        | 已经不再使用，配置了也没效果                                 |
| minIdle                       |          | 最小连接池数量                                               |
| maxWait                       |          | 获取连接时最大等待时间，单位毫秒。配置了maxWait之后，缺省启用公平锁，并发效率会有所下降，如果需要可以通过配置useUnfairLock属性为true使用非公平锁。 |
| poolPreparedStatements        | false    | 是否缓存preparedStatement，也就是PSCache。PSCache对支持游标的数据库性能提升巨大，比如说oracle。在mysql下建议关闭。 |
| maxOpenPreparedStatements     | -1       | 要启用PSCache，必须配置大于0，当大于0时，poolPreparedStatements自动触发修改为true。在Druid中，不会存在Oracle下PSCache占用内存过多的问题，可以把这个数值配置大一些，比如说100 |
| validationQuery               |          | 用来检测连接是否有效的sql，要求是一个查询语句。如果validationQuery为null，testOnBorrow、testOnReturn、testWhileIdle都不会其作用。 |
| testOnBorrow                  | true     | 申请连接时执行validationQuery检测连接是否有效，做了这个配置会降低性能。 |
| testOnReturn                  | false    | 归还连接时执行validationQuery检测连接是否有效，做了这个配置会降低性能 |
| testWhileIdle                 | false    | 建议配置为true，不影响性能，并且保证安全性。申请连接的时候检测，如果空闲时间大于timeBetweenEvictionRunsMillis，执行validationQuery检测连接是否有效。 |
| timeBetweenEvictionRunsMillis |          | 有两个含义： 1)Destroy线程会检测连接的间隔时间2)testWhileIdle的判断依据，详细看testWhileIdle属性的说明 |
| numTestsPerEvictionRun        |          | 不再使用，一个DruidDataSource只支持一个EvictionRun           |
| minEvictableIdleTimeMillis    |          |                                                              |
| connectionInitSqls            |          | 物理连接初始化的时候执行的sql                                |
| exceptionSorter               |          | 根据dbType自动识别   当数据库抛出一些不可恢复的异常时，抛弃连接 |
| filters                       |          | 属性类型是字符串，通过别名的方式配置扩展插件，常用的插件有：   监控统计用的filter:stat日志用的filter:log4j防御sql注入的filter:wall |
| proxyFilters                  |          | 类型是List，如果同时配置了filters和proxyFilters，是组合关系，并非替换关系 |



#### Apache-DBUtils 实现 CRUD操作

- 介绍

  - commons-dbutils 是 Apache 组织提供的一个开源 JDBC 工具类库，它是对 JDBC 的简单封装，学习成本极低
  - 使用dbutils能极大简化 JDBC 编码的工作量，也不会影响程序的性能
  - API介绍
    - org.apache.commons.dbutils.QueryRunner
    - org.apache.commons.dbutils.ResultSetHandler
    - 工具类：org.apache.commons.dbutils.DbUtils   


  - DbUtils 
    - 提供如关闭连接、装载 JDBC 驱动程序等常规工作的工具类，里面的所有方法都是静态的
    - 主要方法
      - public static void close (…) throws java.sql.SQLException：DbUtils类提供了三个重载的关闭方法。这些方法检查所提供的参数是不是NULL，如果不是的话，它们就关闭Connection、Statement和ResultSet。
      - public static void closeQuietly (…)：这一类方法不仅能在Connection、Statement和ResultSet为NULL情况下避免关闭，还能隐藏一些在程序中抛出的SQLEeception。
      - public static void commitAndClose(Connection conn)throws SQLException：用来提交连接的事务，然后关闭连接
      - public static void commitAndCloseQuietly(Connection conn)：用来提交连接，然后关闭连接，并且在关闭连接时不抛出SQL异常。
      - public static void rollback (Connection conn)throws SQLException：允许conn为null，因为方法内部做了判断
      - public static void rollbackAndClose (Connection conn) throws SQLException
      - rollbackAndCloseQuietly (Connection)
      - public static boolean loadDriver (java.lang.String driverClassName)：这一方装载并注册JDBC驱动程序，如果成功就返回true。使用该方法，你不需要捕捉这个异常ClassNotFoundException

-  QueryRunner类
  - 该类简单化了SQL查询，它与ResultSetHandler组合在一起使用可以完成大部分的数据库操作，能够大大减少编码量。

  - QueryRunner类提供了两个构造器：

    - 默认的构造器
    - 需要一个 javax.sql.DataSource 来作参数的构造器

  - 主要方法

    1. 更新
       - public int update (Connection conn, String sql, Object... params) throws SQLException：用来执行一个更新（插入、更新或删除）操作

    2. 插入
       - public <T> T insert(Connection conn, String sql, ResultSetHandler<T> rsh, Object... params) throws SQLException：只支持INSERT语句，其中 rsh - The handler used to create the result object from the ResultSet of auto-generated keys.  返回值: An object generated by the handler.即自动生成的键值

    3. 批处理
       - public int[] batch (Connection conn, String sql, Object[][] params) throws SQLException： INSERT, UPDATE, or DELETE语句
       - public <T> T insertBatch(Connection conn,String sql,ResultSetHandler<T> rsh,Object[][] params)throws SQLException：只支持INSERT语句

    4. 查询
       - public Object query (Connection conn, String sql, ResultSetHandler rsh, Object... params) throws SQLException：执行一个查询操作，在这个查询中，对象数组中的每个元素值被用来作为查询语句的置换参数。该方法会自行处理 PreparedStatement 和 ResultSet 的创建和关闭 

- ResultSetHandler 接口及实现类

  - 接口
  - 用于处理 java.sql.ResultSet，将数据按要求转换为另一种形式
  - ResultSetHandler 接口提供了一个单独的方法：Object handle (java.sql.ResultSet .rs)
  - 主要实现类

    - ArrayHandler：把结果集中的第一行数据转成对象数组。
    - ArrayListHandler：把结果集中的每一行数据都转成一个数组，再存放到List中。
    - BeanHandler：将结果集中的第一行数据封装到一个对应的JavaBean实例中。
    - BeanListHandler：将结果集中的每一行数据都封装到一个对应的JavaBean实例中，存放到List里。
    - ColumnListHandler：将结果集中某一列的数据存放到List中。
    - KeyedHandler(name)：将结果集中的每一行数据都封装到一个Map里，再把这些map再存到一个map里，其key为指定的key。
    - MapHandler：将结果集中的第一行数据封装到一个Map里，key是列名，value就是对应的值。
    - MapListHandler：将结果集中的每一行数据都封装到一个Map里，然后再存放到List
    - ScalarHandler：查询单个值对象



## JDBC总结

```java
总结
  @Test
  public void testUpdateWithTx() {

  Connection conn = null;
  try {
    // 1.获取连接的操作（
    // ① 手写的连接：JDBCUtils.getConnection();
    // ② 使用数据库连接池：C3P0;DBCP;Druid
    // 2.对数据表进行一系列CRUD操作
    // ① 使用PreparedStatement实现通用的增删改、查询操作（version 1.0 \ version 2.0)
    // version2.0的增删改public void update(Connection conn,String sql,Object ... args){}
    // version2.0的查询 public <T> T getInstance(Connection conn,Class<T> clazz,String sql,Object ... args){}
    // ② 使用dbutils提供的jar包中提供的QueryRunner类

    // 提交数据
    conn.commit();


  } catch (Exception e) {
    e.printStackTrace();


    try {
      // 回滚数据
      conn.rollback();
    } catch (SQLException e1) {
      e1.printStackTrace();
    }

  }finally{
    // 3.关闭连接等操作
    // ① JDBCUtils.closeResource();
    // ② 使用dbutils提供的jar包中提供的DbUtils类提供了关闭的相关操作
  }
  
}
```

