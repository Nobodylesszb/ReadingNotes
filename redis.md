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
- ![image-20191031211101476](/Users/bo/Library/Application Support/typora-user-images/image-20191031211101476.png)
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

![image-20191031212756871](/Users/bo/Library/Application Support/typora-user-images/image-20191031212756871.png)

![image-20191031212910830](/Users/bo/Library/Application Support/typora-user-images/image-20191031212910830.png)

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
- ![image-20191031214409798](/Users/bo/Library/Application Support/typora-user-images/image-20191031214409798.png)

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

### Redis持久化的取舍和选择

#### rdb

- 触发机制
  - sava 同步 阻塞 复杂度n
  - bgsave 异步 fork子进程
  - 自动
- ![image-20191031225903551](/Users/bo/Library/Application Support/typora-user-images/image-20191031225903551.png)
- ![image-20191031230608806](/Users/bo/Library/Application Support/typora-user-images/image-20191031230608806.png)
- 全量复制

- debug rekiad
- Shutdown 这些都会产生rdb

![image-20191031235039478](/Users/bo/Library/Application Support/typora-user-images/image-20191031235039478.png)

#### AOF

- rdb问题
  - 耗时，耗性能
  - ![image-20191101221025164](/Users/bo/Library/Application Support/typora-user-images/image-20191101221025164.png)
- 原理
  - 写日志模式
- 三种策略
  - always
    - ![image-20191101221235930](/Users/bo/Library/Application Support/typora-user-images/image-20191101221235930.png)
  - everysec
    - ![image-20191101221253532](/Users/bo/Library/Application Support/typora-user-images/image-20191101221253532.png)
  - no
    - ![image-20191101221337060](/Users/bo/Library/Application Support/typora-user-images/image-20191101221337060.png)
  - ![image-20191101221428426](/Users/bo/Library/Application Support/typora-user-images/image-20191101221428426.png)
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
    - ![image-20191101223153486](/Users/bo/Library/Application Support/typora-user-images/image-20191101223153486.png)

#### RDB和aof抉择

- ![image-20191101230707741](/Users/bo/Library/Application Support/typora-user-images/image-20191101230707741.png)
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

<img src="/Users/bo/Library/Application Support/typora-user-images/image-20191102205609558.png" alt="image-20191102205609558" style="zoom:50%;" />

**子进程优化**

![image-20191102205845704](/Users/bo/Library/Application Support/typora-user-images/image-20191102205845704.png)

**aof追加**

![image-20191102210230275](/Users/bo/Documents/notes/life/image-20191102210230275.png)

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

![image-20191102221832531](/Users/bo/Library/Application Support/typora-user-images/image-20191102221832531.png)

**开销**

- bgsave时间
- rdb文件网络传输时间
- 从节点清除数据时间
- 从节点加载rdb的时间
- 可能的aof重写时间

#### 部分复制

![image-20191102222157245](/Users/bo/Library/Application Support/typora-user-images/image-20191102222157245.png)

#### **故障处理**

- slave宕机
- ![image-20191102222516833](/Users/bo/Library/Application Support/typora-user-images/image-20191102222516833.png)
- master宕机
- ![image-20191102222612720](/Users/bo/Library/Application Support/typora-user-images/image-20191102222612720.png)

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

### Redis-sentinel

![image-20191103093250163](/Users/bo/Library/Application Support/typora-user-images/image-20191103093250163.png)

#### 客户端连接sentinel

![image-20191103103554305](/Users/bo/Library/Application Support/typora-user-images/image-20191103103554305.png)











### 







