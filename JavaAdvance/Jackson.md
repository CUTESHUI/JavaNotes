## Jackson

#### 优点

- 解析大文件的速度比较快
- 运行时占用的内存比较少，性能更佳
- API 很灵活，容易进行扩展和定制



#### 组成

- jackson-core，核心包，提供基于“流模式”解析的相关 API，包括 JsonPaser 和 JsonGenerator
- jackson-annotations，注解包，提供标准的注解功能
- jackson-databind ，数据绑定包，提供基于“对象绑定”解析的相关 API （ ObjectMapper ） 和基于“树模型”解析的相关 API （JsonNode）



#### ObjectMapper

- Jackson 最常用的 API 就是基于”对象绑定” 的 ObjectMapper
- ObjectMapper 通过 **writeValue** 的系列方法将 Java 对象序列化为 JSON，并且可以存储成不同的格式
  - writeValueAsString(Object value)：将对象存储成字符串
  - writeValueAsBytes(Object value)：将对象存储成字节数组
  - writeValue(File resultFile, Object value)：将对象存储成文件

- 例子

```java
public class Demo {
    public static void main(String[] args) throws JsonProcessingException {
        Writer wanger = new Writer("tom", 18);
        ObjectMapper mapper = new ObjectMapper();
        String jsonString = mapper.writerWithDefaultPrettyPrinter().writeValueAsString(wanger);
        System.out.println(jsonString);
    }
}
  
@Data
class Writer{
  private String name;
  private int age;

  @Override
  public String toString() {
    return "Writer{" +
      "name='" + name + '\'' +
      ", age=" + age +
      '}';
  }
}
```

```java
{
  "name" : "tom",
  "age" : 18
}
```

- 不是所有的字段都支持序列化和反序列化，需要符合以下规则：
  - 如果字段的修饰符是 public，则该字段可序列化和反序列化（不是标准写法）
  - 如果字段的修饰符不是 public，但是它的 getterr( )和 setterr( )是 public，则该字段可序列化和反序列化，@Data 的都是public
  - getter( ) 用于序列化，setter( ) 用于反序列化
  - 如果字段只有 public 的 setter( ) ，而无 public 的 getter( ) ，则该字段只能用于反序列化

- 如果想更改默认的序列化和反序列化规则，需要调用 ObjectMapper 的 setVisibility( )。否则将会抛出 InvalidDefinitionException 异常

- ObjectMapper 通过 **readValue** 的系列方法从不同的数据源将 JSON 反序列化为 Java 对象。
  - readValue(String content, Class<T> valueType)：将字符串反序列化为 Java 对象
  - readValue(byte[] src, Class<T> valueType)：将字节数组反序列化为 Java 对象
  - readValue(File src, Class<T> valueType)：将文件反序列化为 Java 对象
- 例子

```java
public class Demo {
  public static void main(String[] args) throws JsonProcessingException {
    ObjectMapper mapper = new ObjectMapper();
    String jsonString = "{\n" +
      "  \"name\" : \"tom\",\n" +
      "  \"age\" : 18\n" +
      "}";
    Writer deserializedWriter = mapper.readValue(jsonString, Writer.class);
    System.out.println(deserializedWriter);
  }
}

@Data
class Writer{
  private String name;
  private int age;

  @Override
  public String toString() {
    return "Writer{" +
      "name='" + name + '\'' +
      ", age=" + age +
      '}';
  }
}
```

```java
Writer{name='tom', age=18}
```



#### 处理日期格式

- 对于日期类型的字段，比如说 java.util.Date，如果不指定格式，序列化后将显示为 long 类型的数据，这种默认格式的可读性很差

```java
{
  "age" : 18,
  "birthday" : 1606358621209
}
```

- Jackson可以这样

  - 在 getter 上使用 @JsonFormat

  ```java
  private Date birthday;
  
  // GMT+8 是指格林尼治的标准时间，在加上八个小时表示你现在所在时区的时间
  @JsonFormat(timezone = "GMT+8", pattern = "yyyy-MM-dd HH:mm:ss")
  public Date getBirthday() {
    return birthday;
  }
  
  public void setBirthday(Date birthday) {
    this.birthday = birthday;
  }
  ```

  ```java
  {
    "age" : 18,
    "birthday" : "2020-11-26 03:02:30"
  }
  ```

  - 调用 ObjectMapper 的 setDateFormat( )

  ```java
  ObjectMapper mapper = new ObjectMapper();
  mapper.setDateFormat(StdDateFormat.getDateTimeInstance());
  Writer wanger = new Writer("tom", 18);
  wanger.setBirthday(new Date());
  String jsonString = mapper.writerWithDefaultPrettyPrinter().writeValueAsString(wanger);
  System.out.println(jsonString);
  ```
  
```java
  {
    "name" : "tom",
    "age" : 18,
    "birthday" : "2020年11月26日 上午11:09:51"
  }
  ```



#### 字段过滤

- 在将 Java 对象序列化为 JSON 时，可能有些字段需要过滤，不显示在 JSON 中，Jackson 有一种比较简单的实现方式
- @JsonIgnore：过滤单个字段

```java
@JsonIgnore
public String getName() {
  return name;
}
```

- @JsonIgnoreProperties：过滤多个字段

```java
@JsonIgnoreProperties(value = { "age","birthday" })
class Writer{
  private String name;
  private int age;
  private Date birthday;
}
```



#### 自定义序列化和反序列化

- 当 Jackson 默认序列化和反序列化不能满足实际的开发需要时，可以自定义新的序列化和反序列化类
- 自定义的序列化类需要继承 StdSerializer，同时重写 `serialize()` 方法，利用 JsonGenerator 生成 JSON
- 定义好自定义序列化类后，要想在程序中调用它们，需要将其注册到 ObjectMapper 的 Module 中
- 例子

```java
public class CustomSerializer extends StdSerializer<Man> {
  protected CustomSerializer(Class<Man> t) {
    super(t);
  }

  public CustomSerializer() {
    this(null);
  }

  @Override
  public void serialize(Man value, JsonGenerator gen, SerializerProvider provider) throws IOException {
    gen.writeStartObject();
    gen.writeStringField("name", value.getName()); //自定义序列化中只添加了 name，age也就无法序列化，无法展示
    gen.writeEndObject();
  }
}

@Data
class Man{
  private int age;
  private String name;
}
```

```java
ObjectMapper mapper = new ObjectMapper();
SimpleModule module = new SimpleModule("CustomSerializer", new Version(1, 0, 0, null, null, null));
module.addSerializer(Man.class, new CustomSerializer());
mapper.registerModule(module);
Man man = new Man( 18,"tom");
String json = mapper.writeValueAsString(man);
System.out.println(json);
```

```java
{"name":"tom"}
```



#### 总结

- 如果只想简单的序列化和反序列化，使用 ObjectMapper 的 write 和 read 方法即可

- 如果还想更进一步的话，就需要对 ObjectMapper 进行一些自定义配置，或者加一些注解，以及直接自定义序列化和反序列化类，更贴近一些 Java 对象