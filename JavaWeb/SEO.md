## SEO

#### 简介

- SEO（search engine optimization 搜索引擎优化）
- SEO是一种技术，主要是用于提高网站浏览量而做的优化手段



#### 为什么需要

- 简单在百度中搜索一下 Java 时，搜索记录的排名有先后的，博客园、CSDN都是靠前的。可是平台还有开源中国、简书啊等，为啥就排不到前面呢？就是SEO没有博客园、CSDN做得好
- 想要提高在搜索引擎的权重(自己的网站排得更前)就需要学习 SEO



#### 提高权重的方法

- 语义话代码（HTML 标签有自己的意义，在适当的位置用适当的标签）

  - h1~h6 多用于标题
  - ul 多用于无序列表
  - ol 多用于有序列表
  - dl 用于定义数据列表
  - em、strong 表示强调
  - div、span 是 html 标签中最没有语义的

- 有利搜索引擎

  - a：访问外部链接时要加上 rel="nofollow"，告诉搜索引擎外部链接无需追踪，加上 title 说明
  - h1：搜索引擎外认为 h1 最重要，使用 css 调整，网页正文标题用 h1，副标题用 h2，其他不要乱用 h 标签
  - br 用于文本间的换行，用在 p 内，也可以用 `<p><br/></p>`表示空行
  - caption：表格标题
  - img：使用 title、alt 说明
  - `<strong><em`>表示强调，`<em>`的权重仅次于`<strong>`,如果只想表示加粗斜体，建议使用`<b><i>`,表示强调则使用`<strong><i>`
  - 重要内容 HTML 代码放在最前面（使用 css 改变布局）
  - 重要内容不要用 JS 输出（搜索引擎看不懂 JS）
  - 尽量少使用 iframe 框架（搜索引擎不喜欢）
  - 谨慎使用 display:none（搜索引擎会过滤掉其内容，设置 z-index 代替）
  - 精简代码

- 页面结构

  - 结构布局优化：用扁平化结构（层次结构超过三层小蜘蛛就不愿意爬了）

  - 控制首页链接数量（中小网站100以内，页面导航、底部导航、锚文字链接等）

  - 扁平化的目录层次（小蜘蛛跳转3次可以到达网站内任何一个内页，网站的设计主页、栏目、内容页，不要用纵线性的结构）

  - 导航seo优化（头部、底部、内容部分，主导航、副导航、分类导航，尽量用文字，面包屑导航，在每个网站上留下面包屑，使用户可以了解网站组织形式，放于正文的左上方）

  - 内容页面的布局细节

  - - 左面正文，右面热门文章、相关文章，下面是版权信息及链接，栏目排布： 首页123456789下拉选择最赞）

  - 网站的加载速度会影响小蜘蛛的爬行，页面最好不要超过100k


