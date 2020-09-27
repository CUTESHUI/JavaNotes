## HTML、CSS

#### HTML简介

- Hyper Text Markup Language

- 超文本标记语言

- HTML 通过标签来标记要显示的网页中的各个部

- 网页文件本身是一种文本文件， 通过在文本文件中添加标记符，可以告诉浏览器如何显示其中的内容（如：文字如何处理，画 

  面如何安排，图片如何显示等） 



#### HTML文件的书写规范

<html> 表示整个 html 页面的开始 

<head> 头信息 

<title>标题</title> 标题 

</head> 

<body> body 是页面的主体内容 

页面主体内容 

</body> 

</html> 表示整个 html 页面的结束 

Html 的代码注释 <!-- 这是 html 注释，可以在页面右键查看源代码中看到 -->



#### HTML 标签介绍 

- 标签的格式: 
  - <标签名>封装的数据</标签名> 

- 标签名大小写不敏感

- 标签拥有自己的属性 
  - 分为基本属性
    - bgcolor=*"red"* 可以修改简单的样式效果 
  - 事件属性
    - onclick="alert('你好！');" 可以直接设置事件响应后的代码。 

- 标签又分为，单标签和双标签
  - 单标签格式
    -  <标签名 /> br 换行 hr 水平线 
  - 双标签格式
    - <标签名> ...封装的数据...</标签名>



#### 标签的语法

- <!-- ①标签不能交叉嵌套 --> 

正确：<div><span>早安，尚硅谷</span></div> 

错误：<div><span>早安，尚硅谷</div></span> 

- <!-- ②标签必须正确关闭 --> 

<!-- i.有文本内容的标签： -->正确：<div>早安，尚硅谷</div> 

错误：<div>早安，尚硅谷 

- <!-- ii.没有文本内容的标签： --> 

正确：<br />

错误：<br>

- <!-- ③属性必须有值，属性值必须加引号 --> 

正确：<font color=*"blue"*>早安，尚硅谷</font> 

错误：<font color=*blue*>早安，尚硅谷</font> 

错误：<font color>早安，尚硅谷</font> 

- <!-- ④注释不能嵌套 --> 

正确：<!-- 注释内容 --> <br/>

错误：<!-- <!-- 这是错误的 html 注释 --> --> 

- 注意事项

  - html 代码不是很严谨

  - 有时候标签不闭合，也不会报错



#### font 字体标签

<!-- 

 <font> 标签是字体

color 属性修改颜色 

face 属性修改字体 

size 属性修改文本大小 

--> 

```html
<body> 
  <font color="red" face="宋体" size="7">我是字体标签</font> 
</body>
```



#### 标题标签

<!-- 

 <h1> 标签是标题


h1 - h6 都是标题标签 

h1 最大 

h6 最小

 

align 属性是对齐属性 

left 左对齐  (默认) 

center 剧中 

right 右对齐 

--> 

```html
<body>
  <h1 align="left">标题 1</h1> 
  <h2 align="center">标题 2</h2> 
  <h3 align="right">标题 3</h3> 
  <h4>标题 4</h4> 
  <h5>标题 5</h5> 
  <h6>标题 6</h6> 
  <h7>标题 7</h7> 
</body>
```



#### 超链接标签

<!-- 

 <a> 标签是超链接 

href 属性设置连接的地址 

target 属性设置哪个目标进行跳转 

_self 表示当前页面 (默认) 

_blank 表示打开新页面来进行跳转 

-->

```html
<body> 
  <a href="http://localhost:8080">百度</a><br/> <a href="http://localhost:8080" target="_self">百度_self</a>
  <br/>
  <a href="http://localhost:8080" target="_blank">百度_blank</a>
  <br/> 
</body>
```



#### 列表标签

<!-- <ul>、<li>标签是列表

ul 是无序列表 

type 属性可以修改列表项前面的符号 

li 是列表项 

--> 

```html
<body>
  <ul type="none"> 
    <li>赵四</li> 
    <li>刘能</li> 
    <li>小沈阳</li> 
    <li>宋小宝</li> 
  </ul> 
</body>
```



#### 图片标签

<!--

 <img> 标签是图片

*img* *标签是图片标签**,**用来显示图片* 

*src* *属性可以设置图片的路径* 

*width* *属性设置图片的宽度* 

*height* *属性设置图片的高度* 

*border* *属性设置图片边框大小* 

*alt*  属性设置当指定路径找不到图片时**,**用来代替显示的文本内容

*-->* 

- 在 web 中路径分为相对路径和绝对路径两种 

  - 相对路径： 
    - . 表示当前文件所在的目录 
    - .. 表示当前文件所在的上一级目录 
    - 文件名 表示当前文件所在目录的文件，相当于 ./文件名 ./ 可以省略 

  - 绝对路径：
    - 正确格式： http://ip:port/工程名/资源路径* 
    - 错误格式： 盘符:/目录/文件名

