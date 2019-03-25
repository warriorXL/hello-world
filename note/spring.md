# spring

## spring消息

JMS(Java Message Service),Java消息服务(消息模型:点对点和发布-订阅)

AMQP(Advanced Message Queuing Protocol),高级消息队列协议

![1544421246586](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1544421246586.png)

AMQP Exchange(四种标准):

​	Direct、Topic、Headers、Fanout

![1544421756060](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1544421756060.png)

![1544421781606](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1544421781606.png)

消息代理(Message Broker)、目的地(destination)

消息驱动bean(message driven brean,MDB)

RabbitMQ(开源消息代理,实现了AMQP)

![1544424299338](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1544424299338.png)

## websocket(全双工的通信通道)

两个应用之间通信的通道.位于Websocket一端的应用发送消息,另外一段处理消息,因为他是全双工的,所以每一端都可以是发送和处理消息.

![1544429990161](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1544429990161.png)

## SocketJS(Websocket技术的一种模拟)

![1544430123449](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1544430123449.png)

## STOMP

Simple Text Oriented Messaging Protocol(STOMP),STOMP代理是基于内存的.