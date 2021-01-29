## ElasticSearch

#### 启动关闭

- 启动
  - cd /usr/local/elasticsearch-7.6.1
  - bin/elasticsearch
  - http://localhost:9200
- 关闭
  - control + c

---

#### ElasticSearch-head

- 启动
  - cd /usr/local/elasticsearch-7.6.1/elasticsearch-head-master
  - npm run start
  - http://localhost:9100
- 关闭
  - control + c

---



#### 简介

- Elasticsearch is a real-time, distributed storage, search, and analytics engine
- Elasticsearch 是一个实时、分布式存储、搜索、分析的引擎



#### 为什么用

- 对模糊搜索非常擅长，搜索速度很快
- 搜索到的数据可以根据评分过滤掉大部分的，只返回评分高的给用户就好了，原生就支持排序
- 没有那么准确的关键字也能搜出相关的结果，能匹配有相关性的记录



#### 数据结构

- 根据 完整的条件 查找对应记录，叫做正向索引
  - 一本书的章节目录，通过章节名称就找到对应的页码
- 根据 某个词 / 不完整的条件 查找对应记录，叫做倒排索引
  - 通过 Value 去找 key
  - 一本书的章节目录，根据关键字算法找到找到对应的页码
- 写入数据到 Elasticsearch 时会进行分词，内置一些分词器
  - Standard  Analyzer 。按词切分，将词小写
  - Simple Analyzer。按非字母过滤，符号被过滤掉，将词小写
  - WhitespaceAnalyzer。按照空格切分，不转小写
- 分词器主要由三部分组成
  - 􏱀􏰉􏰂􏰈􏰂􏰆􏰄Character Filters：文本过滤器，去除HTML）
  - Tokenizer（按照规则切分，比如空格）
  - TokenFilter（将切分后的词进行处理，比如转成小写）




#### 术语

- Index：相当于数据库的 Table
- Type：在新版本已经废除（在以前的Elasticsearch版本，一个Index下支持多个Type--有点类似于消息队列一个topic下多个group的概念）
- Document：相当于数据库的一行记录
- Field：相当于数据库的 Column
- Mapping：相当于数据库的 Schema
- DSL：相当于数据库的SQL，读取 Elasticsearch 数据的API



#### 架构

- 一个 Elasticsearch 集群会有多个 Elasticsearch 节点，所谓节点实际上就是运行着 Elasticsearch 进程的机器
- 在众多的节点中，其中会有一个 Master Node，它主要负责维护索引元数据、负责切换主分片和副本分片身份等工作，如果主节点挂了，会选举出一个新的主节点，这样即便节点挂了，数据就不会丢
- Elasticsearch 最外层的是 Index（相当于数据库 表的概念），一个 Index 的数据我们可以分发到不同的 Node 上进行存储，叫做分片
  - Index 要分为多少个主分片和副本分片，可以通过配置设置
  - 比如集群里边有4个节点，有一个Index，想这个 Index 在4个节点上存储，那可以设置为4个分片，这4个分片的数据合起来就是 Index 数据



#### 写入流程

- 向 Elasticsearch 写入数据时，是写到主分片

- 客户端写入一条数据，到 Elasticsearch 集群里是由节点来处理这次请求：

  - 集群上的每个节点都是 coordinating node（协调节点），协调节点可以路由

    - 比如节点1接收到了请求，但发现这个请求的数据应该是由节点2处理（主分片在节点2上），那节点1会把请求转发到节点2上
    - coordinating node（协调节点）通过 hash 算法可以计算出是在哪个主分片上，然后路由到对应的节点
    - shard = hash(document_id) % (num_of_primary_shards)

  - 路由到对应的节点以及对应的主分片时，做处理
    - 将数据写到内存缓存区
    - 然后将数据写到 translog 缓存区
    - 每隔 1s 数据从 buffer 中 refresh 到 FileSystemCache 中，生成 segment 文件，一旦生成 segment 文件，就能通过索引查询
    - refresh 完，memory buffer 就清空了
    - 每隔 5s ，translog 从 buffer flush 到磁盘中
    - 定期 / 定量从 FileSystemCache 中,结合 translog 内容 flush index 到磁盘中

- 细节
  - Elasticsearch 写入的数据需要 1s 才能查询到
    - Elasticsearch 把数据先写入内存缓冲区，然后每隔 1s 刷新到文件系统缓存区，当数据被刷新到文件系统缓冲区以后，数据才可以被检索到
  - Elasticsearch 某个节点如果挂了，可能会造成 5s 的数据丢失
    - 为了防止节点宕机，内存中的数据丢失，Elasticsearch 会另写一份数据到日志文件上，但最开始的还是写到内存缓冲区，每隔 5s 才会将缓冲区的刷到磁盘中
  - 等到磁盘上的 translog 文件大到一定程度或者超过了30分钟，会触发 commit 操作，将内存中的 segement 文件异步刷到磁盘中，完成持久化操作
- 总结

  - 写内存缓冲区（定时去生成 segement，生成 translog，目的是让数据能被索引、被持久化）
  - 通过 commit 完成一次的持久化
  - 等主分片写完了以后，会将数据并行发送到副本集节点上
  - 等所有的节点写入成功就返回 ack 给协调节点
  - 协调节点返回 ack 给客户端，完成一次的写入



#### 更新和删除

- 给对应的 doc 记录打上 .delete 标识
  - 删除：打上 delete 状态
  - 更新：打上 delete 状态，然后重新新写入一条数据
- 每隔 1s 会生成一个segement 文件，segement 文件会越来越多
- Elasticsearch 有一个 merge 任务，将多个 segement 文件合并成一个 segement 文件，在合并的过程中，会把带有 delete 状态的 doc 给物理删除掉



#### 查询

- 根据 ID 查询 doc、Get

  - 检索内存的 Translog 文件
  - 检索硬盘的 Translog 文件
  - 检索硬盘的 Segement 文件

- 根据 query（搜索词）去查询匹配的 doc、Query

  - 同时去查询内存和硬盘的 Segement 文件

- 分为三个阶段

  - QUERY_AND_FETCH
    - 查询完就返回整个doc 内容
    - 只适合于只需要查一个分片的请求
  - QUERY_THEN_FETCH
    - 先查询出对应的 doc id ，然后再根据 doc id 匹配去对应的文档
    - 一般用得最多
    - 流程
      1. 客户端请求发送到集群的某个节点上。集群上的每个节点都是 coordinate node（协调节点）
      2. 然后协调节点将搜索的请求转发到所有分片上，主分片和副本分片都行
      3. 每个分片将自己搜索出的结果、doc id 返回给协调节点，由协调节点进行数据的合并、排序、分页等操作，产出最终结果
      4. 接着由协调节点根据  doc id 去各个节点上拉取实际的  document 数据，最终返回给客户端
  - DFS_QUERY_THEN_FETCH
    - 先算分，再查询
    - 分指的是 词频率（Term Frequency）、文档的频率（Document Frequency），出现频率越高，相关性就更强

- Query Phase 阶段，节点做的事：

  - 协调节点向目标分片发送查询的命令（转发请求到主分片或者副本分片上）
  - 数据节点（在每个分片内做过滤、排序等等操作），返回 doc id 给协调节点

  Fetch Phase 阶段，节点做的事：

  - 协调节点得到数据节点返回的 doc id ，对这些 doc id 做聚合，然后将目标数据分片发送抓取命令，希望拿到整个 doc 
  - 数据节点按协调节点发送的 doc id ，拉取实际需要的数据返回给协调节点