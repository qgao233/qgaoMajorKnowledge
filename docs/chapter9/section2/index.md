# 数据类型

## 1 string:
expire key  60 # 数据在 60s 后过期

setex key 60 value # 数据在 60s 后过期（该命令字符串类型独有）

strlen key # 返回 key 所储存的字符串值的长度。

场景：计数

## 2 list:
rpush,lpop,lpush,rpop,lrange,llen

场景：消息队列

## 3 hash:
hset,hmset,hexists,hget,hgetall,hkeys,hvals

场景：对象

## 4 set:
sadd,spop,smembers,sismember,scard,sinterstore,sunion

scard mySet # 查看 set 的长度

sinterstore mySet3 mySet mySet2 # 获取 mySet 和 mySet2 的交集并存放在 mySet3 中

场景：求交集或并集

## 5 sorted set
相较于set增加了一个权重参数 score:

zadd,zcard,zscore,zrange,zrevrange,zrem

zadd myZset 3.0 value1 # 添加元素到 sorted set 中 3.0 为权重

zscore myZset value1 # 查看某个 value 的权重

zrevrange  myZset 0 1 # 逆序输出某个范围区间的元素，0 为 start  1 为 stop

场景：排行榜

## 6 bitmap:
setbit 、getbit 、bitcount、bitop

setbit mykey 7 1 # 生成7位（这7位全部默认为0），并且设置第7位为1

setbit mykey 8 1 # 增加第8位，设置第8位为1

bitcount mykey   # 统计被被设置为 1 的位的数量,return 2;

bitop (and/or/not/xor) destkey key1 key2 [key3 ...]
>and/or/not/xor是后面的key1,key2之间的位运算操作符，将结果保存进destkey

场景：统计

## 7 Stream

Redis 5.0 新增加的一个数据结构 Stream 可以用来做消息队列，Stream 支持：

发布 / 订阅模式
按照消费者组进行消费
消息持久化（ RDB 和 AOF）
不过，和专业的消息队列相比，还是有很多欠缺的地方比如消息丢失和堆积问题不好解决。

我们通常建议是不需要使用 Redis 来做消息队列的，你完全可以选择市面上比较成熟的一些消息队列比如 RocketMQ、Kafka。

## 8 zset

底层跳表实现的，

跳表先从第⼀层查找，不满⾜就下沉到第⼆层找，因为每⼀层都是有序的，写⼊和插⼊的时间复杂度都是O(logN)

zset为什么不⽤红⿊树(跳表实现简单，踩坑成本低，红⿊树每次插⼊都要通过旋转以维持平衡，实现复杂)

>红⿊树: N叉平衡树，O(logN)