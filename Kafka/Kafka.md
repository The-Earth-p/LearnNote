# Kafka

线程间的数据交互，如果两边处理速度不一样，就会导致数据堆积，进程也是如此，严重甚至导致系统不可用。种种原因，归根于资源不足，耦合性过高，因此引入了一个第三方软件，作为数据缓冲区，从而降低耦合性，也就是消息中间件，kafka就是这样一个消息中间件。

MQ软件，也叫Java message service（JMS）provider，还有consumer和producer。有一个p2p模型，消息只能消费一次，每次消费有反馈；还有一个模型叫PS模型，它里面有个top部分，里面存着数据，可以被多个消费者消费

## 组件

record：就是消息

kafka broker：中介，就是中间件

kafka consumer：消费者

kafka producer：生产者

.log文件：存数据的一个文件

topic：保存事件的文件夹，数据可以标号，叫偏移量，根据这个可以访问

## 主题创建

先启动zookeeper，在启动kafka

`kafka-topic.bat` 这个脚本启动主题
