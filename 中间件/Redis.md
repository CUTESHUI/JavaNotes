## Redis

- 目录

  - /usr/local/redis-5.0.9

- 配置

  - 添加配置目录

    1. 在 /usr/local/redis-5.0.9 下

       ```bash
       sudo mkdir  /usr/local/redis-5.0.9/bin
       sudo mkdir  /usr/local/redis-5.0.9/etc
       sudo mkdir  /usr/local/redis-5.0.9/db
       ```

    2. 把  /usr/local/redis-5.0.9 下的 mkreleasehdr.sh，redis-benchmark， redis-check-rdb， redis-cli， redis-server 拷贝到bin 目录

    3. 在 etc 目录下，创建配置文件，如果单机一个就够了配置如下（redis-6379.conf）：

       ```bash
       #修改为守护模式
       daemonize yes
       #设置进程锁文件
       pidfile /usr/local/redis-5.0.5/redis.pid
       #端口
       port 6379
       #客户端超时时间
       timeout 300
       #日志级别
       loglevel debug
       #日志文件位置
       logfile /usr/local/redis-5.0.5/log-redis.log
       #设置数据库的数量，默认数据库为0，可以使用SELECT <dbid>命令在连接上指定数据库id
       databases 16
       ##指定在多长时间内，有多少次更新操作，就将数据同步到数据文件，可以多个条件配合
       #save <seconds> <changes>
       #Redis默认配置文件中提供了三个条件：
       save 900 1
       save 300 10
       save 60 10000
       #指定存储至本地数据库时是否压缩数据，默认为yes，Redis采用LZF压缩，如果为了节省CPU时间，
       #可以关闭该#选项，但会导致数据库文件变的巨大
       rdbcompression yes
       #指定本地数据库文件名
       dbfilename dump.rdb
       #指定本地数据库路径
       dir /usr/local/redis-5.0.5/db/
       #指定是否在每次更新操作后进行日志记录，Redis在默认情况下是异步的把数据写入磁盘，如果不开启，可能
       #会在断电时导致一段时间内的数据丢失。因为 redis本身同步数据文件是按上面save条件来同步的，所以有
       #的数据会在一段时间内只存在于内存中
       appendonly no
       #指定更新日志条件，共有3个可选值：
       #no：表示等操作系统进行数据缓存同步到磁盘（快）
       #always：表示每次更新操作后手动调用fsync()将数据写到磁盘（慢，安全）
       #everysec：表示每秒同步一次（折衷，默认值）
       appendfsync everysec
       ```

    4. 集群环境需要添加多个配置（redis-6370.conf，redis-6371.conf，方便起见只写两个），路径可根据个人喜好进行分配，配置内容较单机多以下配置：

       ```bash
       #开启集群
       cluster-enabled yes
       #集群超时时间
       cluster-node-timeout 5000
       ```

- 启动 redis 服务

  - 方式一 劝退
    1. cd /usr/local/redis-5.0.9
    2. redis-server
  - 方式二
    1. cd /usr/local/redis-5.0.9/src
    2. redis-server
  - 方式三
    1. cd /usr/local/redis-5.0.9/bin
    2. redis-server
  - 自定义、**推荐**
    - 以 /usr/local/redis-5.0.9/etc/redis-6370 为例
      1. cd  /usr/local/redis-5.0.9/etc
      2. redis-server redis-6370.conf
      3. redis-cli -p 6370

- 关闭 redis 服务

  - 方式一
    1. 没进入客户端时 / redis-cli -端口号
    2. ctrl c
  - 方式二、**推荐**
    1. 进入客户端、
    2. shutdown
  - 退出命令行
    - exit

---



#### String 

- keys *：查看所有的key
- exists key：判断当前的key是否存在
- set key value：设置值
- get key：获得值
- move key 1：将key移到1号数据库
- expire key 10：设置key的过期时间，单位是秒
- ttl key：查看当前key的剩余时间
- type key：查看当前key的类型
- append key value：追加字符串，如果当前key不存在，就相当于setkey
- strlen key：获取字符串的长度
- incr key：自增1
- decr key：自减1
- incr key 10：设置步长，指定增量
- decr key 10：设置步长，指定减量
- getrange key 0 3：截取字符串[0,3]
- getrange key 0 -1：获取全部的字符串
- setrange key 1 value：替换指定位置开始的字符串
- setex key 30 value：设置key的值为value，过期时间为30秒
- setnx key value：如果key不存在则创建key，如果存在则创建失败
- mset k1 v1 k2 v2 k3 v3：同时设置多个值
- mget k1 k2 k3：同时获取多个值
- msetnx k1 v1 k4 v4：msetnv是一个原子性的操作，要么一起成功，要么一起失败
- getset key value：如果不存在值，则返回nil，并设置值；如果存在值，获取原来的值，并设置新的值



