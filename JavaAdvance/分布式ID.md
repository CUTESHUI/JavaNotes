## 分布式ID

#### 背景

- ID是数据的唯一标识，传统的做法是利用 UUID 和数据库的自增ID
- 在互联网企业中，大部分公司使用的都是 Mysql，并且因为需要事务支持，所以通常会使用 Innodb 存储引擎
- UUID太长以及无序，所以并不适合在Innodb中来作为主键，自增ID比较合适
- 但是随着公司的业务发展，数据量将越来越大，需要对数据进行分表，而分表后，每个表中的数据都会按自己的节奏进行自增，很有可能出现ID冲突
- 因此需要一个单独的机制来负责生成唯一ID，生成出来的ID也可以叫做**分布式ID**，或**全局ID**



#### 数据库自增ID

```mysql
CREATE DATABASE `SEQID`;

CREATE TABLE SEQID.SEQUENCE_ID (
  id bigint(20) unsigned NOT NULL auto_increment, 
  str char(10) NOT NULL default '',
  PRIMARY KEY (id),
  UNIQUE KEY str (str)
) ENGINE=MyISAM;
```

使用下面的语句生成并获取到一个自增ID

```mysql
begin;
replace into SEQUENCE_ID (str) VALUES ('anyword');
select last_insert_id();
commit;
```

str 只有能插入数据才能产生自增id。而对于插入用的是 replace，replace 会先看是否存在和 str 一样的数据，如果存在则先 delete 再 insert，如果不存在则直接 insert



- 这种生成分布式ID的机制，需要一个单独的 Mysql 实例
- 但是基于性能与可靠性来考虑的话都不够，业务系统每次需要一个ID时，都需要请求数据库获取，性能低，并且如果此数据库实例下线了，那么将影响所有的业务系统



#### 数据库多主模式

- 如两个数据库组成一个**主从模式**集群，正常情况下可以解决数据库可靠性问题，但是如果主库挂掉后，数据没有及时同步到从库，这个时候会出现ID重复的现象
- 可以使用**双主模式**集群，也就是两个 Mysql 实例都能单独的生产自增ID，这样能够提高效率，但是如果不经过其他改造的话，这两个 Mysql 实例很可能会生成同样的ID，需要单独给每个Mysql实例配置不同的起始值和自增步长



第一台Mysql实例配置：

```sql
set @@auto_increment_offset = 1;     -- 起始值
set @@auto_increment_increment = 2;  -- 步长
```

第二台Mysql实例配置：

```sql
set @@auto_increment_offset = 2;     -- 起始值
set @@auto_increment_increment = 2;  -- 步长
```

经过上面的配置后，这两个 Mysql 实例生成的 id 序列

- mysql1：起始值为1，步长为2,ID生成的序列为：1,3,5,7,9,...
- mysql2：起始值为2，步长为2,ID生成的序列为：2,4,6,8,10,...

对于这种生成分布式 ID 的方案，需要单独新增一个生成分布式 ID 应用，比如 DistributIdService，该应用提供一个接口供业务应用获取 ID，业务应用需要一个ID时，通过 rpc 的方式请求 DistributIdService，DistributIdService 随机去上面的两个 Mysql 实例中去获取 ID

实行这种方案后，就算其中某一台 Mysql实例下线了，也不会影响 DistributIdService，DistributIdService 仍然可以利用另外一台Mysql来生成ID

但是这种方案的扩展性不太好，如果两台Mysql实例不够用，需要新增Mysql实例来提高性能时，这时就会比较麻烦

现在如果要新增一个实例mysql3，要怎么操作呢？ 第一，mysql1、mysql2的步长肯定都要修改为3，而且只能是人工去修改，这是需要时间的。 第二，因为mysql1和mysql2是不停在自增的，对于mysql3的起始值我们可能要定得大一点，以给充分的时间去修改mysql1，mysql2的步长。 第三，在修改步长的时候很可能会出现重复ID，要解决这个问题，可能需要停机才行



#### 号段模式

- 号段可以理解成批量获取，比如 DistributIdService 从数据库获取 ID时，如果能批量获取多个ID并缓存在本地的话，那样将大大提供业务应用获取ID的效率
- 比如 DistributIdService 每次从数据库获取 ID 时，就获取一个号段，比如(1,1000]，这个范围表示了1000个ID，业务应用在请求 DistributIdService 提供ID时，DistributIdService 只需要在本地从1开始自增并返回即可，而不需要每次都请求数据库，一直到本地自增到1000时，也就是当前号段已经被用完时，才去数据库重新获取下一号段