```html
<body>
  <img src="1.jpg" width="200" height="260" border="1" alt="美女找不到"/> 
  <img src="../2.jpg" width="200" height="260" /> 
  <img src="../imgs/3.jpg" width="200" height="260" /> 
  <img src="../imgs/4.jpg" width="200" height="260" /> 
  <img src="../imgs/5.jpg" width="200" height="260" /> 
  <img src="../imgs/6.jpg" width="200" height="260" /> 
</body>
```



#### 表格标签

<!-- 

<tr>、<th>、<td> 标签是表格

table 标签是表格标签 

border 设置表格标签 

width 设置表格宽度 

height 设置表格高度 

align 设置表格相对于页面的对齐方式cellspacing 设置单元格间距 

tr 是行标签 

th 是表头标签 

td 是单元格标签 

align 设置单元格文本对齐方式 

b 是加粗标签 

-->

```html
<body>
  <table align="center" border="1" width="300" height="300" cellspacing="0"> 
    <tr><th>1.1</th> 
      <th>1.2</th> 
      <th>1.3</th> 
    </tr>
    <tr><
      td>2.1</td> 
      <td>2.2</td> 
      <td>2.3</td> 
    </tr> 
    <tr>
      <td>3.1</td> 
      <td>3.2</td> 
      <td>3.3</td> 
    </tr> 
  </table> 
</body>
```



#### 跨行跨列表格

<!--

<tr/th/td colspan=" ">

colspan 属性设置跨列 

rowspan 属性设置跨行 

-->



#### **了解** **iframe** **框架标签** (内嵌窗口)

<!--

ifarme 标签可以在页面上开辟一个小区域显示一个单独的页面 

ifarme 和 a 标签组合使用的步骤: 

1. 在 iframe 标签中使用 name 属性定义一个名称 

2. 在 a 标签的 target 属性上设置 iframe 的 name 的属性值 

-->

```html
<body> 我是一个单独的完整的页面<br/><br/>  
  <iframe src="3.标题标签.html" width="500" height="400" name="abc"></iframe> 
  <br/> 
  <ul>
    <li>
      <a href="0-标签语法.html" target="abc">0-标签语法.html</a>
    </li> 
    <li>
      <a href="1.font 标签.html" target="abc">1.font 标签.html</a>
    </li> 
    <li>
      <a href="2.特殊字符.html" target="abc">2.特殊字符.html</a>
    </li>
  </ul>
</body>
```



#### 表单标签

<!--

 <form> 标签是表单

input type=text 是文件输入框 value 设置默认显示内容 

input type=password 是密码输入框 value 设置默认显示内容 

input type=radio 是单选框 name 属性可以对其进行分组 checked="checked"表示默认选中input type=checkbox 是复选框 checked="checked"表示默认选中 

input type=reset 是重置按钮 value 属性修改按钮上的文本 

input type=submit 是提交按钮 value 属性修改按钮上的文本 

input type=button 是按钮 value 属性修改按钮上的文本 

input type=file 是文件上传域 

input type=hidden 是隐藏域 当我们要发送某些信息，而这些信息，不需要用户参与，就可以使用隐藏域（提交的 

时候同时发送给服务器） 

select 标签是下拉列表框 

option 标签是下拉列表框中的选项 selected="selected"设置默认选中 

textarea 表示多行文本输入框 （起始标签和结束标签中的内容是默认值） 

rows 属性设置可以显示几行的高度 

cols 属性设置每行可以显示几个字符宽度 

--> 

- 表单提交细节

<!--

form 标签是表单标签 

action 属性设置提交的服务器地址 

method 属性设置提交的方式 GET(默认值)或 POST 

表单提交的时候，数据没有发送给服务器的三种情况： 

1. 表单项没有 name 属性值 

2. 单选、复选（下拉列表中的 option 标签）都需要添加 value 属性，以便发送给服务器 

3. 表单项不在提交的 form 标签中 

GET 请求的特点是： 

1. 浏览器地址栏中的地址是：action 属性[+?+请求参数] 

请求参数的格式是：name=value&name=value 

2. 不安全 

3. 它有数据长度的限制 

POST 请求的特点是： 

1. 浏览器地址栏中只有 action 属性值 

2. 相对于 GET 请求要安全 

3. 理论上没有数据长度的限制 

-->

