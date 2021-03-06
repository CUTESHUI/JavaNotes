## Oracle优化

#### 性能优化场景

- 从未达到性能要求的数据库优化
- 这个数据库性能应对当前业务绰绰有余的情况进行性能优化
  - 此类故障一般是出现在压力测试或者业务刚刚上线时，这时候可能需要通过修改应用框架或者大规模的优化SQL语句进行改进
- 优化的耗时最长，难度也最大
- 量变引起质变的数据库优化
  - 该类故障通常是数据库进入生产环境，随着业务的并发量、数据量的增加引起的
  - 优化难度一般，但是代价比较高，一般通过增加硬件性能或存储来解决该类性能问题
- 突发性数据库性能故障优化
  - 该类故障优化难度最低，但是时间是最急迫的，需要数据库管理员能够快速定位问题，提出解决方案



#### 优化时间点

- 事前优化
  - 项目上线前的优化
  - 在事前优化过程中，系统设计、开发、产品优化、平台优化的全过程都很重要
  - 对系统的平稳运行有着根本性的影响，而且优化代价也最低
  - 在这个阶段进行优化非常考验开发者的技术能力和开发经验
- 事后优化
  - 业务系统上线后的优化
  - 由于事后优化受到宕机时间、事态的严重性，经常是被迫进行优化，时间会比较紧迫
  - 往往只是紧急的应对了性能下降的问题，大部分情况下是治标不治本的



#### 优化目标

- Oracle 性能优化并没有统一的执行标准或者参考步骤，但是我们进行数据库性能优化时，有一些大概的优化目标，比如：
  - 增大数据库吞吐量
    - 通过数据库优化增加每秒执行的事务数
  - 高效利用服务器硬件资源
    - 开启并行充分利用 CPU 和存储 I/O 资源加快执行速度



#### 优化基本方法

- 常见的方法：建立数据库性能基准线、寻找关键变化、定位最主要的影响因素、查看服务器硬件和系统的使用情况

- 数据库性能基准线

  - 指的是数据库在正常运行时，服务器的各项指标所处的位置，主要包括主机资源和数据库资源的消耗情况，所以数据库性能的优化都要建立在基准线之上

  - 但是在现实生产环境中，由于业务周期在不同阶段，应该采取不同的基准线。比如：正常活动水平、较低活动水平、一天当中最忙时间、夜间处理批任务、商业周期的每月或每季度结束等，在不同的时间段，主机和数据库的资源使用的情况是不一样的

  - 一般来说，数据库性能基线又分技术基线和感官基线两种：

    1. 技术基线
       - 主要指的是数字指标
       - 通常由操作系统资源（CPU、内存、I/O、 网络等）、并发性资源（系统解析数、事务数、归档量等）、TOP SESSION、TOP EVENT、TOP SQL 组成
       - 简单来说，操作系统资源情况加 AWR / STATSPACK 报告就可以构成一份相对完整的技术基线

    2. 感官基线
       - 主要指的是人为感觉
       - 比如：在前端单击业务按钮正常的情况下应该在多少时间内返回结果，客户在使用系统时反馈某一个业务模块是否流畅

- 寻找关键变化

  - 数据库的性能下降往往是由变化引起的
  - 比如：在进行对某张千万级的表进行大批量的数据插入时，有可能导致 Oracle 优化器识别错误
  - 可以通过之前观察和使用过程中设定好的数据库基准线，通过基准线找到关键变化
  - 可以考虑以下几点
    - 数据库系统性能是从什么时候开始变坏的
    - 是不是所有业务模块都不行了，还是局部模块不行了
    - 是不是在数据库变化的这个时间点，做了某些特殊的操作，比如：代码更新、数据统计、脚本执行、数据迁移、数据备份或硬件的替换等等
    - 是不是有的业务模块上线，是否有新的优惠活动，导致数据库访问量大大提升
    - 查找直接的维护记录，是否有出现过该类情况，是在什么时间节点出现的
    - 数据库的性能是刚刚突然就变化了，还是一个逐步的过程，从几个小时或者几天前逐渐变化的
    - 数据库的性能下降，是否会影响正常的业务，还是对某些特定的业务有影响
    - 硬件是否出现了故障?比如存储的电池故障导致 CACHE 失效



#### 操作系统资源对 Oracle 性能的重要性

