## 第一个JAVA程序总结

#### 1. JAVA程序 编写 - 编译 - 运行过程

- 编写：我们将编写的 JAVA 代码保存在后缀名为 `.java` 的源文件中。

- 编译：使用 `javac` 命令编译源文件，生成字节码文件。格式：javac 文件名.java
- 运行：使用 `java.exe` 命令解释运行字节码文件。格式： java 类名



#### 2. class 声明问题

- 在一个源文件中，可以声明多个 class，但是，最多只能有一个类声明为 public。
- public 只能出现在与文件名相同的 class 前。



#### 3. main方法

- 程序的入口是 main() 方法。

- 格式是固定的：

  ```java
  public static void main(String[] args)
  {
    
  }
  ```

- args : arguments / 参数



#### 4. 输出语句

- System.out.println()    换行 
- System.out,print()       不换行



#### 5. 编译过程

- 编译之后，会生成一个或多个字节码文件。
- 字节码文件的文件名与源文件中的类名相同。
- 换句话，有几个类就有几个字节码文件。



#### 代码：

```java
package HelloWorld;

/**
 @author CUTESHUI
 @version JDK8
 这是文档注释，也是我的第一个JAVA程序
 */

public class HelloJava {
    public static void main(String[] args){
        System.out.println("hellowjava");
        String hello = "java";
        System.out.println("hello" + hello + "!!!!");
        System.out.println(hello);
    }
}
```



```java
package HelloWorld;
/*
多行注释
 */
// 单行注释

/**
 @author CUTESHUI
 @version JDK8
 这是文档注释，也是我的第一个测试
 */

public class Test1 {
    public static void main(String[] args){
        
    }
}

/*
Error:java: class Tset2 is public, should be declared in a file named Tset2.java
public class Tset2 
{

}
*/
```

