### Redis api的使用

### 单线程为什么怎么快

1. 存内存
2. 非阻塞io
3. 避免线程切换和竞态消耗

**使用注意**

1. 一次只运行一条命令
2. 拒绝慢命令 keys,flushall, flushdb,slow lua script等
3. 其实不是单线程，

### 字符串总结

| 命令                     | 解释                             | 时间复杂度 |
| ------------------------ | -------------------------------- | ---------- |
| setnx key value          | key不存在 才色值                 | 1          |
| set  key value xx        | key存在，才设置                  | 1          |
| set key value            | 不管key 是否存在，都设置         | 1          |
| Getset key new value     | set key newvalue 并返回旧的value | 1          |
| Append key value         | 将value 追加到旧的value          | 1          |
| Strlen key               | 返回字符串的长度                 |            |
| Incrbyfloat              | 增加key对应的值                  | 1          |
| Getrange key start end   | 获得字符串指定下标所有的值       | 1          |
| Setrange key index value | 设置指定下标所有对应的值         |            |

### list 列表

- 有序，可重复

 

| 命令                                     | 解释                                                         | 时间复杂 |
| ---------------------------------------- | ------------------------------------------------------------ | -------- |
| linsert key before\|after value newvalue |                                                              | n        |
| Lpop rpop                                |                                                              | 1        |
| lrem key count value                     |                                                              | n        |
| ltrim key start end                      | 按照索引范围修剪列表                                         | n        |
| Lrange key start end(包含end)            |                                                              | n        |
| Lindex key  index                        | 获取列表指定索引的item                                       | n        |
| llen key                                 | 获取列表长度                                                 | 1        |
| lset key index newvalue                  |                                                              | n        |
| Blpop brpop blpop key timeout            | lop为阻塞版本，timeout 是阻塞超时时间，timeout=0,为永远不阻塞 |          |

#### tips

1. Lpush +lpop = stack
2. lpush +rpop = queue
3. lpush + ltraim =capped collecion
4. Lpush + brpop = message queue

###hash

| 命令                               | 解释                                                        | 复杂度 |
| ---------------------------------- | ----------------------------------------------------------- | ------ |
| Hgetall key                        | 获得该key的所有value,返回一个map (小心使用，牢记单线程)     | n      |
| Hvals key                          | 获得该key的所有value                                        | 1      |
| Hkeys key                          | 获得该key的所有keys                                         | 1      |
| Hsetnx key field value             | 设置hash key 对应field的value 如果field已经存在，则设置失败 | 1      |
| Hincryby key filed int counter     | hash key 对应的field的value自增intcounter                   | 1      |
| Hincrybyfloat key fielf floatcount | hincryby浮点版                                              | 1      |

### set

| 命令                  | 解释                                 | 复杂度 |
| --------------------- | ------------------------------------ | ------ |
| Sadd srem             | 向集合中添加和删除元素               | 1      |
| scard                 | 计算集合大小                         |        |
| Sismember             | 判断it是否在集合中                   |        |
| Srandmember 5         | 从集合中随机选5个元素                |        |
| Smembers              | 取出集合中所有元素（无序，小心使用） |        |
| spop                  | 从集合中随机弹出一个元素             |        |
| sdiff sinter sunion   | 差集，交集，并集                     |        |
| sdiff + store destkey | 将差集的结果保存在destkey中          |        |

### 有序集合 zset

| 命令                                | 解释                             | 复杂度    |
| ----------------------------------- | -------------------------------- | --------- |
| zadd key score element              | 添加score 和element              | Logn      |
| Zrem key element (可以是多个)       | 删除元素                         | 1         |
| zscore key element                  | 查询元素分数                     | 1         |
| Zincrby key increscore element      | 增加或减少元素的分数             | 1         |
| zcard key                           | 返回元素的总个数                 | 1         |
| Zrank key element                   | 获得一个元素的排名               | 1         |
| zrange key start end [withscores]   | 返回指定索引内的升序元素[分数]   | Logn +m   |
| Zrangebyscore key minscore maxscore | 返回指定分数范围内的升序元素     | Logn +m   |
| Zcount key minscore maxscore        | 返回有序集合内在指定分数内的个数 | log(n) +m |
| Zremrangebyrank key start end | 删除指定排名内的升序元素 | log(n) +m |
| Zremrangebyscore key start end | 删除指定分数内的升序元素 | log(n) +m |
| Zrevrank key element | 获得一个元素的排名 从低到高 | 1 |
| Zrevrange key start end | 返回指定索引内的降序元素[分数] |  |
| Zrevrangebyscore | 返回指定分数范围内的降序元素 |  |
| Zinterstore | 交集 |  |
| Zunionstore | 并集 |  |
|  |  |  |

