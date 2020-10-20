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
