## 第四天JAVA杂七杂八

1. 程序流程控制：顺序结构、分支结构、循环结构

2.  要从键盘获取不同数据类型的数据，输入，需要使用 Scanner 类

   - 第一步 导包 ：import java.util.Scanner;

   - 第二步 Scanner实例化 ： Scanner scan = new Sanner(System.in);

   - 第三步 调用Scanner方法
     - String ： .next()
     - int : .nextInt()
     - short : nextShort()
     - long : nextLong()
     - double : nextDouble()
     - float : nextFloat()
     - char 没有对应的方法

3. 如果多个条件表达式没有交集的关系，那判断语句的声明在上在下都很困难

4. 如果多个表达式有交集关系，需要考虑清楚哪个语句在上，毕竟程序是顺序执行

5. 如果多个表达式有包含关系，一般来说，需要将范围小的放在范围大的上面，否则，范围小的就没机会执行了

6. if else 不写 { }，只能罩住一行

7. else 看不出和哪个 if 是一对时，就近原则

8. if ( true ) 才执行

9. switch( ) 中的表达式只能是以下六个类型： byte、 short、 int、char、String类型 ( JDK 7.0 新增 )、枚举类型 ( JDK5.0 新增 )

10. switch的 default，相当于 if else 的 else

11. switch( )中，有多个执行结果相同时，可以考虑合并