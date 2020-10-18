### 发布与订阅机制相关命令
pubsub channels; pubsub numsub sms_send;

1.监控redis所执行的命令: monitor

2.key失效后的事件通知机制

### 持久化方式
RDB方式：通过BGSAVE 或 SAVE来创建一个内存快照。优点：恢复数据快，对性能影响小。缺点：同步是有丢失数据的风险。
AOF方式：