```html
表单显示
<body>
  <form> 
    用户名称：<input type="text" value="默认值"/><br/> 
    用户密码：<input type="password" value="abc"/><br/> 
    确认密码：<input type="password" value="abc"/><br/> 
    性别：<input type="radio" name="sex"/>男<input type="radio" name="sex" checked="checked" />女<br/> 
    兴趣爱好：<input type="checkbox" checked="checked" />Java<input type="checkbox" />JavaScript<input type="checkbox" />C++<br/> 
    国籍：<select> 
    	<option>--请选择国籍--</option> 
    	<option selected="selected">中国</option> 
    	<option>美国</option> 
    	<option>小日本</option> </select><br/> 
    自我评价：<textarea rows="10" cols="20">我才是默认值</textarea><br/>
    <input type="reset" value="abc" /> 
    <input type="submit"/> 
  </form> 
</body>

表单格式化

```



#### 其他标签

<!--

div 标签 默认独占一行 

span 标签 它的长度是封装数据的长度 

p 段落标签 默认会在段落的上方或下方各空出一行来（如果已有就不再空） 

-->

```html
<body> 
  <div>div 标签 1</div> 
  <div>div 标签 2</div> 
  <span>span 标签 1</span> 
  <span>span 标签 2</span> 
  <p>p 段落标签 1</p> 
  <p>p 段落标签 2</p> 
</body>
```



#### CSS介绍

- 是层叠样式表单

- 是用于增强、控制网页样式，允许将样式信息与网页内容分离的一种标记性语言

- 语法规则

  - 选择器

    - 浏览器根据 “选择器” 决定受 CSS 样式影响的 HTML 元素（标签）

    - 格式：

      标签名{ 

      ​				属性：值; 

      }

    - div{

      ​		border: 1px solid yellow; 

      ​		color: blue; 

      ​		font-size: 30px; 

      }

  - 属性 / property

    - 你要改变的样式名，并且每个属性都有一个值

    - 属性和值被冒号分开，并由花括号包围，这样就组成了一个完整的样式声明
      
    - 
      
    - p {

      ​		color: blue;

      }

  - 多个声明 / declaration

      - 如果要定义不止一个声明，则需要用分号将每个声明分开

      - 尽量在每条声明的末尾都加上分号

      - p{ 

        ​		color:red;

        ​		font-size:30px; 

        } 

- 注：一般每行只描述一个属性 
  
- CSS 注释：/*注释内容*/ 



#### **CSS** **和** **HTML** **的结合方式** 

1. 第一种

- 在标签的 style 属性上设置”key: value value ...”，修改标签样式

```html
<!--需求 1：分别定义两个 div、span 标签，分别修改每个 div 标签的样式为：边框 1 个像素，实线，红色。--> 
<div style="border: 1px solid red;">div 标签 1</div>
<div style="border: 1px solid red;">div 标签 2</div> 
<span style="border: 1px solid red;">span 标签 1</span> 
<span style="border: 1px solid red;">span 标签 2</span>
```

- 缺点：
  - 如果标签多了，样式多了，代码量非常庞大
  - 可读性非常差
  - CSS 代码没什么复用性可方言

2. 第二种
- 在 head 标签中，使用 style 标签来定义各种自己需要的 css 样式，格式如下： 

  xxx {

  ​			Key : value value; 

  ​		} 

```html
<!DOCTYPE html> 
<html lang="en"> 
<head> 
  <meta charset="UTF-8"> 
  <title>Title</title> 
  <!--style 标签专门用来定义 css 样式代码--> 
  <style type="text/css"> 
    /* 需求 1：分别定义两个 div、span 标签，分别修改每个 div 标签的样式为：边框 1 个像素，实线，红色。*/ 
    div{
      	  border: 1px solid red; 
    }
    span{
      	  border: 1px solid red;
    } 
  </style> 
</head> 
  <body> 
    <div>div 标签 1</div> 
    <div>div 标签 2</div> 
    <span>span 标签 1</span> 
    <span>span 标签 2</span> 
  </body> 
</html>
```

- 缺点
  - 只能在同一页面内复用代码，不能在多个页面中复用 css 代码
  - 维护起来不方便，实际的项目中会有成千上万的页面，要到每个页面中去修改，工作量太大了

3. 第三种

