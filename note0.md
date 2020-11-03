### 发布与订阅机制相关命令
pubsub channels; pubsub numsub sms_send;

1.监控redis所执行的命令: monitor

2.key失效后的事件通知机制
### 持久化方式
#### RDB方式：
通过BGSAVE 或 SAVE来创建一个内存快照。优点：恢复数据快，对性能影响小。缺点：同步是有丢失数据的风险。
#### AOF方式：
一种增量备份的方式，默认不开启，开启配置：appendonly yes。

AOF策略配置：appendfsync always(每次有数据修改时都会写入AOF文件)，appendfsync everysec(每秒同步一次，默认策略)，appendfsync no(不同步)。优点：安全。缺点：文件体积大，性能消耗比RDB高，恢复数据比RDB慢。
### 内存分配
1. String: 一个String类型的value最大可存512M。Lists类型：list元素个数最多为2^32-1。 Sets类型：元素个数最多为2^32-1。Hashes类型：键值对个数最多为2^32-1。
2. 最大内存控制：maxmemory(最大内存阈值)   maxmemory-policy(达到最大内存阈值后的执行策略)
3. 过期数据的处理策略：主动处理(redis主动触发检测key是否过期)， 被动处理(访问key的时候发现已过期，清除)
### 内存回收策略
1. 配置文件中设置：maxmemory-policy:回收策略 ，动态调整：config set maxmemory-policy:回收策略
2. 回收策略, noeviction：不设置回收策略，达到阈值则报错，allkeys-lru：对所有key都执行LRU算法，volatile-lru：对所有已过期的key执行LRU，allkeys-lfu，volatile-lfu，allkeys-random，volatile-random，volatile-ttl。
3. 热点数据分析功能：redis-cli --hotkeys
### 主从复制
主redis以普通模式启动，从服务器的启动方式为：1.命令行slaveof ip port。 2.redis.conf配置文件中加 slaveof ip port, slave-read-only yes(从服务器是否只读，默认yes)。3.退出主从集群的方式：slaveof no one；查看主从状态: info replication
