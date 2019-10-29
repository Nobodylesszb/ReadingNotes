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