- 把 css 样式写成一个单独的 css 文件，再通过 link 标签引入即可复用。 
- 使用 html 的 <link rel=*"stylesheet"* type=*"text/css"* href=*"./styles.css" /*> 标签 导入 css 样 式文件

```html
// CSS 文件
div{
			border: 1px solid yellow; 
}
span{
			border: 1px solid red;
}

2. Html 文件
<!DOCTYPE html> 
<html lang="en"> 
  <head> 
    <meta charset="UTF-8"> 
    <title>Title</title> 
    <!--link 标签专门用来引入 css 样式代码--> 
    <link rel="stylesheet" type="text/css" href="1.css"/> 
  </head> 
  <body> 
    <div>div 标签 1</div> 
    <div>div 标签 2</div> 
    <span>span 标签 1</span> 
    <span>span 标签 2</span> 
  </body> 
</html>
```



#### id 选择器

- 通过 id 属性选择，哪个选择器的去使用这个样式
- 格式：

\#id 属性值{ 

​	属性：值; 

}

```html
<!DOCTYPE html> 
<html> 
  <head> 
    <meta charset="UTF-8"> 
    <title>ID 选择器</title> 
    <style type="text/css"> 
      #id001{ color: blue; 
        font-size: 30px; 
        border: 1px yellow solid;
}
      #id002{ 
        color: red; 
        font-size: 20px; 
        border: 5px blue dotted; 
      } 
    </style> 
  </head> 
  <body> 
    <!-- 需求 1：分别定义两个 div 标签， 
		第一个 div 标签定义 id 为 id001 ，然后根据 id 属性定义 css 样式修改字体颜色为蓝色， 字体大小 30 个像素。边框为 1 像素黄色实线。 
		第二个 div 标签定义 id 为 id002 ，然后根据 id 属性定义 css 样式 修改的字体颜色为红色，字体大小 20 个像素。 边框为 5 像素蓝色点线。 --> 
    <div id="id002">div 标签 1</div> 
    <div id="id001">div 标签 2</div> 
  </body> 
</html>
```



#### **class** **选择器（类选择器）** 

- 通过 class 属性选择，哪个选择器去使用这个样式

- 格式： 

  **.**class 属性值{ 

  ​	属性：值; 

  } 

```html
<!DOCTYPE html> 
<html> 
  <head> 
    <meta charset="UTF-8"> 
    <title>class 类型选择器</title> 
    <style type="text/css"> 
      .class01{ 
        color: blue; 
        font-size: 30px; border: 1px solid yellow; 
      }
      .class02{ 
        color: grey; 
        font-size: 26px; border: 1px solid red; 
      } 
    </style> 
  </head> 
  <body> 
    <!--需求 1：修改 class 属性值为 class01 的 span 或 div 标签，字体颜色为蓝色，字体大小 30 个像素。边框为 1 像素黄色实线。 				 需求 2：修改 class 属性值为 class02 的 div 标签，字体颜色为灰色，字体大小 26 个像素。边框为 1 像素红色实线。 --> 
    <div class="class02">div 标签 class01</div> 
    <div class="class02">div 标签</div> 
    <span class="class02">span 标签 class01</span> 
    <span>span 标签 2</span> 
  </body> 
</html>
```



#### 组合选择器

- 器可以让多个选择器共用同一个 css 样式代码
- 格式：

选择器 1，选择器 2，选择器 n{ 

​	属性：值; 

} 

```html
<!DOCTYPE html> 
<html> 
  <head> 
    <meta charset="UTF-8"> <title>class 类型选择器</title> 
    <style type="text/css"> 
      .class01 , #id01{ 
        color: blue; font-size: 20px; 
        border: 1px yellow solid; 
      } 
    </style> 
  </head> 
  <body> 
    <!-- 需求 1：修改 class="class01" 的 div 标签 和 id="id01" 所有的 span 标签， 字体颜色为蓝色，字体大小 20 个像素。边框为 1 像素黄色实线。 --> 
    <div id="id01">div 标签 class01</div> 
    <br /> 
    <span >span 标签</span> 
    <br /> 
    <div>div 标签</div> 
    <br /> 
    <div>div 标签 id01</div> 
    <br /> 
  </body> 
</html>
```



#### 常用样式

- 字体颜色 

  - color：red； 
  - 颜色可以写颜色名如：black、blue、red、green 等 
  - 颜色也可以写 rgb 值和十六进制表示值：如 rgb(255,0,0)，#00F6DE，如果写十六进制值必 须加#

- 宽度
  - width:19px; 
  - 宽度可以写像素值：19px； 
  - 也可以写百分比值：20%； 

- 高度
  - height:20px; 
  - 高度可以写像素值：19px； 
  - 也可以写百分比值：20%； 

- 背景颜色 
  
- background-color:#0F2D4C 
  
- 字体样式
  - color：#FF0000；字体颜色红色 
  - ont-size：20px; 字体大小 

- 红色 1 像素实线边框 
  
- border：1px solid red; 
  
- DIV 居中 
  - margin-left: auto; 
  - margin-right: auto; 

- 文本居中

  - text-align: center;

- 超连接去下划线 

  - text-decoration: none; 

- 表格细线 

  table { 

  ​	border: 1px solid black; /*设置边框*/ 

  ​	border-collapse: collapse; /*将边框合并*/ 

  }

  td,th {

  ​	border: 1px solid black; /*设置边框*/ 

  } 

- 列表去除修饰 


  ul { 

  ​	list-style: none; 

  } 





