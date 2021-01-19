## Hadoop

#### 前言

- 对于大数据量的时代，遇到的问题很简单，数据量呈指数增长（硬盘容量不断提升），但是硬盘的访问速度并未与时俱进有非常大的提升，硬盘数据的写入速度更慢
- 旧的处理方式无法满足现阶段的大数据量计算和读取的需求，互联网巨头谷歌优先推出 MapReduce 并行计算模型来处理大数据量计算
- 之后由 Apache 基金会在 MapReduce 的基础上研发了 Hadoop 框架，它是一个分布式系统的基础架构，在用户不需要了解底层分布式细节的情况下，进行分布式程序的开发



#### Hadoop 简介

- Hadoop 是 Apache 旗下一个开源框架，用来开发与运行分布式应用程序来处理海量数据（大型数据集）
- Hadoop 不是指一个具体的软件或者应用，它是一个编程模型（思想）来处理实际的问题，它提供了一些基础模块或软件为此框架做支撑
- 主要的模块：

  - Hadoop HDFS：分布式存储系统
  - Hadoop YARN： 任务调度和集群资源管理的框架
  - Hadoop MapReduce：一种基于Hadoop YARN的大型数据集并行计算处理系统
  -   等等。上述每个模块有自己独立的功能，而模块之间又有相互的关联。通常我们所说的hadoop指的是Hadoop的一个生态圈
- 其它还有一些 Hadoop 生态圈中的辅助工具，主要用于特定目的或者功能等，如：
  - Hbase: 是一个分布式的、面向列的开源数据库
  - Hive：是基于 Hadoop 的一个数据仓库工具
  - Pig：运行在 Hadoop上，是对大型数据集进行分析和评估的平台
  - Spark：Hadoop 数据快速通用的计算引擎
  - ZooKeeper：一个分布式的，开放源码的分布式应用程序协调服务，是 Hadoop 和 Hbase 的重要组件



#### 优势

- 方便
  - Hadoop 部署不需要高昂的硬件设施，只需要一般的商用机器或者云计算的云服务器节点上即可完成部署运行
- 可靠
  - Hadoop 大部分是运行在商用的小型机上，会存在机器出现一些物理故障
  - Hadoop 在处理此类故障发生时，数据处理不会出问题
- 可扩展
  - Hadoop 可以通过增加集群节点，有效的扩展提升处理数据和计算的速度，以便处理更大的数据集
- 开发变得简单
  - 假如没有 Hadoop 框架的支持，自己要开发一个符合业务需求高效的并行代码将变得非常麻烦



#### Hadoop 不同版本区别

- Hadoop1.0
  - Hadoop1.0 是最早的版本，只是在 google 上发表的三篇论文转变过来的，在开发过程当中存在诸多的缺陷
  - Hadoop1.0 主要是 HDFS（分布式系统）和一个分布式计算框架（MapReduce）组成的
  - Hadoop1.0 的 NameNode 节点有且只有一个，虽然可以通过 SecondaryNameNode 进行主节点数据备份，但是存在延时情况，假如主节点挂掉，这时部分数据还未同步到 SecondaryNameNode 节点上，就会存在资源数据的缺失。因为 NameNode 是存储着 DataNode 节点等元数据信息
  - 对于 MapReduce，Hadoop1.0 也是一个简单的主从结构，是由一个主 JobTracker 和多个从的 TaskTracker 组成，而且在 Hadoop1.0 中 JobTracker 任务繁重
    - 负责接收客户端的计算任务，同时要把任务分发给 TaskTracker 进行执行
    - 通过心跳机制来管理 TaskTracker 节点的运行情况
