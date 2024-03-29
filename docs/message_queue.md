## kafka丢消息

- 生产者情况：
    - 生产者采用push模式将数据发布到broker，每条消息追加到分区中，顺序写入磁盘。消息写入Leader后，Follow是主动与Leader进行同步。
    - Kafka消息发送有两种方式：同步（sync）和异步（async），默认同步，可通过producer.type属性进行配置。

- 消费者情况：
    - 先 commit 再处理消息。如果在处理消息的时候异常了，但是 offset 已经提交了，这条消息对于该消费者来说就是丢失了，再也不会消费到了。
    - 先处理消息再 commit。如果在 commit之前发生异常，下次还会消费到该消息，重复消费的问题可以通过业务保证消息幂等性来解决。

- broker本身情况：
    - Kafka通过多分区多副本的机制已经能最大限度保证数据不会丢失，如果数据已经写入系统cache中但是没来得及刷入磁盘，此时突然宕机或者掉电那就丢了，此情况很极端。