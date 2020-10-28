## Sentinel

#### Sentinel入门

- 限流分为单机和分布式两种

  - 单机限流
    - 指限定当前进程里面的某个代码片段的 QPS / 并发线程数 / 整个机器负载指数，一旦超出规则配置的数值就会抛出异常或者返回 false
    - 把被限流的代码片段称为 临界区
  - 分布式限流
    - 需要另启一个集中的发票服务器，针对每个指定的资源每秒只会生成一定量的票数，在执行临界区的代码之前先去集中的发票服务领票，如果领成功了就可以执行，否则就会抛出限流异常
    - 使用了 Netty 框架
    - 代价较高，需要多一次网络读写操作

- Sentinel 在使用上提供了两种形式，一种是异常捕获形式，一种是布尔形式

  - 异常捕获形式
    - 使用 Sentinel 需要我们提供限流规则，在规则的基础上，将临界区代码（被限流的代码片段）使用限流作用域结构包裹起来
    - 在下面的例子中限定了 tutorial 资源的单机 QPS 不得超出 1，但是实际上它的运行 QPS 是 2，这多出来的执行逻辑就会被限制，对应的 Sphu.entry() 方法就会抛出限流异常 BlockException
    - Sentinel 在本地文件中记录了详细的限流日志，可以将这部分日志收集起来作为报警的数据源

  ```java
  import com.alibaba.csp.sentinel.Entry;
  import com.alibaba.csp.sentinel.SphU;
  import com.alibaba.csp.sentinel.slots.block.BlockException;
  
  public class SentinelTest {
      public static void main(String[] args) {
          // 配置规则
          List<FlowRule> rules = new ArrayList<>();
          FlowRule rule = new FlowRule();
          rule.setResource("tutorial");
          // QPS 不得超出 1
          rule.setCount(1);
          rule.setGrade(RuleConstant.FLOW_GRADE_QPS);
          rule.setLimitApp("default");
          rules.add(rule);
          // 加载规则
          FlowRuleManager.loadRules(rules);
          // 下面开始运行被限流作用域保护的代码
          while (true) {
              Entry entry = null;
              try {
                  entry = SphU.entry("tutorial");
                  System.out.println("hello world");
              } catch (BlockException e) {
                  System.out.println("blocked");
              } finally {
                  if (entry != null) {
                      entry.exit();
                  }
              }
              try {
                  Thread.sleep(500);
              } catch (InterruptedException e) {}
          }
      }
  }
  ```

  - 布尔形式

  ```java
  import java.util.ArrayList;
  import java.util.List;
  import com.alibaba.csp.sentinel.SphO;
  import com.alibaba.csp.sentinel.slots.block.RuleConstant;
  import com.alibaba.csp.sentinel.slots.block.flow.FlowRule;
  import com.alibaba.csp.sentinel.slots.block.flow.FlowRuleManager;
  
  public class SentinelTest {
      public static void main(String[] args) {
          // 配置规则
          List<FlowRule> rules = new ArrayList<>();
          FlowRule rule = new FlowRule();
          rule.setResource("tutorial");
          // QPS 不得超出 1
          rule.setCount(1);
          rule.setGrade(RuleConstant.FLOW_GRADE_QPS);
          rule.setLimitApp("default");
          rules.add(rule);
          FlowRuleManager.loadRules(rules);
          // 运行被限流作用域保护的代码
          while (true) {
              if (SphO.entry("tutorial")) {
                  try {
                      System.out.println("hello world");
                  } finally {
                      SphO.exit();
                  }
              } else {
                  System.out.println("blocked");
              }
              try {
                  Thread.sleep(500);
              } catch (InterruptedException e) {}
          }
      }
  }
  ```

- 规则控制

  - 上面的例子中规则都是通过代码写死的，在实际的项目中，规则应该需要支持动态配置
  - 这就需要有一个规则配置源，它可以是 Redis、Zookeeper 等数据库，还需要有一个规则变更通知机制和规则配置后台，允许管理人员可以在后台动态配置规则并实时下发到业务服务器进行控制
  - 有一些规则源存储不支持事件通知机制，比如关系数据库，Sentinel 也提供了定时刷新规则，比如每隔几秒来刷新内存里面的限流规则。下面是 redis 规则源定义：

  ```java
  // redis 地址
  RedisConnectionConfig redisConf = new RedisConnectionConfig("localhost", 6379, 1000);
  // 反序列化算法
  Converter<String, List<FlowRule>> converter = r -> JSON.parseArray(r, FlowRule.class);
  // 定义规则源，包含全量和增量部分
  // 全量是一个字符串key，增量是 pubsub channel key
  ReadableDataSource<String, List<FlowRule>> redisDataSource = new RedisDataSource<List<FlowRule>>(redisConf,"app_key", "app_pubsub_key", converter);
  FlowRuleManager.register2Property(redisDataSource.getProperty());
  ```

- 健康状态上报与检查

  - 接入 Sentinel 的应用服务器需要将自己的限流状态上报到 Dashboard，这样就可以在后台实时呈现所有服务的限流状态
  - Sentinel 使用拉模型来上报状态，它在当前进程注册了一个 HTTP 服务，Dashboard 会定时来访问这个 HTTP 服务来获取每个服务进程的健康状况和限流信息
  - Sentinel 需要将服务的地址以心跳包的形式上报给 Dashboard，如此 Dashboard 才知道每个服务进程的 HTTP 健康服务的具体地址
  - 如果进程下线了，心跳包就停止了，那么对应的地址信息也会过期，如此Dashboard 就能准实时知道当前的有效进程服务列表