- Hadoop2.0
  - Hadoop2.0 增加了 HDFS HA 机制，解决了 Hadoop1.0 中的单点故障问题，通过 HA 进行 standbynamenode 的热备份
  - Hadoop2.0 增加了HDFS Federation（联邦）水平扩展，支持多个 NameNode 同时运行，每一个 NameNode 分管一批目录，然后共享所有 DataNode 的存储资源，从而解决 Hadoop1.0 当中单个 NameNode 节点内存受限问题
  - Hadoop2.0 虽然 HDFS 架构上发生了一些变化，但是使用方式不变，1.0 当中相关的命令与 API 可以继续使用
  - Hadoop2.0 增加了 YARN 框架，针对 Hadoop1.0 中主 JobTracker 压力太大的不足，把 JobTracker 资源分配和作业控制分开，利用 Resource Manager 在 NameNode 上进行资源管理调度，利用 ApplicationMaster 进行任务管理和任务监控。由 NodeManager 替代 TaskTracker 进行具体任务的执行，因此 MapReduce2.0 只是一个计算框架。对比 Hadoop1.0 中相关资源的调用全部给 Yarn 框架管理
- Hadoop3.0
  - Hadoop2.0 之后版本就相对稳定，大部分实际生产环境中都使用的是2.0
  - Hadoop3.0 主要增加了一些性能上的优化和支持
    - Java 运行环境升级为1.8，对之前低版本的 Java 不在支持
    - HDFS3.0 支持数据的擦除编码，调高存储空间的使用率
    - 一些默认端口的改变
    - 增加一些 MapReduce 的调优



#### HDFS

- HDFS 全称 Hadoop Distributed File System

- HDFS 文件系统是为 Hadoop 生态圈提供基础存储服务的一个分布式文件系统

- 是一个主-从（master-slave）架构，一个 Hadoop 集群中 HDFS 由一个 NameNode 和多个 DataNode 组成，这两类 Node 分工明确：

  - NameNode（名字节点）
    - HDFS系统中只有一个
    - 是一个中心服务器角色，负责管理构建HDFS的名字空间（namespace）
    - 比如：打开、关闭、重命名文件或目录，管理和检索多个DataNode的实际数据所需的所有元数据
  - DataNode（数据节点）
    - HDFS 集群中一般是一个集群节点有一个 DataNode
    - 负责管理它所在节点上的存储
    - 在 NameNode 的统一调度下进行数据块的创建、删除和复制

- HDFS 当做一个整体对外暴露文件系统的名字空间，用户可以以文件的形式在上面存储数据，从HDFS集群内部把一个文件分解成一个或者多个数据块，存储在一组的 DataNode 节点上

- HDFS 读过程

  - 客户端发送读取文件请求，调用 HDFS API 的 open 方法到 NameNode 节点，通过 NameNode 节点获取到文件的块存储的位置（在哪几个DataNode上面）
  - NameNode 返回所有 block 的位置信息，并将这些信息返回给客户端
  - 客户端拿到 block 的位置信息后调用 HDFS API 的 read 方法并行的读取 block 信息，HDFS 默认 block 存储在三个DataNode节点上，所以每一个 block只需要从一个副本读取即可
  - DataNode 把结果数据返回给客户端

- HDFS 写过程

  - 客户端发送写文件请求，调用 HDFS API 的 create 方法到 NameNode，create 的入参有上传的文件名，文件大小，文件拥有者
  - NameNode 根据上传的文件大小切成多个 block 块，存储复制到多个 DataNode 上，并将存储的信息和位置信息返回给客户端
  - 客户端调用 HDFS 的 write 方法将 block 块写入对应的 DataNode 上，这里要注意的是 HDFS 的块是有副本的，默认是3个副本，并不是由客户端一次写入到3个副本当中，而是有 block 块所在的 DataNode 产生新的线层进行副本的复制
  - 写完后返回给客户端一个信息，然后客户端再将信息反馈给 NameNode

- 适用场景

  - 适用
    - 高容错性环境：数据会自动保存多个副本，副本就算丢失，也能够自动回复
    - 适合批处理：可以进行快速的分布式计算，移动计算
    - 适合大数据量的处理：GB / TB 级别的数据或百万规模以上文件数量
    - 流式文件的访问：一致性写入，多次读取
    - 可搭建在廉价的机器上，通过分布式集群提高性能，通过高容错性和可靠性的模型框架提供恢复机制
  - 不适用
    - 数据访问实时性较高的应用场景，对延时要求在毫秒级的应用，不适合采用 HDFS 进行存储
    - 大量小文件，因为 HDFS 整个文件系统的文件数量受限于 NameNode 的内存大小。如果文件过多，比如100万个文件，每个文件占用一个文件块（大小150字节），则最少需要 300M 的内存
    - 上亿级别的文件数量在现有的服务器上难以支持
    - 多方写入，需要在任意的文件上进行修改的应用场景，HDFS 采用的是追加方式写入数据，是不支持修改操作的

