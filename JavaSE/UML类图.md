## UML类图



比如 Person 类

| Person                 |
| ---------------------- |
| - name ： String       |
| <u>+ Perosn( )</u>     |
| + setName( )           |
| + getName( )  ：String |



#### 说明

- “ + ” 表示 public 类型
- “ - ”  表示 private 类型
- “ # ” 表示 protected 类型
- 有 <u>下划线</u> 的表示构造器
-  ：后的表示数据类型
- 有 ： 表示有返回值
- 写法 ： 权限修饰符 ( + - # ) 方法名( 参数类型 参数名 ) ：返回值类型