- CPU 资源
  - CPU 资源是否紧张可通过检查 CPU 的利用率及等待运行的进程数量来了解
  - CPU 运算速度主要受主频（Processor Clock Speed）高低和缓存（Cache Memory）大小影响
  - 比如，在 OLTP 业务系统中，由于每个 CPU 在特定时间内只允许一个进程运行，所以这里的 CPU 个数决定着事务的运行效率
  - 在 OLAP 系统中，由于并发需求不高，反而每次运算的时间较长，所以这个时候 CPU 的主频决定了事物的运行效率
  - 在很多的管理员中，认为 CPU 资源使用率越高说明 Oracle 的性能越差，但是这个不是批评性能优劣的唯一指标，因为合理并最大限度地利用系统资源是数据库优化的目标之一

- 内存资源
  - 我们经常讲的内存资源，主要包括物理内存和虚拟内存
  - 当进程需要新的内存资源，而实际内存资源不足的时候，系统会把部分活动性弱的内存数据写人虚拟内存，在进程需要的时候再次从虚拟内存中读出
  - 为提高 Oracle 运行性能，则要求没有进程被交换到虚拟内存中
  - 确实这样子把数据全部放在内存中，可以提高数据库性能，但是大内存并不能保证数据库系统的性能一定很优良
  - 虽然数据块的 BUFFER CACHE 命中率为100%，但并不能保证 SQL 的运行效率高。提高 SQL 运行效率的优化思路是降低 SQL 的逻辑读取数据块数量
  - 内存一直是数据库性能优化时的重点优化对象，内存分配没有多少之分，够用就行

- I / O 资源
  - I / O 资源是系统资源中最慢的活动，存储 I / O 能力的高低通常用吞吐量、IOPS（ I / O 每秒进行操作的次数）、磁盘响应时间等指标来区分
  - 决定存储性能的主要因素在于存储阵列的算法、Cache 命中率，以及磁盘数、存储 I / O 总线的宽度。CACHE 的命中率取决于 CACHE 的算法、Cachesize 的大小以及数据的访问规则
  - 一般 CACHE 的读命中率越高，支持的 IOPS 也就越高，因此，每个物理硬盘能处理的 IOPS 是有限制的
  - 存储的 I / O 响应缓慢往往是由以下因素引起的：
    - 当应用所发起的 IOPS 超过物理磁盘的理论 IOPS 时，系统的 I / O 能力将急剧下降
    - 热点盘。即数据访问集中在几张盘上
    - 系统大量交换导致本地盘繁忙
    - 不合理的 RAID 配置模式
    - 存储性能瓶颈。主要表现为控制器不足或者 I / O 通道堵塞
    - 磁盘转速较低
    - 硬件故障，比如存储硬件的 CACHE 算法故障，导致命中率不够，最后导致 I / O 响应时间长



#### 常见性能故障

- 开发阶段

  - 这个阶段，遇到的数据库性能问题相对较少
  - 问题：
    - 开发过程中因某些程序段执行速度慢而影响整体开发进程
    - 大部分是数据库性能不理想，导致开发结束后，系统测试未通过，以致延迟投人生产

- 业务上线生产阶段

  - 这个阶段，数据库的数据量会随着业务逐渐增大，性能问题会逐渐的爆发出来
  - 问题：
    - 对系统有致命影响的 SQL 语句条数过多，导致数据库服务器资源耗尽，甚至宕机
    - 单个 SQL 在执行过程中所读取的物理和逻辑 I / O 太多，导致业务响应速度过慢
    - 存在大量无效的重复执行的 SQL 语句，导致业务响应速度变慢
    - 执行批处理作业时，在限定时间内无法结束，严重影响日常业务运行
    - 随着业务量的逐渐提升，之前的系统硬件存在性能瓶颈，要通过增加硬件设备来提升性能

- 业务稳定数据处理阶段

  - 这个阶段，业务量逐渐稳定，主要是后期对数据的迁移和数据的分析处理

  - 问题：
    - 大规模的迁移数据，导致数据迁移时间过长、性能影响面过大
    - 数据迁移之后执行计划变更，导致业务响应缓慢
    - 进行大数据量的数据分析和报表的统计，导致计算时间过长，或者读取 I / O 太多，特别是进行多维度、多表、大数据量的关联统计和数据分析