#### Hash

key-Map集合

- hset key field value：set一个具体的值，值是一个map集合，field-value
- hget key field：获取一个字段值
- hmset key field [key field …]：同时set多个值
- hmget key field [field…]：同时获取多个值
- hgetall key：获取所有的值
- hdel key field [field…]：删除hash指定的field字段，对应的value也会删除
- hlen key：获取hash表的字段数量
- hexists key field：判断key中field字段是否存在
- hkeys key：只获得所有的field
- hvals key：只获得所有的value
- hincrby key field increment：指定增量
- hsetnx key field value：如果存在则设置值，如果存在则不能设置



#### List

- lpush key value：将一个值或者多个值，插入到列表的头部（左）
- rpush key value：将一个值或者多个值，插入到列表的尾部（右）
- lpop key：移除key的左边元素
- rpop key：移除key的右边元素
- llen list：返回列表的长度
- lrange key 0 -1：获取key中所有的值
- lindex key index：通过下标获得key中的某一个值
- lrem key count value：移除key集合中指定count的value值
- ltrim key start stop：通过下标截取指定的长度，并改变key中的值
- rpoplpush source destination：移除列表的最后一个元素，将他添加到新的列表中
- lset key index value：如果存在index下标的值则更新为value，不存在则报错
- exists key：判断这个列表是否存在
- linsert key before|after pivot value：将某个具体的value值插入到列表中某个元素的前面或者后面



#### Set

无序不重复

- sadd key member：添加值
- srem key member：移除key集合中的指定元素
- smembers key：查看key中所有值
- smember key value：判断value值是否在key集合中
- scard key：获取key集合中的内容元素个数
- srandmember key [count]：随机抽取key中count数量的元素
- spop key [count]：随机删除key中count数量的元素
- smove source destination member：将source中指定的值移动到另外一个set集合中
- sdiff key [key…]：差集
- sinter key [key…]：交集
- sunion key [key…]：并集



#### Zset

有序不重复

- zadd key score value：增加值
- zrem key member [member…]：移除有序集合中的指定元素
- zrange key start end：获取指定范围的值
- zcard key：获取有序集合中的个数
- zcount key min max：获取指定区间的成员数量
- zrangebyscore key min max [withscores]：显示全部的用户，从小到大 [附带成绩] -inf +inf
- zrevrangebyscore key key max min [withscores]：显示全部的用户，从大到小 [附带成绩]



#### geospatial

地理位置，底层是Zset

- geoadd key 经度 纬度 member：添加数据
- geopos key member：获得指定城市的经度和纬度
- geodist key member1 member2 [unit]：查看member1到member2的距离[单位]
- georadius key 经度 纬度 半径 单位：以这个经度纬度为中性寻找半径以内的位置
- georadiusbymember key member radius 单位：找出member周围的元素
- geohash key member：将二维的经纬度转换成一维的字符串



#### Hyperloglog

是一种数据结构

占用的内存是固定的， 2^64个不同的元素，也就是12KB

统计基数（不重复）

统计网页UV，set存uid，浪费空间，目的是计数而不是存uid

有误差，0.81%

- pfadd key element：创建一组元素
- pfcount key：统计key元素的基数数量
- pfmerge destkey sourcekey [sourcekey…]：合并两组->destkey



#### Bitmap

是一种数据结构

位存储，bit

操作二进制数来进行记录，只有0和1两个状态

1 byte = 8 bit

记录周一到周日的打卡、是否的记录（登录、未登录；活跃、不活跃...）

- setbit key offset value：设置值
- getbit key offset：获取值
- bitcount key [start end]：统计数量



#### 事务

- Redis 单条命令是保证原子性的，但是事务不保证

