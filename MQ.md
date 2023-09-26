# MQ

---

### 1. 概述

##### 1.1 用途

* 限流削峰

* 异步解耦

* 数据收集

---

### 2. RocketMQ

##### 2.1 消息的生产

###### 2.1.1 消息的生产过程

Producer可以将消息写入到某Broker中的某Queue中，其经历了如下过程：

* Producer发送消息之前，会先向NameServer发出获取消息Topic的路由信息的请求

* NameServer返回该Topic的路由表及Broker列表。

* Producer根据代码中指定的Queue选择策略，从Queue列表中选出一个队列，用于后续存储消息。

* Produer对消息做一些特殊处理，例如，消息本身超过4M，则会对其进行压缩。

* Producer向选择出的Queue所在的Broker发出RPC请求，将消息发送到选择出的Queue。

> 路由表: 实际是一个Map，key为Topic名称，value是一QueueData实例列表。QueueData并不是一个Queue对应一个QueueData，而是一个Broker中该Topic的所有Queue对应一QueueData。
> 
> Broker列表：其实际也是一个Map，key为brokerName，value为BrokerData。一个Broker对应一个BrokerData实例(一套BrokerName相同的broker，主从)。

###### 2.1.2 Queue选择算法

对于无序消息，其Queue选择算法，也称为消息投递算法，常见的有两种:

* 轮询算法：
  默认选择算法。该算法保证了每个Queue中可以均匀的获取到消息。

* 最小投递延迟算法
  该算法会统计每次消息投递的时间延迟，然后根据统计出的结果将消息投递到时间延迟最小的Queue。如果延迟相同，则采用轮询算法投递。

##### 2.2 消息的存储

RocketMQ中的消息存储在本地文件系统中，这些相关文件默认在当前用户主目录下的store目录中。

store目录下结构：

* abort: 该文件在Broker启动后会自动创建，正常关闭Broker，该文件会自动消失。若在没有启动Broker的情况下，发现这个文件是存在的，则说明之前Broker的关闭是非正常关闭。

* checkpoint：其中存储着commitlog、consumequeue、index文件的最后刷盘时间戳。

* commitlog：其中存放着commitlog文件，而消息是写在commitlog文件中的。

* config：存放着Broker运行期间的一些配置数据。

* consumequeue: 其中存放着consumequeue文件，队列就存放在这个目录中。

* index：其中存放着消息索引文件indexFile。

* lock：运行期间使用到的全局资源锁。

---

### 3. Kafka