#### 实战 排行榜

### 瑞士军刀Redis

- 慢查询
- pipeline
- 发布订阅
- bitmap
- hyperLogLog
- GEO

#### 慢查询

- 生命周期
- 两个配置
- 三个命令
- 运维经验
- ![](https://cdn.jsdelivr.net/gh/Nobodylesszb/upic@pic/upload/pics/1574084517-image-20191031211101476.png)
- 两个配置 
  - slowlog-max-len
    - 先进先出队列
    - 固定长度
    - 保存在内存中
  - Slowlog-log-slower-than
    - 慢查询阈值（微秒）
    - Slowlog-log-slower-than=0 记录所有命令
  - 配置方法
    - Config get slowlog-max-len =128
    - Config get slowlog-log-slower-than 10000
  - 修改配置文件
  - 动态配置
- 慢查询命令
  - Slowlog get[n]：获取慢查询队列
  - Slowlog len : 获取慢查询队列长度
  - Slowlog reset 清空慢查询
- 运维经验
  - Slowlog-max-len 不要设置太大，默认10没ms,通常设置1ms
  - Slowlog-log-slower-than 不要设置太小，通常设置1000左右
  - 理解命令生命周期
  - 定期持久化慢查询

#### Pipeline

![](https://cdn.jsdelivr.net/gh/Nobodylesszb/upic@pic/upload/pics/1574082479-image-20191031212756871.png)

![](https://cdn.jsdelivr.net/gh/Nobodylesszb/upic@pic/upload/pics/1574082542-image-20191031212910830.png)

- 注意
  1. redis的命令时间是微秒级别
  2. pipeline每次条数要控制（网络）
- 使用建议
  - 注意每次pipline携带数据量
  - pipeline每次只能作用在一个redis节点上

#### 发布订阅

- api
  - publish channel message
  - Subscribe [channel]
  - Unsubscribe channel
  - psubscribe [pattern]订阅模式
- ![](https://cdn.jsdelivr.net/gh/Nobodylesszb/upic@pic/upload/pics/1574082573-image-20191031214409798.png)

#### bitmap

- api
  - Getbit key offset
  - Setbit key offset value
  - bitcount[start,end] 获得指定范围内位值位1的个数
  - bit op destkey key [key] 将多个bitmap的and,or,not,xor操作并将结果保存在destkey中

#### hyperloglog

- 极少的空间完成独立数量统计
- api
  - pfadd key element [elment...] 向hyperloglog添加元素
  - pfcount key 计算hyperloglog的独立总数
  - pfmerge destkey sourcekey [sourcekey] 合并多个hyperloglog
- 缺点
  - 是否能容忍错误(错误率:0.81%)
  - 是否需要单条数据

#### geo

- api
  - geoadd key log lat memeber
  - Geopos key member 获取地理位置信息
  - Geodist key member1 member2 [m,km,mi,ft]
  - Georadius

#### redis删除

Redis中有个设置时间过期的功能，即对存储在 redis 数据库中的值可以设置一个过期时间。作为一个缓存数据库， 这 是非常实用的。如我们一般项目中的 token 或者一些登录信息，尤其是短信验证码都是有时间限制的，按照传统的 数据库处理方式，一般都是自己判断过期，这样无疑会严重影响项目性能。

我们 set key 的时候，都可以给一个 expire time，就是过期时间，通过过期时间我们可以指定这个 key 可以存活的 时间。

如果假设你设置了一批 key 只能存活1个小时，那么接下来1小时后，redis是怎么对这批key进行删除的？ 定期删除+惰性删除。

- 定期删除
  - redis默认是每隔 100ms 就随机抽取一些设置了过期时间的key，检查其是否过期，如果过期就删除。 注意这里是随机抽取的。为什么要随机呢？你想一想假如 redis 存了几十万个 key ，每隔100ms就遍历所有的 设置过期时间的 key 的话，就会给 CPU 带来很大的负载！
- 惰性删除
  - 定期删除可能会导致很多过期 key 到了时间并没有被删除掉。所以就有了惰性删除。假如你的过期key， 靠定期删除没有被删除掉，还停留在内存里，除非你的系统去查一下那个 key，才会被redis给删除掉。这就是所谓 的惰性删除，也是够懒的哈！ 但是仅仅通过设置过期时间还是有问题的。我们想一下：如果定期删除漏掉了很多过期 key，然后你也没及时去查， 也 就没走惰性删除，此时会怎么样？如果大量过期key堆积在内存里，导致redis内存块耗尽了。怎么解决这个问题 呢？

#### redis内存淘汰策略

redis 提供 6种数据淘汰策略：

1. volatile-lru：从已设置过期时间的数据集（server.db[i].expires）中挑选最近最少使用的数据淘汰
2. volatile-ttl：从已设置过期时间的数据集（server.db[i].expires）中挑选将要过期的数据淘汰
3. volatile-random：从已设置过期时间的数据集（server.db[i].expires）中任意选择数据淘汰
4. allkeys-lru：当内存不足以容纳新写入数据时，在键空间中，移除最近最少使用的key（这个是最常用的）.
5. allkeys-random：从数据集（server.db[i].dict）中任意选择数据淘汰
6. no-eviction：禁止驱逐数据，也就是说当内存不足以容纳新写入数据时，新写入操作会报错。这个应该没人使 用吧！



### Redis持久化的取舍和选择

#### rdb

- 触发机制
  - sava 同步 阻塞 复杂度n
  - bgsave 异步 fork子进程
  - 自动
- ![](https://cdn.jsdelivr.net/gh/Nobodylesszb/upic@pic/upload/pics/1574081564-image-20191031225903551.png)
- ![](https://cdn.jsdelivr.net/gh/Nobodylesszb/upic@pic/upload/pics/1574081616-image-20191031230608806.png)
- 全量复制

- debug rekiad
- Shutdown 这些都会产生rdb

![](https://cdn.jsdelivr.net/gh/Nobodylesszb/upic@pic/upload/pics/1574081658-image-20191031235039478.png)

#### AOF

- rdb问题
  - 耗时，耗性能
  - ![](https://cdn.jsdelivr.net/gh/Nobodylesszb/upic@pic/upload/pics/1574081690-image-20191101221025164.png)
- 原理
  - 写日志模式
- 三种策略
  - always
    - ![](https://cdn.jsdelivr.net/gh/Nobodylesszb/upic@pic/upload/pics/1574081741-image-20191101221235930.png)
  - everysec
    - ![](https://cdn.jsdelivr.net/gh/Nobodylesszb/upic@pic/upload/pics/1574081767-image-20191101221253532.png)
  - no
    - ![](https://cdn.jsdelivr.net/gh/Nobodylesszb/upic@pic/upload/pics/1574081803-image-20191101221337060.png)
  - ![](https://cdn.jsdelivr.net/gh/Nobodylesszb/upic@pic/upload/pics/1574081173-image-20191101221428426.png)
- AOF重写
  -   减少磁盘占用量
  - 加速恢复速度
- aof重写实现方式
  - bgrewriteaof
    - 异步执行
  - aof重写配置
    - Auto-aof-rewrite-min-size aof文件重写需要的尺寸
    - auto-aof-rewrite-percentage aof文件增长率
  - 统计
    - aof_current_size aof当前尺寸
    - aof_base_size aof上次启动和重写的尺寸
    - ![](https://cdn.jsdelivr.net/gh/Nobodylesszb/upic@pic/upload/pics/1574081851-image-20191101223153486.png)

#### RDB和aof抉择

- ![](https://cdn.jsdelivr.net/gh/Nobodylesszb/upic@pic/upload/pics/1574081910-image-20191101230707741.png)
- RDB最佳策略
  - 关
  - 集中管理
  - 主从，从开
- AOF最佳策略
  - 开：缓存和存储
  - aof重写集中管理
  - everysec
- 最佳策略
  - 小分片
  - 缓存或者存储
  - 监控(硬盘，内存，负载，网络)

### 开发运维常见问题

**fork**

![](https://cdn.jsdelivr.net/gh/Nobodylesszb/upic@pic/upload/pics/1574081963-image-20191102205609558.png)

**子进程优化**

![](https://cdn.jsdelivr.net/gh/Nobodylesszb/upic@pic/upload/pics/1574081999-image-20191102205845704.png)

**aof追加**

![](https://cdn.jsdelivr.net/gh/Nobodylesszb/upic@pic/upload/pics/1574082037-image-20191102210230275.png)

### redis复制的原理和优化

#### 主从复制实现方式

1. slaveof命令
   1. Slaveof host port 称为某个主机的从节点
   2. slaveof no one 取消复制
   3. 成为从节点后之前的数据会清零
2. 配置
   1. Slaveof ip port
   2. Slave-read-only yes
   3. info replication 查询分片消息

#### 全量复制

![](https://cdn.jsdelivr.net/gh/Nobodylesszb/upic@pic/upload/pics/1574082108-image-20191102221832531.png)

**开销**

- bgsave时间
- rdb文件网络传输时间
- 从节点清除数据时间
- 从节点加载rdb的时间
- 可能的aof重写时间

#### 部分复制

![](https://cdn.jsdelivr.net/gh/Nobodylesszb/upic@pic/upload/pics/1574082192-image-20191102222157245.png)

#### **故障处理**

- slave宕机
- ![](https://cdn.jsdelivr.net/gh/Nobodylesszb/upic@pic/upload/pics/1574082252-image-20191102222516833.png)
- master宕机
- ![](https://cdn.jsdelivr.net/gh/Nobodylesszb/upic@pic/upload/pics/1574082290-image-20191102222612720.png)

#### 开发与运维中的问题

1. 读写分离
   1. 复制数据延迟
   2. 读到过期数据
   3. 从节点故障
2. 主从配置不一致
   1. 例如maxmemory不一致，丢失数据
3. 规避全量复制
   1. 第一次全量复制 不可规避
   2. 节点运行id不匹配
      1. 主节点重启（运行id变化）
      2. 故障转移
   3. 复制积压缓冲区不足
      1. 网络中断，部分复制无法满足
      2. 增大复制缓冲区配置rel_backlog_size 网络增强
4. 规避复制风暴
   1. 单主节点复制风暴
      1. 一个主节点拥有很多从节点，如果主节点挂了，那么很多从节点就会进行全量复制
      2. 主节点重启，多从节点复制
      3. 更换复制拓扑
   2. 单机器复制风暴
      1. 机器宕机后，大量全量复制
      2. 主节点分散多机器

### redis常见故障及其解决方案

1. 缓存穿透

   - 针对缓存穿透的情况， 简单的对策就是将不存在的数据访问结果， 也存储到缓存中，避免缓存访

   问的穿透。最终不存在商品数据的访问结果也缓存下来。有效的避免缓存穿透的风险。

   - 布隆过滤器

2. 缓存雪崩

   当缓存重启或者大量的缓存在某一时间段失效， 这样就导致大批流量直接访问数据库， 对 DB 造成压力， 从而引起 DB 故障，系统崩溃。

   举例来说， 我们在准备一项抢购的促销运营活动，活动期间将带来大量的商品信息、库存等相关信息的查询。 为了避免商品数据库的压力，将商品数据放入缓存中存储。 不巧的是，抢购活动期间，大量的热门商品缓存同时失效过期了，导致很大的查询流量落到了数据库之上。对于数据库来说造成很大的压力

   - 将商品根据品类热度分类， 购买比较多的类目商品缓存周期长一些， 购买相对冷门的类目 商品，缓存周期短一些；
   - 在设置商品具体的缓存生效时间的时候， 加上一个随机的区间因子， 比如说 5~10 分钟 之间来随意选择失效时间；
   3. 提前预估 DB 能力， 如果缓存挂掉，数据库仍可以在一定程度上抗住流量的压力

3. 缓存预热

   缓存预热就是系统上线后，将相关的缓存数据直接加载到缓存系统。这样就可以避免在用户请求的时候，先查询数据库，然后再将数据缓存的问题。用户直接查询事先被预热的缓存数据。

   - 数据量不大的时候，工程启动的时候进行加载缓存动作；
   - 数据量大的时候，设置一个定时任务脚本，进行缓存的刷新；
   3. 数据量太大的时候，优先保证热点数据进行提前加载到缓存。

4. 缓存降级

   降级的情况，就是缓存失效或者缓存服务挂掉的情况下，我们也不去访问数据库。我们直接访问内存部分数据缓存或者直接返回默认数据	

### 分布式锁

当多个进程不在同一个系统中，用分布式锁控制多个进程对资源的操作或者访问。 与之对应有线程锁，进程锁。

分布式锁可以避免不同进程重复相同的工作，减少资源浪费。 同时分布式锁可以避免破坏数据正确性的发生， 例如多个进程对同一个订单操作，可能导致订单状态错误覆盖。应用场景如下。

分布式实现方式有很多种：

1. 数据库乐观锁方式

2. 基于 Redis 的分布式锁

3. 基于 ZK 的分布式锁

咱们这篇文章主要是讲 Redis，那么我们重点介绍基于 Redis 如何实现分布式锁。

分布式锁实现要保证几个基本点。

1. 互斥性：任意时刻，只有一个资源能够获取到锁。
2. 容灾性：能够在未成功释放锁的的情况下，一定时限内能够恢复锁的正常功能。
3. 统一性：加锁和解锁保证同一资源来进行操作。

```java
public static boolean tryGetDistributedLock(Jedis jedis,String lockKey,String traceId,int expireTime) {

    SetParams setParams=new SetParams();

    setParams.ex(expireTime);

    setParams.nx();

    String result=jedis.set(lockKey,traceId,setParams);

    if(LOCK_SUCCESS.equals(result)) {

        return true;

    }

    return false;
```

#### 分布式自增id

**应用场景**

随着用户以及交易量的增加， 我们可能会针对用户数据，商品数据，以及订单数据进行分库分表

的操作。这时候由于进行了分库分表的行为，所以 MySQL 自增 ID 的形式来唯一表示一行数据的方案不可行了。 因此需要一个分布式 ID 生成器，来提供唯一 ID 的信息。

**实现方式**

通常对于分布式自增 ID 的实现方式有下面几种：

1. 利用数据库自增 ID 的属性

2. 通过 UUID 来实现唯一 ID 生成

3. Twitter 的 SnowFlake 算法

4. 利用 Redis 生成唯一 ID

在这里我们自然是说 Redis 来实现唯一 ID 的形式了。使用 Redis 的 INCR 命令来实现唯一ID。

Redis 是单进程单线程架构，不会因为多个取号方的 INCR 命令导致取号重复。因此，基于 Redis

的 INCR 命令实现序列号的生成基本能满足全局唯一与单调递增的特性。

### Redis-sentinel

Redis 提供的 sentinel（哨兵）机制，通过 sentinel 模式启动redis后，自动监控 Master/Slave

的运行状态，基本原理是：心跳机制 + 投票裁决。

简单来说，哨兵的作用就是监控 Redis 系统的运行状况。它的功能包括以下两个：

1. 监控主数据库和从数据库是否正常运行；

2. 主数据库出现故障时自动将从数据库转换为主数据库。

哨兵模式主要有下面几个内容：

监控（ Monitoring ）：Sentinel 会定期检查主从服务器是否处于正常工作状态。

提醒（ Notification ）：当被监控的某个 Redis 服务器出现异常时，Sentinel 可以通过 API 向管理员或者其他应用程序发送通知。 

自动故障迁移（Automatic failover）：当一个主服务器不能正常工作时，Sentinel 会开 始一次自动故障迁移操作，它会将失效主服务器的其中一个从服务器升级为新的主服务器， 并让失效主服务器的其他从服务器改为复制新的主服务器；当客户端试图连接失效的主服务 器时，集群也会向客户端返回新主服务器的地址， 使得集群可以使用新主服务器代替失效 服务器。

优点

1. 哨兵模式主从可以切换，具备基本的故障转移能力；

2. 哨兵模式具备主从模式的所有优点。

缺点

1. 哨兵模式也很难支持在线扩容操作；

2. 集群的配置信息管理比较复杂。

![](https://cdn.jsdelivr.net/gh/Nobodylesszb/upic@pic/upload/pics/1574082336-image-20191103093250163.png)

#### 客户端连接sentinel

![](https://cdn.jsdelivr.net/gh/Nobodylesszb/upic@pic/upload/pics/1574082386-image-20191103103554305.png)



### cluster集群模式

Redis Cluster 是一种服务器 Sharding 技术，3.0 版本开始正式提供。采用无中心结构，每个节

点保存数据和整个集群状态,每个节点都和其他所有节点连接。如图所示：

![](https://cdn.jsdelivr.net/gh/Nobodylesszb/upic@pic/upload/pics/1574083782-GPZIYt.png)

Cluster 集群结构特点：

1. Redis Cluster 所有的物理节点都映射到 [ 0-16383 ] slot 上（不一定均匀分布），Cluster 负责维护节点、桶、值之间的关系；

2. 在 Redis 集群中放置一个 key-value 时，根据 CRC16(key) mod 16384 的值，从之前 划分的 16384 个桶中选择一个；

3. 所有的 Redis 节点彼此互联（PING-PONG 机制），内部使用二进制协议优化传输效率；

4. 超过半数的节点检测到某个节点失效时则判定该节点失效；

5. 使用端与 Redis 节点链接,不需要中间 proxy 层，直接可以操作，使用端不需要连接集群 所有节点，连接集群中任何一个可用节点即可。

优点

1. 无中心架构，节点间数据共享，可动态调整数据分布；

2. 节点可动态添加删除，扩展性比较灵活；

3. 部分节点异常，不影响整体集群的可用性。

缺点

1. 集群实现比较复杂；

2. 批量操作指令（ mget、mset 等）支持有限；

3. 事务操作支持有限。

### 如何保证缓存与数据库双写时的数据一致性







### 