- Reids 事务的本质

  - 一组命令的集合
  - 一个事务中所有的命令都会被顺序化，在事务执行的过程中，会按照顺序执行

- Redis 事务的特性

  - 一次性：所有命令一次性全部执行
  - 顺序性：按顺序执行
  - 排他性：执行过程中不允许被干扰

- Redis 没有隔离级别的概念

- 所有的命令在事务中，没有直接被执行，只有在发起执行命令时才执行

- Redis 事务的执行过程

  - 开启事务（ multi ）
  - 命令入队
  - 执行事务（ exec ） | 放弃事务（ discard ）

- 异常

  - 编译型异常（代码有问题，命令有错），事务中所有的命令都不会被执行

  - 运行时异常，如果事务队列中存在语法性，那么执行命令的时候，其他命令式可以正常执行的，错误命令抛出异常



#### Redis 持久化

- Redis 是内存数据库，如果不将内存中的数据库状态保存到磁盘，那么一旦服务器进程退出，服务器中
  的数据库状态也会消失，断电即失

- **RDB** ( Redis DataBase )

  - Redis 默认的就是 RDB，一般情况下不需要修改这个配置

  - 什么是 RDB ？
    - 在指定的时间间隔内将内存中的数据集快照写入磁盘，也就是行话讲的Snapshot快照，它恢复时是将快照文件直接读到内存里
  - 过程
    - Redis会单独创建（fork）一个子进程来进行持久化
    - 先将数据写入到一个临时文件中
    - 待持久化过程都结束了，再用这个临时文件替换上次持久化好的文件
  - 优点
    - 适合大规模的数据恢复，dump.rdb
    - 对数据的完整性要求不高
    - 整个过程中，主进程是不进行任何 IO操作的，这就确保了极高的性能
  - 缺点
    - 需要一定的时间间隔进程操作。如果 redis 意外宕机，这个最后一次修改数据就没有的了
    - fork 进程的时候，会占用一定的内存空间
  - 触发机制
    - save的规则满足的情况下，会自动触发 RDB 规则
    - 执行 flushall 命令，会触发 RDB 规则
    - 退出redis，也会产生 RDB 文件
  - 备份
    - 会自动生成一个 dump.rdb
    - 只需要将rdb文件放在我们redis启动目录就可以，redis启动的时候会自动检查dump.rdb 恢复其中的数据

- **AOF** ( Append Only File )

  - 什么是 AOF ？

    - 快照功能（RDB）并不是非常耐久： 如果 Redis因为某些原因而造成故障停机，那么服务器将丢失最近写入、以及未保存到快照中的那些数据。 从 1.1 版本开始， Redis 增加了一种完全耐久的持久化方式： AOF 持久化。

    - 以日志的形式来记录每个写操作，将Redis执行过的所有指令记录下来（读操作不记录）
    - 只许追加文件但不可以改写文件，redis启动之初会读取该文件重新构建数据
    - 换言之，redis重启的话就根据日志文件的内容将写指令从前到后执行一次以完成数据的恢复工作

  - 优点

    - 每一次修改都同步，文件的完整会更加好
    - 每秒同步一次，可能会丢失一秒的数据
    - 从不同步，效率最高

  - 缺点

    - AOF 默认就是文件的无限追加，文件会越来越大。对于数据文件来说，AOF 远远大于 RDB，修复的速度也比 RDB 慢

      如果 AOF 文件大于 64M，太大了，fork一个新的进程来将我们的文件进行重写

    - AOF 运行效率也要比 RDB 慢

|            | RDB    | AOF          |
| ---------- | ------ | ------------ |
| 启动优先级 | 低     | 高           |
| 体积       | 小     | 大           |
| 恢复速度   | 快     | 慢           |
| 数据安全性 | 丢数据 | 根据策略决定 |

- 如何选择使用哪种持久化方式 ？
  - 如果非常关心数据， 但可以承受数分钟以内的数据丢失， 那可以只使用 RDB 持久化
  - 有很多用户都只使用 AOF 持久化， 但并不推荐这种方式： 因为定时生成 RDB 快照（snapshot）非常便于进行数据库备份， 并且 RDB 恢复数据集的速度也要比 AOF 恢复的速度要快



#### Redis 发布与订阅



