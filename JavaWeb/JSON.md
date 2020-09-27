## JSON

#### 什么是 JSON

- JSON (JavaScript Object Notation， JS 对象标记) 是一种轻量级的数据交换格式，目前使用特别广泛
- 采用完全独立于编程语言的**文本格式**来存储和表示数据
- 简洁和清晰的层次结构使得 JSON 成为理想的数据交换语言
- 易于人阅读和编写，同时也易于机器解析和生成，并有效地提升网络传输效率



在 JavaScript 语言中，一切都是对象。因此，任何 JavaScript 支持的类型都可以通过 JSON 来表示，例如字符串、数字、对象、数组等。看看他的要求和语法格式：

- 对象表示为键值对，数据由逗号分隔
- 花括号保存对象
- 方括号保存数组



#### JSON 键值对

- 用来保存 JavaScript 对象的一种方式
- 和 JavaScript 对象的写法也大同小异
- 键/值对组合中的键名写在前面并用双引号 "" 包裹，使用冒号 : 分隔，然后紧接着值：

```json
{"name": "QinJiang"}
{"age": "3"}
{"sex": "男"}
```

- JSON 是 JavaScript 对象的字符串表示法，它使用文本表示一个 JS 对象的信息，本质是一个字符串

```json
var obj = {a: 'Hello', b: 'World'}; //这是一个对象，注意键名也是可以使用引号包裹的
var json = '{"a": "Hello", "b": "World"}'; //这是一个 JSON 字符串，本质是一个字符串
```



#### JSON 和 JavaScript 对象互转

- 要实现从 JSON字符串转换为 JavaScript 对象，使用 JSON.parse( ) 方法：

```json
var obj = JSON.parse('{"a": "Hello", "b": "World"}');
//结果是 {a: 'Hello', b: 'World'}
```

- 要实现从 JavaScript 对象转换为 JSON字符串，使用 JSON.stringify( ) 方法：

```json
var json = JSON.stringify({a: 'Hello', b: 'World'});
//结果是 '{"a": "Hello", "b": "World"}'
```



#### FastJSON

- fastjson.jar 是阿里开发的一款专门用于 Java开发的包，可以方便的实现 json 对象与 JavaBean对象的相互转换
- 实现 json 的转换方法很多，最后的实现结果都是一样的
- POM依赖

```xml
<dependency>
   <groupId>com.alibaba</groupId>
   <artifactId>fastjson</artifactId>
   <version>1.2.60</version>
</dependency>
```

- FastJSON 三个主要的类
  - **JSONObject 代表 json 对象** 
    - JSONObject 实现了 Map接口，猜想  JSONObject 底层操作是由Map实现的
    - JSONObject 对应json对象，通过各种形式的get( )可以获取 json对象中的数据，也可利用诸如 size( )，isEmpty( )等方法获取"键：值"对的个数和判断是否为空
    - 其本质是通过实现 Map接口并调用接口中的方法完成

  - **JSONArray 代表 json 对象数组**
    - 内部是有 List 接口中的方法来完成操作的。
  - **JSON 代表 JSONObject 和 JSONArray 的转化**
    - JSON类源码分析与使用
    - 仔细观察这些方法，主要是实现 json对象、json对象数组、javabean对象、json字符串之间的相互转化