- 分布式限流

  - 前面我们说到分布式限流需要另起一个 Ticket Server，由它来分发 Ticket，能够获取到 Ticket 的请求才可以允许执行临界区代码，Ticket 服务器也需要提供规则输入源
  - Ticket Server 是单点的，如果 Ticket Server 挂掉了，应用服务器限流将自动退化为本地模式

- 熔断降级

  - 限流在于限制流量，也就是 QPS 或者线程的并发数，还有一种情况是请求处理不稳定或者服务损坏，导致请求处理时间过长或者老是频繁抛出异常，这时就需要对服务进行降级处理。
  - 降级处理和限流处理在形式上没有明显差异，也是以同样的形式定义一个临界区，区别是需要对抛出来的异常需要进行统计，这样才可以知道请求异常的频率，有了这个指标才会触发降级
  - 触发限流时会抛出 FlowException，触发熔断时会抛出 DegradeException，这两个异常都继承自 BlockException

  ```java
  // 定义降级规则
  List<DegradeRule> rules = new ArrayList<>();
  DegradeRule rule = new DegradeRule();
  rule.setResource("tutorial");
  // 5s内异常不得超出10
  rule.setCount(10);
  rule.setGrade(RuleConstant.DEGRADE_GRADE_EXCEPTION_COUNT);
  rule.setLimitApp("default");
  rules.add(rule);
  DegradeRuleManager.loadRules(rules);
  
  Entry entry = null;
  try {
    entry = SphU.entry(key);
    // 业务代码在这里
  } catch (Throwable t) {
    // 记录异常
    if (!BlockException.isBlockException(t)) {
      Tracer.trace(t);
    }
  } finally {
    if (entry != null) {
      entry.exit();
    }
  }
  ```

- 热点限流

  - 限制动态的热点资源。内部采用 LRU 算法计算出 topn 热点资源，然后对 topn 的资源进行限流，同时还提供特殊资源特殊对待的参数设置
  - 在下面的例子中限定了同一个用户的访问频次，同时也限定了同一本书的访问频次，但是对于某个特殊用户和某个特殊的书进行了特殊的频次设置

  ```java
  ParamFlowRule ruleUser = new ParamFlowRule();
  // 同样的 userId QPS 不得超过 10
  ruleUser.setParamIdx(0).setCount(10);
  // qianwp用户特殊对待，QPS 上限是 100
  ParamFlowItem uitem = new ParamFlowItem("qianwp", 100, String.class);
  ruleUser.setParamFlowItemList(Collections.singletonList(uitem));
  
  ParamFlowRule ruleBook = new ParamFlowRule();
  // 同样的 bookId QPS 不得超过 20
  ruleBook.setParamIdx(1).setCount(20);
  // redis 的书特殊对待，QPS 上限是 100
  ParamFlowItem bitem = new ParamFlowItem("redis", 100, String.class);
  ruleBook.setParamFlowItemList(Collections.singletonList(item));
  
  // 加载规则
  List<ParamFlowRule> rules = new ArrayList<>();
  rules.add(ruleUser);
  rules.add(ruleBook);
  ParamFlowRuleManager.loadRules(rules)；
  
  // userId的用户访问bookId的书
  Entry entry = Sphu.entry(key, EntryType.IN, 1, userId, bookId);
  ```

- 系统自适应限流-过载保护

  - 当系统的负载较高时，为了避免系统被洪水般的请求冲垮，需要对当前的系统进行限流保护

  - 保护的方式是逐步限制 QPS，观察到系统负载恢复后，再逐渐放开 QPS，如果系统的负载又下降了，就再逐步降低 QPS。如此达到一种动态的平衡，这里面涉及到一个特殊的保持平衡的算法。

  - 系统的负载指数存在一个问题，它取自操作系统负载的 load1 参数，load1 参数更新的实时性不足，从 load1 超标到恢复的过程存在一个较长的过渡时间，如果使用一刀切方案，在这段恢复时间内阻止任何请求，待 load1 恢复后又立即放开请求，势必会导致负载的大起大落，服务处理的时断时开。为此作者将 TCP 拥塞控制算法的思想移植到这里实现了系统平滑的过载保护功能。这个算法很精巧，代码实现并不复杂，效果却是非常显著

  - 算法定义了一个稳态公式，稳态一旦打破，系统负载就会出现波动。算法的本质就是当稳态被打破时，通过持续调整相关参数来重新建立稳态

    - ThreadNum * (1/ResponseTime) = QPS。系统的 QPS = 线程数 x 单个线程每秒可以执行的请求数量。系统会实时采样统计所有临界区的 QPS 和 ResponseTime，就可以计算出相应的稳态并发线程数。当负载超标时，通过判定当前的线程数是否超出稳态线程数就可以明确是否需要拒绝当前的请求

  - 定义自适应限流规则需要提供多个参数

    - 系统的负载水平线，超过这个值时触发过载保护功能

    - 当过载保护超标时，允许的最大线程数、最长响应时间和最大 QPS，可以不设置

  ```java
  List<SystemRule> rules = new ArrayList<SystemRule>();
  SystemRule rule = new SystemRule();
  rule.setHighestSystemLoad(3.0);
  rule.setAvgRt(10);
  rule.setQps(20);
  rule.setMaxThread(10);
  rules.add(rule);
  SystemRuleManager.loadRules(Collections.singletonList(rule));
  ```



#### Sentinel详解

- 