#### Redis 主从复制

- 将一台Redis服务器的数据，复制到其他的Redis服务器

- 前者称为主节点（Master/Leader），后者称为从节点（Slave/Follower），数据的复制是单向的

- 只能由主节点复制到从节点（主节点以写为主、从节点以读为主）

- 复制原理

  - slave 成功连接到 master 后，会发送一个 sync 命令
  - master 接到命令后，启动后台的存盘进程，同时收集所有用于修改数据集的命令，在后台进程执行完成后，master 将传送整个数据文件到 slave，完成同步 / 复制
  - 全量复制：slave 在收到数据文件后，将其存盘加载到内存中
  - 增量复制：master 继续将收集到的新的修改命令，依次传给 slave

- 作用

  - 数据冗余
    - 主从复制实现了数据的热备份，是持久化之外的一种数据冗余的方式
  - 故障恢复
    - 当主节点故障时，从节点可以暂时替代主节点提供服务，是一种服务冗余的方式
  - 负载均衡
    - 在主从复制的基础上，配合读写分离，由主节点进行写操作，从节点进行读操作，分担服务器的负载
    - 尤其是在读多写少的场景下，通过多个从节点分担负载，提高并发量
  - 高可用基石
    - 主从复制还是哨兵和集群能够实施的基础

- 为什么使用集群 ？

  - 单台服务器难以负载大量的请求
  - 单台服务器故障率高，系统崩坏概率大
  - 单台服务器内存容量有限

- 配置

  - 查看当前库的信息：info replication

  - 既然需要启动多个服务，就需要多个配置文件。每个配置文件对应修改以下信息：
    - 端口号
    - pid 文件名
    - log 文件名
    - rdb 文件名
  - 一主二从配置
    - 默认情况下，每台Redis服务器都是主节点。我们一般情况下**只用配置从机**就好了
    - 使用 **SLAVEOF host port** 就可以为从机配置主机

- 使用规则

  - 从机只能读，不能写，主机可读可写但是多用于写
  - 当主机断电宕机后，默认情况下从机的角色不会发生变化 ，集群中只是失去了写操作，当主机恢复以后，又会连接上从机恢复原状
  - 当从机断电宕机后，若不是使用配置文件配置的从机，再次启动后作为主机是无法获取之前主机的数据的，若此时重新配置称为从机，又可以获取到主机的所有数据
  - 默认情况下，主机故障后，不会出现新的主机，有两种方式可以产生新的主机：
    - 从机手动执行命令 slaveof no one，这样执行以后从机会独立出来成为一个主机 
    - 使用哨兵模式（自动选举）
  - 如果主机断开了连接，我们可以使用 SLAVEOF no one 让自己变成主机！其他的节点就可以手动连接到最新的主节点（手动），如果这个时候老大修复了，那就重新连接



#### 哨兵模式（自动选取老大）

- 当主服务器宕机后，需要手动把一台从服务器切换为主服务器，这就需要人工干预，费事费力，还会造成一段时间内服务不可用。这不是一种推荐的方式，更多时候，我们优先考虑 哨兵模式

- 哨兵的作用

  - 通过发送命令，让 Redis 服务器返回监控其运行状态，包括主服务器和从服务器。
  - 当哨兵监测到 master宕机，会自动将 slave 切换成 master，然后通过发布订阅模式通知其他的从服务器，修改配置文件，让它们切换主机

- 多哨兵

  - 一个哨兵进程对Redis服务器进行监控可能会出现问题，可以使用多个哨兵进行监控，各个哨兵之间还会进行监控，这样就形成了多哨兵模式
  - 主观下线
    - 假设主服务器宕机，哨兵1先检测到这个结果，系统并不会马上进行 failover 过程（故障转移）
    - 仅仅是哨兵1主观的认为主服务器不可用
  - 客观下线
    - 当除了哨兵1以外，还有哨兵也检测到主服务器不可用，并且数量达到一定值时，那么哨兵之间就会进行一次投票，投票的结果由一个哨兵发起，进行 failover 操作
    - 切换成功后，就会通过发布订阅模式，让各个哨兵把自己监控的从服务器实现切换主机

- 优点

  - 哨兵集群，基于主从复制模式，所有主从复制的优点

  - 主从可以切换，故障可以转移，系统的可用性更好

  - 哨兵模式是主从模式的升级，手动到自动，更加健壮