- 总结
  - 系统资源瓶颈
    - 比如：CPU、内存、I / O、网络资源， 当这些资源达到瓶颈时，数据库性能容易出现问题。所以了解操作系统各项资源的响应时间和发能力是性能优化很重要的技术储备
  - 程序设计
    - 在很多情况下，一个好的程序设计，一个好的框架决定了数据库性能的好坏
  - 并发性资源冲突
    - 比如：会话的连接方式，会话连接到数据库主要分长连接和短连接两种，在高并发的环境下，频繁的短连接会话可能会成为影响数据库性能的主要因素
  - 锁的争用
    - 数据库的锁争用是不可避免的，但是如果大量进程之间相互锁，进而导致了死锁，那就会成为影响性能的主要原因之一
  - 大量的不同SQL的解析，往往会引起共享池不够用，进而导致性能下降
  - ROW CACHE 的争用
    - 大量的 DDL 语句或使用 CACHE 偏小的 SEQUENCE 是引起 ROW CACHE 的大小不够和争用的主要原因
  - BUFFER CACHE 的争用
    - 当应用集中读取 BUFER CACHE 中的某个数据块，或者大量的 SQL 执行计划有问题时，往往会引起 BUFFER CACHE 的争用
  - 数据块争用
    - 当大量的会话同时更改同一个数据块中的内容时，容易导致数据块争用。最典型的情况是索引的叶节点块争用
  - REDO LOG 和 REDO BUFFER 的争用
    - 当系统产生大量的日志或者频繁地执行 COMMIT，容易导致 REDO BUFFER 的争用
  - UNDO 表空间的争用
    - 大量的并发事务导致的 UNDO 段头争用
  - 不适当的并行操作
    - 从理论上来讲，开启并行操作能加快数据库操作，但不恰当的并行操作不仅容易增加协调成本，而且容易消耗过多的操作系统资源，进而导致其他进程获得不了操作系统资源
  - 数据库参数设置不合理
    - 比如 SGA 参数设置不合理是导致数据库性能问题的常见原因之一
  - SQL语句的运行效率低下



#### 减少 I / O 读取提高 Oracle 运行效率

- 减少 I / O 读取
  - 在选择列上创建索引
    - 如果从索引上读取数据所消耗的 I / O 数比从表中读取的少，则可以考虑使用此方法
  - 重建索引
    - 主要针对碎片较多、索引层数较高、CLUSTER 因子较大的索引
    - 重建时建议将索引的 PCTFREE 参数设置为 0，这样一个索引块中就可以保存更多的数据行信息
  - 删除无效的索引
    - 过多的无效索引不仅容易引起 CBO 优化器选择错误，从而导致执行计划变差，而且会降低 DML操作效率
  - 重组表
    - 重组表针对碎片严重、行迁移和行链接较严重的[表](https://www.oraclejsq.com/article/010100139.html)。表重组之后，Oracle 进行一次 I / O 操作可以读取更多的数据
  - 将大表变成小表
    - 如迁移历史数据并重组表格，由于 SQL 的 WHERE 条件过滤性不佳，导致执行计划只能选择全表扫描
    - 随着时间的推移，当表中的数据越来越多时，全表扫描的代价可能越来越高，从而导致数据库运行越来越慢
  - 以内存换取I/O
    - 如果内存足够，可以对小的“热”表（使用频繁的）执行 KEEP BUFFER CACHE
  - 压缩表
    - 合理的使用压缩技术，可以大幅度缩减大量数据处理时的 I / O 量
  - 对于值不断增加的表（如日志表），因为这些表很少更新，最好设置一个非常低的PCTFREE (甚至可以为0)，从而节省存储空间
  - PCTFREE：指的是为一个数据块保留一个空间的百分比，表示数据块在低于这个百分比时，默认是10%，就不可以被 insert 了，只能被 update。因此可以节省存储空间。

- 避免 I / O 争用
  - 分散热块中的数据
    - 最常见的优化手段是使用 HASH 分区、HASH CLUSTER TABLE、反转键索引，加大表 PCTFREE 参数
    - 其中分区的方法可以把不同的分区映射到不同的磁盘上用来平衡 I / O 操作，从而改善系统性能
    - 反转键索引就是将索引键的值反转过来进行索引，索引的结构没什么变化，只是索引值得存储方式有点不一样，但是在特定的时候，可以把原本密集的值散列开，可以大大提高查询效率
    - 加大表 PCTFREE 参数。由于经常要做 update 操作，可以把 PCTFREE 相对提高，可以调到30%，为每个块预留更多的空间，以便更快的 update，而不用作迁移块的操作
  - 使用较小 BLOCK SIZE 的数据块，如4KB
    - 这个就很好理解，数据块是数据库 I / O 的最小单位