```mysql
CREATE TABLE id_generator (
  id int(10) NOT NULL,
  current_max_id bigint(20) NOT NULL COMMENT '当前最大id',
  increment_step int(10) NOT NULL COMMENT '号段的长度',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

这个数据库表用来记录自增步长以及当前自增 ID 的最大值（也就是当前已经被申请的号段的最后一个值），因为自增逻辑被移到 DistributIdService 中去了，所以数据库不需要这部分逻辑了。

这种方案不再强依赖数据库，就算数据库不可用，那么 DistributIdService也能继续支撑一段时间。但是如果 DistributIdService 重启，会丢失一段 ID，导致 ID 空洞

为了提高 DistributIdService 的高可用，需要做一个集群，业务在请求 DistributIdService 集群获取 ID 时，会随机的选择某一个 DistributIdService 节点进行获取，对每一个 DistributIdService 节点来说，数据库连接的是同一个数据库，那么可能会产生多个 DistributIdService 节点同时请求数据库获取号段，那么这个时候需要利用乐观锁来进行控制，比如在数据库表中增加一个 version 字段，在获取号段时使用如下 SQL：

```mysql
update id_generator set current_max_id=#{newMaxId}, version=version+1 where version = #{version}
```

因为 newMaxId 是 DistributIdService 中根据 oldMaxId+步长算出来的，只要上面的 update 更新成功了就表示号段获取成功了。

为了提供数据库层的高可用，需要对数据库使用多主模式进行部署，对于每个数据库来说要保证生成的号段不重复，这就需要利用最开始的思路，再在刚刚的数据库表中增加起始值和步长，比如如果现在是两台 Mysql，那么 mysql1将生成号段（1,1001]，自增的时候序列为1，3，4，5，7.... mysql1将生成号段（2,1002]，自增的时候序列为2，4，6，8，10...



#### 雪花算法

- snowflake 是 twitter 开源的分布式ID生成算法，是一种算法，所以它和上面的三种生成分布式ID机制不太一样，它不依赖数据库
- 核心思想是：分布式ID固定是一个 long 型的数字，一个 long 型占8个字节，也就是64bit
  - 第一个bit位是标识部分，在java中由于long的最高位是符号位，正数是0，负数是1，一般生成的ID为正数，所以固定为0
  - 时间戳部分占41bit，这个是毫秒级的时间，一般实现上不会存储当前的时间戳，而是时间戳的差值（当前时间-固定的开始时间），这样可以使产生的ID从更小值开始；41位的时间戳可以使用69年，(1L << 41) / (1000L * 60 * 60 * 24 * 365) = 69年
  - 工作机器id占10bit，这里比较灵活，比如，可以使用前5位作为数据中心机房标识，后5位作为单机房机器标识，可以部署1024个节点
  - 序列号部分占12bit，支持同一毫秒内同一个节点可以生成4096个ID
- 在大厂里，其实并没有直接使用 snowflake，而是进行了改造，因为 snowflake 算法中最难实践的就是工作机器id，原始的snowflake算法需要人工去为每台机器去指定一个机器id，并配置在某个地方从而让 snowflake 从此处获取机器id。但是在大厂里，机器是很多的，人力成本太大且容易出错，所以大厂对 snowflake 进行了改造



#### 百度（uid-generator）

- uid-generator使用的就是snowflake，只是在生产机器id，也叫做workId时有所不同
- uid-generator中的workId是由uid-generator自动生成的，并且考虑到了应用部署在docker上的情况，在uid-generator中用户可以自己去定义workId的生成策略，默认提供的策略是：应用启动时由数据库分配。说的简单一点就是：应用在启动时会往数据库表(uid-generator需要新增一个WORKER_NODE表)中去插入一条数据，数据插入成功后返回的该数据对应的自增唯一id就是该机器的workId，而数据由host，port组成
- 对于uid-generator中的workId，占用了22个bit位，时间占用了28个bit位，序列化占用了13个bit位，需要注意的是，和原始的snowflake不太一样，时间的单位是秒，而不是毫秒，workId也不一样，同一个应用每重启一次就会消费一个workId



#### 美团（Leaf）

- 美团的 Leaf 也是一个分布式ID生成框架。它非常全面，即支持号段模式，也支持snowflake模式
- Leaf 中的 snowflake 模式和原始 snowflake 算法的不同点，也主要在 workId 的生成，Leaf 中 workId 是基于 ZooKeeper 的顺序 Id 来生成的，每个应用在使用 Leaf-snowflake 时，在启动时都会都在 Zookeeper 中生成一个顺序Id，相当于一台机器对应一个顺序节点，也就是一个 workId



#### Redis

- 其实和利用 Mysql 自增ID类似，可以利用 Redis 中的 incr 命令来实现原子性的自增与返回

- 使用 Redis 的效率是非常高的，但是要考虑持久化的问题。Redis支持 RDB 和 AOF 两种持久化的方式。

  - RDB 持久化相当于定时打一个快照进行持久化，如果打完快照后，连续自增了几次，还没来得及做下一次快照持久化，这个时候 Redis 挂掉了，重启 Redis 后会出现 ID 重复

  - AOF 持久化相当于对每条写命令进行持久化，如果 Redis 挂掉了，不会出现 ID 重复的现象，但是会由于 incr 命令过得，导致重启恢复数据时间过长

```
127.0.0.1:6379> set seq_id 1     // 初始化自增ID为1
OK
127.0.0.1:6379> incr seq_id      // 增加1，并返回
(integer) 2
127.0.0.1:6379> incr seq_id      // 增加1，并返回
(integer) 3
```