- 缺点 

  - Redis 不好在线扩容，集群容量一旦达到上限，在线扩容就十分麻烦

  - 实现哨兵模式的配置其实是很麻烦的，里面有很多配置项

- 配置哨兵配置文件 sentinel.conf
  - \# sentinel monitor 被监控的名称 host port 1 
  - sentinel monitor myredis 127.0.0.1 6379 1
  - 后面的这个数字1，代表主机挂了，slave投票看让谁接替成为主机，票数最多的，就会成为主机！
- 启动哨兵
  - redis-sentinel xxx/sentinel.conf

- 哨兵模式完整配置

```bash
# Example sentinel.conf
 
# 哨兵sentinel实例运行的端口 默认26379
port 26379
 
# 哨兵sentinel的工作目录
dir /tmp
 
# 哨兵sentinel监控的redis主节点的 ip port 
# master-name  可以自己命名的主节点名字 只能由字母A-z、数字0-9 、这三个字符".-_"组成。
# quorum 当这些quorum个数sentinel哨兵认为master主节点失联 那么这时 客观上认为主节点失联了
# sentinel monitor <master-name> <ip> <redis-port> <quorum>
sentinel monitor mymaster 127.0.0.1 6379 1
 
# 当在Redis实例中开启了requirepass foobared 授权密码 这样所有连接Redis实例的客户端都要提供密码
# 设置哨兵sentinel 连接主从的密码 注意必须为主从设置一样的验证密码
# sentinel auth-pass <master-name> <password>
sentinel auth-pass mymaster MySUPER--secret-0123passw0rd
 
 
# 指定多少毫秒之后 主节点没有应答哨兵sentinel 此时 哨兵主观上认为主节点下线 默认30秒
# sentinel down-after-milliseconds <master-name> <milliseconds>
sentinel down-after-milliseconds mymaster 30000
 
# 这个配置项指定了在发生failover主备切换时最多可以有多少个slave同时对新的master进行 同步，
# 这个数字越小，完成failover所需的时间就越长，
# 但是如果这个数字越大，就意味着越 多的slave因为replication而不可用。
# 可以通过将这个值设为 1 来保证每次只有一个slave 处于不能处理命令请求的状态。
# sentinel parallel-syncs <master-name> <numslaves>
sentinel parallel-syncs mymaster 1

# 故障转移的超时时间 failover-timeout 可以用在以下这些方面： 
#1. 同一个sentinel对同一个master两次failover之间的间隔时间。
#2. 当一个slave从一个错误的master那里同步数据开始计算时间。直到slave被纠正为向正确的master那里同步数据时。
#3.当想要取消一个正在进行的failover所需要的时间。  
#4.当进行failover时，配置所有slaves指向新的master所需的最大时间。不过，即使过了这个超时，slaves依然会被正确配置为指向master，但是就不按parallel-syncs所配置的规则来了
# 默认三分钟
# sentinel failover-timeout <master-name> <milliseconds>
sentinel failover-timeout mymaster 180000
 
# SCRIPTS EXECUTION
 
#配置当某一事件发生时所需要执行的脚本，可以通过脚本来通知管理员，例如当系统运行不正常时发邮件通知相关人员。
#对于脚本的运行结果有以下规则：
#若脚本执行后返回1，那么该脚本稍后将会被再次执行，重复次数目前默认为10
#若脚本执行后返回2，或者比2更高的一个返回值，脚本将不会重复执行。
#如果脚本在执行过程中由于收到系统中断信号被终止了，则同返回值为1时的行为相同。
#一个脚本的最大执行时间为60s，如果超过这个时间，脚本将会被一个SIGKILL信号终止，之后重新执行。
 
#通知型脚本:当sentinel有任何警告级别的事件发生时（比如说redis实例的主观失效和客观失效等等），将会去调用这个脚本，
#这时这个脚本应该通过邮件，SMS等方式去通知系统管理员关于系统不正常运行的信息。调用该脚本时，将传给脚本两个参数，
#一个是事件的类型，
#一个是事件的描述。
#如果sentinel.conf配置文件中配置了这个脚本路径，那么必须保证这个脚本存在于这个路径，并且是可执行的，否则sentinel无法正常启动成功。
#通知脚本
# sentinel notification-script <master-name> <script-path>
  sentinel notification-script mymaster /var/redis/notify.sh
 
# 客户端重新配置主节点参数脚本
# 当一个master由于failover而发生改变时，这个脚本将会被调用，通知相关的客户端关于master地址已经发生改变的信息。
# 以下参数将会在调用脚本时传给脚本:
# <master-name> <role> <state> <from-ip> <from-port> <to-ip> <to-port>
# 目前<state>总是“failover”,
# <role>是“leader”或者“observer”中的一个。 
# 参数 from-ip, from-port, to-ip, to-port是用来和旧的master和新的master(即旧的slave)通信的
# 这个脚本应该是通用的，能被多次调用，不是针对性的。
# sentinel client-reconfig-script <master-name> <script-path>
sentinel client-reconfig-script mymaster /var/redis/reconfig.sh
```



