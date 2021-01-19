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

#### 配置文件

- conf 详解

```bash
# 当配置中需要配置内存大小时，可以使用 1k, 5GB, 4M 等类似的格式，其转换方式如下(不区分大小写)
#
# 1k => 1000 bytes
# 1kb => 1024 bytes
# 1m => 1000000 bytes
# 1mb => 1024*1024 bytes
# 1g => 1000000000 bytes
# 1gb => 1024*1024*1024 bytes
#
# 内存配置大小写是一样的.比如 1gb 1Gb 1GB 1gB
 
# daemonize no 默认情况下，redis不是在后台运行的，如果需要在后台运行，把该项的值更改为yes
daemonize yes
 
# 当redis在后台运行的时候，Redis默认会把pid文件放在/var/run/redis.pid，你可以配置到其他地址。
# 当运行多个redis服务时，需要指定不同的pid文件和端口
pidfile /var/run/redis.pid
 
# 指定redis运行的端口，默认是6379
port 6379
 
# 指定redis只接收来自于该IP地址的请求，如果不进行设置，那么将处理所有请求，
# 在生产环境中最好设置该项
# bind 127.0.0.1
 
# Specify the path for the unix socket that will be used to listen for
# incoming connections. There is no default, so Redis will not listen
# on a unix socket when not specified.
#
# unixsocket /tmp/redis.sock
# unixsocketperm 755
 
# 设置客户端连接时的超时时间，单位为秒。当客户端在这段时间内没有发出任何指令，那么关闭该连接
# 0是关闭此设置
timeout 0
 
# 指定日志记录级别
# Redis总共支持四个级别：debug、verbose、notice、warning，默认为verbose
# debug 记录很多信息，用于开发和测试
# varbose 有用的信息，不像debug会记录那么多
# notice 普通的verbose，常用于生产环境
# warning 只有非常重要或者严重的信息会记录到日志
loglevel debug
 
# 配置log文件地址
# 默认值为stdout，标准输出，若后台模式会输出到/dev/null
#logfile stdout
logfile /var/log/redis/redis.log
 
# To enable logging to the system logger, just set 'syslog-enabled' to yes,
# and optionally update the other syslog parameters to suit your needs.
# syslog-enabled no
 
# Specify the syslog identity.
# syslog-ident redis
 
# Specify the syslog facility.  Must be USER or between LOCAL0-LOCAL7.
# syslog-facility local0
 
# 可用数据库数
# 默认值为16，默认数据库为0，数据库范围在0-（database-1）之间
databases 16
 
################################ 快照  #################################
#
# 保存数据到磁盘，格式如下:
#
#   save <seconds> <changes>
#
#   指出在多长时间内，有多少次更新操作，就将数据同步到数据文件rdb。
#   相当于条件触发抓取快照，这个可以多个条件配合
#    
#   比如默认配置文件中的设置，就设置了三个条件
#
#   save 900 1  900秒内至少有1个key被改变
#   save 300 10  300秒内至少有300个key被改变
#   save 60 10000  60秒内至少有10000个key被改变
 
save 900 1
save 300 10
save 60 10000
 
# 存储至本地数据库时（持久化到rdb文件）是否压缩数据，默认为yes
rdbcompression yes
 
 
# 本地持久化数据库文件名，默认值为dump.rdb
dbfilename dump.rdb
 
# 工作目录
#
# 数据库镜像备份的文件放置的路径。
# 这里的路径跟文件名要分开配置是因为redis在进行备份时，先会将当前数据库的状态写入到一个临时文件中，等备份完成时，
# 再把该该临时文件替换为上面所指定的文件，而这里的临时文件和上面所配置的备份文件都会放在这个指定的路径当中。
# 
# AOF文件也会存放在这个目录下面
# 
# 注意这里必须制定一个目录而不是文件
dir ./
 
################################# 复制 #################################
# 主从复制. 设置该数据库为其他数据库的从数据库. 
# 设置当本机为slav服务时，设置master服务的IP地址及端口，在Redis启动时，它会自动从master进行数据同步
#
# slaveof <masterip> <masterport>
 
# 当master服务设置了密码保护时(用requirepass制定的密码)
# slav服务连接master的密码
# 
# masterauth <master-password>
 
 
# 当从库同主机失去连接或者复制正在进行，从机库有两种运行方式：
#
# 1) 如果slave-serve-stale-data设置为yes(默认设置)，从库会继续相应客户端的请求
# 
# 2) 如果slave-serve-stale-data是指为no，出去INFO和SLAVOF命令之外的任何请求都会返回一个
#    错误"SYNC with master in progress"
#
slave-serve-stale-data yes
 
# 从库会按照一个时间间隔向主库发送PINGs.可以通过repl-ping-slave-period设置这个时间间隔，默认是10秒
#
# repl-ping-slave-period 10
 
# repl-timeout 设置主库批量数据传输时间或者ping回复时间间隔，默认值是60秒
# 一定要确保repl-timeout大于repl-ping-slave-period
# repl-timeout 60

################################## 安全 ###################################
# 设置客户端连接后进行任何其他指定前需要使用的密码。
# 警告：因为redis速度相当快，所以在一台比较好的服务器下，一个外部的用户可以在一秒钟进行150K次的密码尝试，这意味着你需要指定非常非常强大的密码来防止暴力破解
#
# requirepass foobared
 
# 命令重命名.
#
# 在一个共享环境下可以重命名相对危险的命令。比如把CONFIG重名为一个不容易猜测的字符。
#
# 举例:
#
# rename-command CONFIG b840fc02d524045429941cc15f59e41cb7be6c52
#
# 如果想删除一个命令，直接把它重命名为一个空字符""即可，如下：
#
# rename-command CONFIG ""

################################### 约束 ####################################
# 设置同一时间最大客户端连接数，默认无限制，Redis可以同时打开的客户端连接数为Redis进程可以打开的最大文件描述符数，
# 如果设置 maxclients 0，表示不作限制。
# 当客户端连接数到达限制时，Redis会关闭新的连接并向客户端返回max number of clients reached错误信息
#
# maxclients 128
 
# 指定Redis最大内存限制，Redis在启动时会把数据加载到内存中，达到最大内存后，Redis会先尝试清除已到期或即将到期的Key
# Redis同时也会移除空的list对象
#
# 当此方法处理后，仍然到达最大内存设置，将无法再进行写入操作，但仍然可以进行读取操作
# 
# 注意：Redis新的vm机制，会把Key存放内存，Value会存放在swap区
#
# maxmemory的设置比较适合于把redis当作于类似memcached的缓存来使用，而不适合当做一个真实的DB。
# 当把Redis当做一个真实的数据库使用的时候，内存使用将是一个很大的开销
# maxmemory <bytes>
 
# 当内存达到最大值的时候Redis会选择删除哪些数据？有五种方式可供选择
# 
# volatile-lru -> 利用LRU算法移除设置过过期时间的key (LRU:最近使用 Least Recently Used )
# allkeys-lru -> 利用LRU算法移除任何key
# volatile-random -> 移除设置过过期时间的随机key
# allkeys->random -> remove a random key, any key
# volatile-ttl -> 移除即将过期的key(minor TTL)
# noeviction -> 不移除任何可以，只是返回一个写错误
# 
# 注意：对于上面的策略，如果没有合适的key可以移除，当写的时候Redis会返回一个错误
#
#       写命令包括: set setnx setex append
#       incr decr rpush lpush rpushx lpushx linsert lset rpoplpush sadd
#       sinter sinterstore sunion sunionstore sdiff sdiffstore zadd zincrby
#       zunionstore zinterstore hset hsetnx hmset hincrby incrby decrby
#       getset mset msetnx exec sort
#
# 默认是:
#
# maxmemory-policy volatile-lru
 
# LRU 和 minimal TTL 算法都不是精准的算法，但是相对精确的算法(为了节省内存)，随意你可以选择样本大小进行检测。
# Redis默认的灰选择3个样本进行检测，你可以通过maxmemory-samples进行设置
#
# maxmemory-samples 3

############################## AOF ###############################
# 默认情况下，redis会在后台异步的把数据库镜像备份到磁盘，但是该备份是非常耗时的，而且备份也不能很频繁，如果发生诸如拉闸限电、拔插头等状况，那么将造成比较大范围的数据丢失。
# 所以redis提供了另外一种更加高效的数据库备份及灾难恢复方式。
# 开启append only模式之后，redis会把所接收到的每一次写操作请求都追加到appendonly.aof文件中，当redis重新启动时，会从该文件恢复出之前的状态。
# 但是这样会造成appendonly.aof文件过大，所以redis还支持了BGREWRITEAOF指令，对appendonly.aof 进行重新整理。
# 你可以同时开启asynchronous dumps 和 AOF
 
appendonly no
 
# AOF文件名称 (默认: "appendonly.aof")
# appendfilename appendonly.aof
 
# Redis支持三种同步AOF文件的策略:
#
# no: 不进行同步，系统去操作 . Faster.
# always: always表示每次有写操作都进行同步. Slow, Safest.
# everysec: 表示对写操作进行累积，每秒同步一次. Compromise.
#
# 默认是"everysec"，按照速度和安全折中这是最好的。
# 如果想让Redis能更高效的运行，你也可以设置为"no"，让操作系统决定什么时候去执行
# 或者相反想让数据更安全你也可以设置为"always"
#
# 如果不确定就用 "everysec".
 
# appendfsync always
appendfsync everysec
# appendfsync no
 
# AOF策略设置为always或者everysec时，后台处理进程(后台保存或者AOF日志重写)会执行大量的I/O操作
# 在某些Linux配置中会阻止过长的fsync()请求。注意现在没有任何修复，即使fsync在另外一个线程进行处理
#
# 为了减缓这个问题，可以设置下面这个参数no-appendfsync-on-rewrite
#
# This means that while another child is saving the durability of Redis is
# the same as "appendfsync none", that in pratical terms means that it is
# possible to lost up to 30 seconds of log in the worst scenario (with the
# default Linux settings).
# 
# If you have latency problems turn this to "yes". Otherwise leave it as
# "no" that is the safest pick from the point of view of durability.
no-appendfsync-on-rewrite no
 
# Automatic rewrite of the append only file.
# AOF 自动重写
# 当AOF文件增长到一定大小的时候Redis能够调用 BGREWRITEAOF 对日志文件进行重写 
# 
# 它是这样工作的：Redis会记住上次进行些日志后文件的大小(如果从开机以来还没进行过重写，那日子大小在开机的时候确定)
#
# 基础大小会同现在的大小进行比较。如果现在的大小比基础大小大制定的百分比，重写功能将启动
# 同时需要指定一个最小大小用于AOF重写，这个用于阻止即使文件很小但是增长幅度很大也去重写AOF文件的情况
# 设置 percentage 为0就关闭这个特性
auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb

################################## SLOW LOG ###################################
# Redis Slow Log 记录超过特定执行时间的命令。执行时间不包括I/O计算比如连接客户端，返回结果等，只是命令执行时间
# 
# 可以通过两个参数设置slow log：一个是告诉Redis执行超过多少时间被记录的参数slowlog-log-slower-than(微妙)，
# 另一个是slow log 的长度。当一个新命令被记录的时候最早的命令将被从队列中移除
 
 
# 下面的时间以微妙微单位，因此1000000代表一分钟。
# 注意制定一个负数将关闭慢日志，而设置为0将强制每个命令都会记录
slowlog-log-slower-than 10000
 
 
# 对日志长度没有限制，只是要注意它会消耗内存
# 可以通过 SLOWLOG RESET 回收被慢日志消耗的内存
slowlog-max-len 1024
 
################################ VM ###############################
### WARNING! Virtual Memory is deprecated in Redis 2.4
### The use of Virtual Memory is strongly discouraged.
 
# Virtual Memory allows Redis to work with datasets bigger than the actual
# amount of RAM needed to hold the whole dataset in memory.
# In order to do so very used keys are taken in memory while the other keys
# are swapped into a swap file, similarly to what operating systems do
# with memory pages.
#
# To enable VM just set 'vm-enabled' to yes, and set the following three
# VM parameters accordingly to your needs.
 
vm-enabled no
# vm-enabled yes
 
# This is the path of the Redis swap file. As you can guess, swap files
# can't be shared by different Redis instances, so make sure to use a swap
# file for every redis process you are running. Redis will complain if the
# swap file is already in use.
#
# The best kind of storage for the Redis swap file (that's accessed at random) 
# is a Solid State Disk (SSD).
#
# *** WARNING *** if you are using a shared hosting the default of putting
# the swap file under /tmp is not secure. Create a dir with access granted
# only to Redis user and configure Redis to create the swap file there.
vm-swap-file /tmp/redis.swap
 
# vm-max-memory configures the VM to use at max the specified amount of
# RAM. Everything that deos not fit will be swapped on disk *if* possible, that
# is, if there is still enough contiguous space in the swap file.
#
# With vm-max-memory 0 the system will swap everything it can. Not a good
# default, just specify the max amount of RAM you can in bytes, but it's
# better to leave some margin. For instance specify an amount of RAM
# that's more or less between 60 and 80% of your free RAM.
vm-max-memory 0
 
# Redis swap files is split into pages. An object can be saved using multiple
# contiguous pages, but pages can't be shared between different objects.
# So if your page is too big, small objects swapped out on disk will waste
# a lot of space. If you page is too small, there is less space in the swap
# file (assuming you configured the same number of total swap file pages).
#
# If you use a lot of small objects, use a page size of 64 or 32 bytes.
# If you use a lot of big objects, use a bigger page size.
# If unsure, use the default :)
vm-page-size 32
 
# Number of total memory pages in the swap file.
# Given that the page table (a bitmap of free/used pages) is taken in memory,
# every 8 pages on disk will consume 1 byte of RAM.
#
# The total swap size is vm-page-size * vm-pages
#
# With the default of 32-bytes memory pages and 134217728 pages Redis will
# use a 4 GB swap file, that will use 16 MB of RAM for the page table.
#
# It's better to use the smallest acceptable value for your application,
# but the default is large in order to work in most conditions.
vm-pages 134217728
 
# Max number of VM I/O threads running at the same time.
# This threads are used to read/write data from/to swap file, since they
# also encode and decode objects from disk to memory or the reverse, a bigger
# number of threads can help with big objects even if they can't help with
# I/O itself as the physical device may not be able to couple with many
# reads/writes operations at the same time.
#
# The special value of 0 turn off threaded I/O and enables the blocking
# Virtual Memory implementation.
vm-max-threads 4
 
############################### ADVANCED CONFIG ###############################
 
# 当hash中包含超过指定元素个数并且最大的元素没有超过临界时，
# hash将以一种特殊的编码方式（大大减少内存使用）来存储，这里可以设置这两个临界值
# Redis Hash对应Value内部实际就是一个HashMap，实际这里会有2种不同实现，
# 这个Hash的成员比较少时Redis为了节省内存会采用类似一维数组的方式来紧凑存储，而不会采用真正的HashMap结构，对应的value redisObject的encoding为zipmap,
# 当成员数量增大时会自动转成真正的HashMap,此时encoding为ht。
hash-max-zipmap-entries 512
hash-max-zipmap-value 64
 
# list数据类型多少节点以下会采用去指针的紧凑存储格式。
# list数据类型节点值大小小于多少字节会采用紧凑存储格式。
list-max-ziplist-entries 512
list-max-ziplist-value 64
 
# set数据类型内部数据如果全部是数值型，且包含多少节点以下会采用紧凑格式存储。
set-max-intset-entries 512
 
# zsort数据类型多少节点以下会采用去指针的紧凑存储格式。
# zsort数据类型节点值大小小于多少字节会采用紧凑存储格式。
zset-max-ziplist-entries 128
zset-max-ziplist-value 64
 
 
# Redis将在每100毫秒时使用1毫秒的CPU时间来对redis的hash表进行重新hash，可以降低内存的使用
# 
# 当你的使用场景中，有非常严格的实时性需要，不能够接受Redis时不时的对请求有2毫秒的延迟的话，把这项配置为no。
#
# 如果没有这么严格的实时性要求，可以设置为yes，以便能够尽可能快的释放内存
activerehashing yes
 
################################## INCLUDES ###################################
 
# 指定包含其它的配置文件，可以在同一主机上多个Redis实例之间使用同一份配置文件，而同时各个实例又拥有自己的特定配置文件
 
# include /path/to/local.conf
# include /path/to/other.conf
```

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
  - 多级缓存



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
  - 多级缓存



