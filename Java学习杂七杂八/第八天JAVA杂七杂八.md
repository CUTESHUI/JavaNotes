## 第八天JAVA杂七杂八

1. 引用类型的变量，只可能存储两类值：null  和   地址值
2. 引用类型 ： 类、接口、数组 
3. 通过地址值，赋给栈空间的变量，指向堆空间的地址
4. 调用方法，要 new 一个所在类的对象

5. 属性赋值的 先后顺序

   1. 默认初始化 
   2. 显式初始化
   3. 构造器初始化
   4. 通过 “对象.方法“ 或 “对象.属性”

   以上操作的先后顺序：1 - 2 - 3 - 4

   注：主要看最后的，之前都是过程

6. ==，对于引用数据类型，比较的是地址值

7. 多态 b = a，= ： 赋值，地址值

8. 没有子父类关系的不可以强转，基本数据不行、类也不行

9. 不是标准的true：True、TrUe... 返回的都是true。但要是加上一些数字字母：true1、truee、True123.... 返回的都是false

10. 对属性可以赋值的位置

    - 默认初始化
    - 显示初始化
    - 构造器中初始化
    - 有了对象后，可以通过 “ 对象.属性 " or " 对象.方法 "
    - 在代码块中
    
11. 多态：形参是父类对象，父类new子类对象，编译父类方法 ( 功能 )，调用子类重写的方法