- 常用命令

  - hadoop hdfs shell 命令是在安装目录使用 dfs+具体命令 的语法来操作文件或目录的，具体语法如下：

    ```shell
    ./hdfs -dfs + 具体命令
    ```

  - -mkdir：在hdfs文件系统上创建目录：

    ```shell
    ./hdfs dfs -mkdir
    ```

  - -ls：查看指定目录的信息：

    ```shell
    ./hdfs dfs -ls
    ```

  - -appendToFile：追加一个文件到已经存在的文件末尾

    ```shell
    ./hdfs dfs -appendToFile /usr/local/hadoop-2.10.0/.txt /test/README.txt
    ```
    
  - -cat：显示文件内容
  
    ```shell
    ./hdfs dfs -cat /test/README.txt
    ```
  
  - -tail：显示一个文件的末尾
  
    ```shell
    ./hdfs dfs  -tail  /test/README.txt
    ```
  
  - -text：以字符形式打印一个文件的内容
  
    ```shell
    ./hdfs dfs  -text  /test/README.txt
    ```
  
  - -chgrp 、-chmod、-chown：修改文件权限命令，用法与linux文件系统中一样
  
    ```shell
    ./hdfs dfs  -chmod  666  /README.txt
    ```
  
  - -copyFromLocal：从本地文件系统中拷贝文件到hdfs路径去
  
    ```shell
    ./hdfs dfs  -copyFromLocal  /usr/local/hadoop-2.10.0/NOTICE.txt  /test
    ```
  
  - -copyToLocal：从hdfs文件系统中拷贝到本地文件系统，默认到本地当前路径
  
    ```shell
    ./hdfs dfs -copyToLocal /test/NOTICE.txt
    ```
  
  - -cp 命令：从hdfs的一个路径拷贝到hdfs的另一个路径
  
    ```shell
    ./hdfs dfs  -cp  /test/NOTICE.txt  /test1/NOTICE.txt
    ```
  
  - -mv：在hdfs目录中移动文件到指定目录
  
    ```shell
    ./hdfs dfs  -mv  /test/README.txt  /test2
    ```
  
  - -get：和copyToLocal命令一样，就是从hdfs下载文件到本地目录
  
    ```shell
    ./hdfs dfs -get  /test/NOTICE.txt
    ```
  
  - -getmerge ：合并下载多个文件，比如hdfs的目录 /test/下有多个文件:log.1, log.2,log.3,…
  
    ```shell
    ./hdfs dfs -getmerge /test/log.* ./log.n
    ```
  
  - -rm：删除文件或文件夹
  
    ```shell
    ./hdfs dfs -rm -r /test2
    ```
  
  - -rmdir：删除空目录
  
    ```shell
    ./hdfs dfs  -rmdir   /test1
    ```
  
  - -df：统计文件系统的可用空间信息
  
    ```shell
    ./hdfs dfs  -df  -h  /test
    ```
  
  - -du：统计文件夹的大小信息
  
    ```shell
    ./hdfs dfs  -du  -s  -h /test
    ```
  
  - -count：统计文件目录下的文件节点数量
  
    ```shell
    ./hdfs dfs -count /test
    ```



#### MapReduce

- “分而治之”

- MapReduce 是有 google 在一篇论文当中提出的的一个分布式计算模型，主要用于解决海量数据当中的计算问题

- 把复杂任务分解成小任务进行计算，通过实现 Map( ) 和 Reduce( ) 两个函数实现分布式计算

- Map( )

  - 负责“分”
  - 把一个总的任务拆分成N个“简单的小任务“来处理，这里简单的小任务指定是：
    - 相对总任务计算规模相对变小、或者计算的数据量变小
    - 小任务分配到所需的数据节点上进行计算，小任务计算尽量在本机上计算
    - 这些小任务可以并行计算，互不相干扰和前后的依赖关系

