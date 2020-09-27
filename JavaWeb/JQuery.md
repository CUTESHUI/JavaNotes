## jQuery

#### 介绍

- jQuery，就是 JavaScript 和查询（Query），它就是辅助 JavaScript 开发的 js 类库
- 核心思想
  - 它的核心思想是 write less, do more(写得更少,做得更多)
  - 实现了很多浏览器的兼容问题

- 优点
  - 免费、开源
  - 语法设计使开发更加便捷，例如操作文档对象、选择 DOM 元素、 制作动画效果、事件处理、使用 Ajax 以及其他功能



#### 核心函数

- $ 是 jQuery 的核心函数，能完成 jQuery 的很多功能，$( ) 就是调用 $ 这个函数

  - 传入参数为 [ 函数 ] 时： 表示页面加载完成之后。相当于 window.onload = function ( ){ } 
  - 传入参数为 [ HTML 字符串 ] 时： 会对我们创建这个 html 标签对象 
  - 传入参数为 [ 选择器字符串 ] 时： 
    - $(“ #id 属性值 ”); id 选择器，根据 id 查询标签对象 
    - $(“ 标签名 ”); 标签名选择器，根据指定的标签名查询标签对象 
    - $(“ .class 属性值 ”); 类型选择器，可以根据 class 属性查询标签对象 

  - 传入参数为 [ DOM 对象 ] 时： 会把这个 dom 对象转换为 jQuery 对象



#### jQuery 对象和 dom 对象区分

- Dom 对象 

  - 通过 getElementById ( )查询出来的标签对象是 Dom 对象 
  - 通过 getElementsByName ( ) 查询出来的标签对象是 Dom 对象 
  - 通过 getElementsByTagName ( ) 查询出来的标签对象是 Dom 对象 
  - 通过 createElement ( ) 创建的对象，是 Dom 对象 
  - DOM 对象 Alert 出来的效果是：[ object HTML 标签名 Element ] 

- jQuery 对象 
  - 通过 jQuery 提供的 API 创建的对象，是 jQuery 对象 
  - 通过 jQuery 包装的 Dom 对象，也是 jQuery 对象 
  - 通过 jQuery 提供的 API 查询到的对象，是 jQuery 对象
  -  jQuery 对象 Alert 出来的效果是：[ object Object ]



#### jQuery 对象的本质是什么？ 

- jQuery 对象是 dom 对象的数组 + jQuery 提供的一系列功能函数



#### jQuery 对象和 Dom 对象使用区别

- jQuery 对象不能使用 DOM 对象的属性和方法 

- DOM 对象也不能使用 jQuery 



#### Dom 对象和 jQuery 对象互转

- dom 对象转化为 jQuery 对象
  - 先有 DOM 对象 
  - $( DOM 对象 ) 就可以转换成为 jQuery 对象 

- jQuery 对象转为 dom 对象
  - 先有 jQuery 对象 
  - jQuery 对象[下标]取出相应的 DOM 对象



#### jQuery 选择器

- 基本选择器
  - \#ID 选择器：根据 id 查找标签对象 
  - .class 选择器：根据 class 查找标签对象 
  - element 选择器：根据标签名查找标签对象 
  - \* 选择器：表示任意的，所有的元素 
  - selector1，selector2 组合选择器：合并选择器 1，选择器 2 的结果并返回

- 层级选择器
  - ancestor descendant 后代选择器 ：在给定的祖先元素下匹配所有的后代元素 
  - parent > child 子元素选择器：在给定的父元素下匹配所有的子元素 
  - prev + next 相邻元素选择器：匹配所有紧接在 prev 元素后的 next 元素 
  - prev ~ sibings 之后的兄弟元素选择器：匹配 prev 元素之后的所有 siblings 元素

- 过滤选择器
  - 基本过滤选择器
    - :first 获取第一个元素 
    - :last 获取最后个元素 
    - :not(selector) 去除所有与给定选择器匹配的元素 
    - :even 匹配所有索引值为偶数的元素，从 0 开始计数 
    - :odd 匹配所有索引值为奇数的元素，从 0 开始计数 
    - :eq(index) 匹配一个给定索引值的元素 
    - :gt(index) 匹配所有大于给定索引值的元素 
    - :lt(index) 匹配所有小于给定索引值的元素 
    - :header 匹配如 h1, h2, h3 之类的标题元素 
    - :animated 匹配所有正在执行动画效果的元素
  - 内容过滤器
    - :contains(text) 匹配包含给定文本的元素 
    - :empty 匹配所有不包含子元素或者文本的空元素 
    - :parent 匹配含有子元素或者文本的元素 
    - :has(selector) 匹配含有选择器所匹配的元素的元素
  - 属性过滤器
    - [attribute] 匹配包含给定属性的元素
    - [attribute=value] 匹配给定的属性是某个特定值的元素 
    - [attribute!=value] 匹配所有不含有指定的属性，或者属性不等于特定值的元素
    - [attribute^=value] 匹配给定的属性是以某些值开始的元素 
    - [attribute$=value] 匹配给定的属性是以某些值结尾的元素 
    - [attribute*=value] 匹配给定的属性是以包含某些值的元素 
    - [attrSel1] [attrSel2] [attrSelN] 复合属性选择器，需要同时满足多个条件时使用
  - 表单过滤器
    - :input 匹配所有 input, textarea, select 和 button 元素 
    - :text 匹配所有 文本输入框 
    - :password 匹配所有的密码输入框 
    - :radio 匹配所有的单选框 
    - :checkbox 匹配所有的复选框 
    - :submit 匹配所有提交按钮 
    - :image 匹配所有 img 标签 
    - :reset 匹配所有重置按钮 
    - :button 匹配所有 input type=button <button>按钮 
    - :file 匹配所有 input type=file 文件上传 
    - :hidden 匹配所有不可见元素 display:none 或 input type=hidden
  - 表单对象属性过滤器
    - :enabled 匹配所有可用元素 
    - :disabled 匹配所有不可用元素 
    - :checked 匹配所有选中的单选，复选，和下拉列表中选中的 option 标签对象 
    - :selected 匹配所有选中的 option



#### jQuery 元素筛选

- eq ( )：获取给定索引的元素 功能跟 :eq() 一样 
- first ( )：获取第一个元素 功能跟 :first 一样 
- last ( )：获取最后一个元素 功能跟 :last 一样 
- filter (exp)：留下匹配的元素 
- is (exp)：判断是否匹配给定的选择器，只要有一个匹配就返回，true 
- has (exp)：返回包含有匹配选择器的元素的元素  
- not (exp)：删除匹配选择器的元素  
- children (exp)：返回匹配给定选择器的子元素  
- find (exp)：返回匹配给定选择器的后代元素 
- nextAll ( )：返回当前元素后面所有的兄弟元素 
- nextUntil ( )：返回当前元素到指定匹配的元素为止的后面元素
- parent ( )：返回父元素 
- prev (exp)：返回当前元素的上一个兄弟元素 
- prevAll ( )：返回当前元素前面所有的兄弟元素 
- prevUnit (exp)：返回当前元素到指定匹配的元素为止的前面元素 
- siblings (exp)：返回所有兄弟元素 
- add ( )：把 add 匹配的选择器的元素添加到当前 jquery 对象中