#### 服务的高可用问题

- Redis缓存的使用，极大的提升了应用程序的性能和效率，特别是数据查询方面，但也有问题：
  - 最要害的问题，就是数据的一致性问题（事务在运行时不能保证原子性），从严格意义上讲，这个问题无解
  - 如果对数据的一致性要求很高，那么就不能使用缓存
  - 另外的一些典型问题：缓存穿透、缓存雪崩、缓存击穿



#### 缓存穿透 

缓存数据库中都没有

查不到

- 概念
  - 用户想要查询一个数据，发现 Redis内存数据库没有，也就是缓存没有命中，
  - 于是向持久层数据库查询，发现也没有，
  - 本次查询失败
- 危害
  - 当用户很多的时候，缓存都没有命中（秒杀！），于是都去请求了持久层数据库，这会给持久层数据库造成很大的压力
- 解决方案
  - 布隆过滤器
    - 是一种数据结构，对所有可能查询的参数以 Hash 的形式存储，以便快速确定是否存在这个值
    - 在**控制层**先进行拦截校验，校验不通过直接打回，减轻了存储系统的压力
  - 缓存空对象
    - 一次请求若在缓存和数据库中都没找到，就在缓存中方一个空对象用于处理后续这个请求
    - 如果空值能够被缓存起来，这就意味着缓存需要更多的空间存储更多的键，因为这当中可能会有很多的空值的键
    - 即使对空值设置了过期时间，还是会存在缓存层和存储层的数据会有一段时间窗口的不一致，这对于需要保持一致性的业务会有影响

#### 缓存击穿 

缓存过期，热点 key

查太多

- 概念

  - 一个非常热的 key，扛着高并发，在过期的瞬间，击穿缓存，直接访问数据库

- 危害

  - 造成瞬时持久层数据库请求量大、压力骤增

- 解决方案

  - 设置热点数据永不过期
    - 这样就不会出现热点数据过期的情况，但是当 Redis内存空间满的时候也会清理部分数据
    - 此方案会占用空间，一旦热点数据多了起来，就会占用部分空间

  - 加互斥锁（分布式锁）
    - 在访问 key 之前，采用 SETNX（set if not exists）来设置另一个短期 key 来锁住当前 key 的访问
    - 访问结束再删除该短期 key
    - 保证同时刻只有一个线程访问，这样对锁的要求就十分高



#### 缓存雪崩

- 概念

  - 大量的 key 设置了相同的过期时间，导致在缓存在同一时刻全部失效，同时有大量的请求

- 危害

  - 造成瞬时持久层数据库请求量大、压力骤

- 解决方案

  - redis高可用
    - 这个思想的含义是，既然 Redis有可能挂掉，那我多增设几台 Redis
    - 这样一台挂掉之后其他的还可以继续工作，其实就是搭建的集群
    - 异地多活

  - 限流降级
    - 在缓存失效后，通过加锁或者队列来控制读数据库写缓存的线程数量
    - 比如对某个 key 只允许一个线程查询数据和写缓存，其他线程等待

  - 数据预热
    - 在正式部署之前，我先把可能的数据先预先访问一遍，这样部分可能大量访问的数据就会加载到缓存中
    - 在即将发生大并发访问前手动触发加载缓存不同的 key，设置不同的过期时间，让缓存失效的时间点尽量均匀



