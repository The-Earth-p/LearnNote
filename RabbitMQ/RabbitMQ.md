# RabbitMQ

## 内部体系结构

消息发送端：Producer

消息接收端：Consumer

连接：Connection，其中会包含channel，是对connection的复用，连接会在服务器实例和发送端以及接收端分别建立

服务器实例：Broker，该实例中可以划分逻辑分组，称为virtual host，可以有很多个host，里面会包含exchange组件，也就是交换机可以绑定多个队列；有队列queue，是消息的容器，里面存放消息，队列和通道channel相连。

## 工作模式

1.Work Queues 消息发到队列里边儿，多个消费者可以拿，但只能拿一次

生产者只有一个，能发送多个消息；有多个消费者，对同一个信息，互相之间竞争关系，

2.Publish/Subscribe 发布订阅模式，消息发送到中间件的交换机上，交换机可以把消息发送到每一个队列上

3.Routing 交换机可以通过路由键来确定信息去哪个队列

4.Topic 通过通配符来匹配一个或多个路由键