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
* 主从复制的流程：使用异步复制。1.从服务器通过psync(partial synchronization)命令发送同步源id，同步进度offset。2.master收到请求，如果同步源是当前master，则根据偏移量增量同步。3.如果同步源非当前master，则进入全量同步：master生成rdb,传给slave。
* 一个master可以拥有多个slave。
* slave可以接受其他slave的连接，slave可以有下级sub slave。
* 主从同步过程在master侧是非阻塞的。
* 主从复制的应用场景：读写分离
### 哨兵
在主从复制基础之上加入了 监控、提醒、故障转移(主从切换)，这些功能都由哨兵来完成。健康检查：哨兵给redis服务器发送ping命令，如果一段时间内没有收到回应则认为下线了。故障切换流程：
* 1.哨兵一旦发现master不能正常提供服务，会通知其他哨兵
* 2.当一定数量的哨兵都认为master挂了
* 3.选举一个哨兵作为故障转移的执行者
* 4.执行者在slave中选一个作为新的master
* 5.将其他slave设置为新master的从属
#### 七大核心概念
* 哨兵如何知道redis的主从信息：哨兵配置文件中需要配置master的信息，知道master信息后就可以通过info replication这个命令进行主从信息的自动发现。
* 什么是master主观下线：哨兵给master发送的ping命令如果一段时间内没有得到回复，则哨兵会主观的(单方面地)认为master已经不可用了，对应的配置：sentinel down-after-milliseconds mymaster 1000
* 客观下线：一定数量的哨兵认为master已经下线。检测机制：当哨兵主观认为master下线后，会通过sentinel is-master-down-by-addr命令询问其他哨兵是否认为master已经下线，如果一定数量的哨兵认为master已经下线，就会认为master客观下线，开始故障转移。对应的配置：sentinel monitor mymaster 192.168.100.241 6380 2
* 哨兵之间如何通信：1.哨兵通过订阅master的__sentinel__:hello这个通道进行自动发现。2.哨兵之间直接发送命令通信。3.哨兵之间通过订阅发布进行通信。
* 哨兵领导的选举机制：