- Reduce( )

  - 负责“合”
  - 把小任务的计算结果进行汇总
  - 可以根据业务需要进行组装后输出汇总数据

- 运行机制

  - 涉及到的知识点定义

    - client：客户端，编写并提交 MapReduce 程序
  - JobTracker：初始化作业，作业任务的分配，与 TaskTracker 进行通信，并管理整个 MapReduce 任务的运行
    
    - TaskTracker：从 JobTracker 接收相关指令，运行、杀死等相关本地节点任务 TASK。汇报节点相关情况给 JobTracker
  - HDFS 文件系统：报存作业（job）的结果数据以及相关配置信息
    
    - resouce manage node：YARN 资源管理器，负责 Hadoop 集群上计算资源的协调
  - node manage node：YARN 节点管理器，负责启动和监控集群中机器上的计算容器（container）
    
    - MRAppMaster：负责协调运行 MapReduce 的作业
    
  - MapReduce1.0 运行过程

    - 作业（job）提交
      - 客户端（client）编写好 MapReduce 程序后并运行，接下来就是要把程序（即任务）提交到 JobTracker 上
      - 向 Jobtracker node 提交申请，获取一个作业ID（get new job id）
      - 计算作业的输入分片大小，将运行作业的相关代码、配置文件、输入分片的计算资源以作业 id 命名复制到 HDFS 文件系统
      - 通过 Jobtracker 提交作业（submit job）
    - 作业初始化
      - 提交完作业后，JobTracker 会对作业进行初始化
      - 初始化的作用是将要执行的 job 放到队列当中，让作业调度器能够调度的到这个作业，以便 JobTracker 能够跟踪 job 的状态和进程

    - 作业任务分配
      - 初始化完毕后，作业调度器会根据程序从HDFS中获取输入分片信息（input split）
      - 为每个分片创建一个 map 任务，这个任务运行在 Tasktracker node 节点上
      - Tasktracker 会定期发送心跳给 Jobtracker，心跳间隔是5秒(这个时间可以参数设定)，通过心跳，JobTracker 可以监控 Tasktracker 是否存活，也可以获取 Tasktracker 处理的状态和问题，同时 Tasktracker 相关的指令也可以通过心跳传递
    - 作业任务的执行
      - 任务分配好后，执行任务
      - 在任务执行时候 JobTracker 可以通过心跳机制监控 Tasktracker 的状态和进度，同时也能计算出整个 job 的状态和进度，而 Tasktracker 也可以本地监控自己的状态和进度
    - 作业任务完成 
      - 当 JobTracker 获得了最后一个完成指定任务的 Tasktracker 操作成功的通知时候，JobTracker 会把整个 job 状态置为成功
      - 当客户端查询 job运行状态时候（注意：这个是异步操作），客户端会查到 job 完成的通知的
    - 问题
      - JobTracker 承担的工作量太大，当随着 job 数量的增加，内存不够，会造成 JobTracker 死掉
      - JobTracker 存在单点故障问题，这个任务资源调度节点一死，Hadoop 就无法正常运行
      - 在 TaskTracker 端，对于资源的分配是通过 task 数目作为参考依据，没有考虑节点的 CPU、内存等占用情况，如果两个复杂任务同时调用到同一个 TaskTracker 端，容易出现节点奔溃

  - MapReduce2.0 运行过程

    - 作业的提交

      - 客户端 client 运行 MapReduce 程序
      - 会向 YARN 资源管理器节点申请一个用于执行 MapReduce 的作业 id
      - Hadoop 会根据作业的输入分片的大小、运行作业的 JAR、配置文件等资源以作业 id 命名的临时共享目录，保存在 HDFS 文件系统上
      - 通过 YARN 资源管理器的 submit 方法提交作业

    - 作业的初始化

      - 提交完作业，YARN 资源管理器就会在节点管理器上分配一个容器（container），用于启动 MRAppMaster 进程。该进程主要负责协调运行 MapReduce 的作业，主要监控作业节点的 Map 和 Reduce 的运行状态。并在启动的时候就向 YARN 资源管理器注册并报告自己的信息
      - MRAppMaster 利用 HDFS 当中任务 id 存储的临时共享目录中的信息对每一个分片创建一个 Map 对象，并通过参数确定 Reduce 的数量，确定最终输出的目录和任务输出的临时工作空间

    - 作业任务的分配

      - 初始化任务完成后，MRAppMaster 会向 YARN 资源管理器申请进行 Map 和 Reduce 任务的资源节点8
      - Map 任务发出资源申请，直到 Map 任务完成超过 5% 时，会为 Reduce 任务申请资源
      - Reduce 任务的分配可以分配到各个 DataNode 节点运行，但是 Map 执行时，会优先考虑符合 Map 任务的本地数据节点，减少跨数据节点进行 Map 操作

    - 作业任务的执行

      - MRAppMaster 提交申请后，资源管理器为其按需分配资源节点，MRAppMaster 就与 YARN 节点管理器通信来启动容器
      - 该任务由主类 YarnChild 的一个 Java 应用程序执行
      - 在运行任务之前，会从 HDFS 的临时共享目录上进行所需资源的本地化
      - 运行 Map 和 Reduce 任务

    - 作业的完成

      当 MRAppMaster 收到最后一个任务已完成的通知，便把作业的状态设置为成功，并告知 YARN 资源管理器

  - MapTask 运行机制

    - Read 阶段
      - 任务节点根据客户端程序代码中编写的待处理的数据
      - 根据任务提交的配置信息来计算需要的 MapTask 的数据
      - MapTask 的第一个阶段就是读的阶段，从待处理的分片数据（inputSplit）当中读出一个个键值对 key / value

    - Map 阶段
      - 通过用户程序编写的 Map( ) 函数处理，把 Read 阶段解析处理的键值对进行处理，再产生一系列新的键值对
      - Collect 阶段在调用完 Map( ) 函数，会调用 outputCollector 对 Map 的结果进行分区处理（partitioner），并写入到内存缓冲区中

    - 溢写阶段
      - 当缓冲区满了，MapTask 会将缓冲区的数据“溢写“到本地磁盘上，生成一个 spillN.out（其中N代表溢写的次数）的文件
      - 利用快速排序算法对缓存区内的数据进行排序，按照分区编号 Partition（先）、分区内的 key（后）进行排序，把数据以分区为单位聚集在一起，同时分区内的 key 是有序的
      - 然后按照分区编号的大小，由小到大依次写入到 spillN.out 临时文件当中。假如用户程序编写了combiner 函数，那么在写入之前还会根据用户的需求对分区数据进行一次聚集处理
      - 将分区数据的元信息写到内存索引数据结构 SpillRecord 中，主要是元数据在临时文件中的位置、压缩前数据大小和压缩后数据大小
      - 如果当前内存索引大小超过 1MB，将会把内存索引写到文件 spillN.out.index 中

    - Combine 阶段
      - 当数据处理完后，MapTask 会将所有临时文件进行合并，合并成一个大文件保存到文件 output/file.out 中，同时生成相应的索引文件output/file.out.index
      - 在进行文件合并过程中，MapTask 以分区为单位进行合并

  - ReduceTask 运行机制

    - Copy 阶段
      - Map 任务完成后会在各个 Map 任务节点上重新生成 key / value 的键值对数据
      - Reduce 任务根据分区从 MapTask 节点上 copy 数据到要执行该分区的 Reduce 计算的节点上
      - 复制数据的过程是先放到内存当中，当内存的数据大小超过一定的阀值时，会溢写到磁盘上

    - Merge 阶段
      - 在进行 COPY 阶段的同时，可以对同一个分区内的数据进行合并，同时对内存当中的数据进行合并、也对磁盘上的数据进行合并

    - Sort 阶段
      - MapReduce 中对于 reduce 函数的输入是按照 KEY 进行排序聚集的一组数据
      - 在调用 reduce 函数之前，需要对所有的数据再进行一次归并排序

    - Reduce 阶段 
      - 在每个节点当中对归并排序好的分区数据进行 reduce 函数的计算，然后将计算结果写到 HDFS 上
      - 这里 reduce 函数是用户编写的代码处理程序

​      