# RabbitMQ

![](http://www.rabbitmq.com/img/rabbitmq_logo_strap.png)

## 基础

### RabbitMQ基本介绍

**AMQP**：Advanced Message Queuing Protocol（**高级消息队列协议**），是一个网络协议，是应用层协议的一个开放标准，为面向消息的中间件设计

基于此协议的客户端与消息中间件可以传递消息，不受客户端或者中间件不同产品，不同开发语言的限制。2006年发布。可以类比于Http协议

模型：

```txt
Publisher ===Publish===> Exchange(交换机) ===Routes(路由)===> Queue ===Consumes===> Consumer
```



Rabbit技术公司2007年基于AMQP协议，采用Erlang语言开发RabbitMQ

[RabbitMQ官网](https://www.rabbitmq.com)



**RabbitMQ架构**

![](https://img2020.cnblogs.com/blog/1552936/202010/1552936-20201024103921637-693350551.png)

* **Broker**：接收和分发消息的应用，RabbitMQ Server就是Message Broker
* **Virtual host**：处于多租户和安全因素设计。把AMQP的基本组件划分到一个虚拟的分组里，类似于网络中的namespace概念。当多个不同的用户使用同一个RabbitMQ Server提供的服务时，可以划分出多个Virtual host，每个用户可以在自己的Virtual host创建exchange和queue等
* **Connection**：publisher，consumer和broker之间的TCP连接
* **Channel**：如果每一次访问RabbitMQ都建立一个Connection，在消息量大的时候建立TCP Connection时的开销是巨大的，效率也很低。Channel是在connection内部建立的逻辑连接没如果应用程序支持多线程，通常每个thread都会创建单独的channel进行通讯，AMQP method包含了channel id，帮助客户端和message broker识别channel，所以channel之间是完全隔离的。channel作为轻量级的connection极大地减少了操作系统建立TCP Connection的开销
* **Exchange**：message到达broker的第一站，根据分发规则，匹配查询表中的routing key，分发消息到queue中去。常用的类型有direct(point-to-point)，topic(publish-subscribe订阅)，fanout(multicast多播)
* **Queue**：消息最终被送到这里等待消费者consumer取走
* **Binding**：exchange和queue之间的虚拟连接，binding中可以包含routing key。binding信息可以被保存到exchange中的查询表中，用于message的分发依据



**RabbitMQ提供了六种工作模式**

* 简单模式
* work queues
* publish/subscribe（发布与订阅模式）
* routing（路由模式）
* topics（主题模式）
* RPC远程调用模式（远程调用，不太算MQ，不做要求）



**JMS**：Java Message Service（**Java消息服务**）应用程序**接口**，是一个Java平台中关于面向消息中间件的API。是JavaEE规范的一种，类比于JDBC。很多消息中间件都实现了JMS规范，但RabbitMQ没有。但RabbitMQ的开源社区里有大佬提供了JMS实现包

------



### RabbitMQ安装配置

**Linux版本**

* 安装依赖环境

  ```shell
  yum install build-essential openssl openssl-devel unixODBC unixODBC-devel make gcc gcc-c++ kernel-devel m4 ncurses-devel tk tc xz
  ```

* 安装Erlang

  下载好【erlang-18.3-1.el7.centos.x86_64.rpm】到对应目录

  ```shell
  rpm -ivh erlang-18.3-1.el7.centos.x86_64.rpm
  ```

* 安装环境

  下载好【socat-1.7.3.2-1.1.el7.x86_64.rpm】到对应目录

  ```shell
  rpm -ivh socat-1.7.3.2-1.1.el7.x86_64.rpm
  ```

* 安装RabbitMQ

  下载好【rabbitmq-server-3.6.5-1.noarch.rpm】到对应目录

  ```shell
  rpm -ivh rabbitmq-server-3.6.5-1.noarch.rpm
  ```



**服务启动**

```shell
service rabbitmq-server start     #启动服务
service rabbitmq-server stop      #停止服务
service rabbitmq-server restart   #重启服务
```



**开启管理界面和配置**

```shell
rabbitmq-plugins enable rabbitmq_management    			 		  #开启管理界面
vim /usr/lib/rabbitmq/lib/rabbitmq_server-3.6.5/ebin/rabbit.app   #修改默认配置信息 - 修改完需要重启服务生效

#修改内容
-> 将 {loopback_users, [<<"guest">>]} 改为 {loopback_users, [guest]} 即可，代表默认用户为guest

#防火墙开放端口
默认服务端口为：5672
浏览器管理界面访问端口为：15672

#放置配置文件 - 设置完需要重启服务
cd /usr/share/doc/rabbitmq-server-3.6.5/    					#默认安装路径
cp ./rabbitmq.config.example /etc/rabbitmq/rabbitmq.config		#复制样本配置文件到所需目录下，并改名为rabbitmq.config
```

------



### RabbitMQ入门程序

需求：使用**简单模式**完成消息传递

步骤

1. 创建工程（生产者，消费者）
2. 分别添加依赖
3. 编写生产者发送消息
4. 编写消费者接收消息



**简单模式**

![](https://www.rabbitmq.com/img/tutorials/python-one.png)

1. 消息的生产者producer将消息放入队列
2. 消息的消费者(consumer) 监听(while) 消息队列,如果队列中有消息,就消费掉,消息被拿走后,自动从队列中删除(隐患 消息可能没有被消费者正确处理,已经从队列中消失了,造成消息的丢失)

应用场景：聊天(中间有一个过度的服务器;p端,c端)



生产者

```java
public class Producer {
    public static void main(String[] args) throws Exception {
        //简单模式 - 无需设置Exchange交换机
        //1. 创建连接工厂
        ConnectionFactory connectionFactory = new ConnectionFactory();
        //2. 设置参数
        connectionFactory.setHost("192.168.xx.xx");//ip       默认值：localhost
        connectionFactory.setPort(5672);           //端口     默认值：5672
        connectionFactory.setVirtualHost("/wyh");  //虚拟机  默认值：/
        connectionFactory.setUsername("wyh");      //用户名 默认值：guest
        connectionFactory.setPassword("wyh");      //密码  默认值：guest
        //3. 创建Connection
        Connection connection = connectionFactory.newConnection();
        //4. 创建Channel
        Channel channel = connection.createChannel();
        //5. 创建Queue
        /*
         * queueDeclare方法参数：
         * 1. queue：队列名称 - String
         * 2. durable 是否持久化，MQ重启之后还会存在 - boolean
         * 3. exclusive： - boolean
         *              ①是否独占，只能有一个消费者监听这队列
         *              ②当Connection关闭时，是否删除队列
         * 4. autoDelete：是否自动删除，当没有Consumer时，会自动删除掉 - boolean
         * 5. arguments：参数信息，例如如何删除队列信息 - Map
         */
        //如果没有名为HelloWorld的队列，那么会自动创建，有则直接使用
        channel.queueDeclare("HelloWorld",true,false,false,null);
        //6. 发送消息到Queue
        /*
         * basicPublish方法参数：
         * 1. exchange：交换机名称，简单模式下使用默认交换机("")
         * 2. routingKey：路由名称，简单模式下需要和队列名称一致才可以找到对应队列
         * 3. props：配置信息
         * 4. body：要发送的消息数据 - byte[]
         */
        channel.basicPublish("","HelloWorld",null,"WangYihuan Will Change The World!".getBytes());
        //7. 释放资源
        channel.close();
        connection.close();
    }
}
```



消费者

```java
public class Consumer {
    public static void main(String[] args) throws Exception {
        //简单模式 - 无需设置Exchange交换机
        //1. 创建连接工厂
        ConnectionFactory connectionFactory = new ConnectionFactory();
        //2. 设置参数
        connectionFactory.setHost("192.168.xx.xx");//ip       默认值：localhost
        connectionFactory.setPort(5672);           //端口     默认值：5672
        connectionFactory.setVirtualHost("/wyh");  //虚拟机  默认值：/
        connectionFactory.setUsername("wyh");      //用户名 默认值：guest
        connectionFactory.setPassword("wyh");      //密码  默认值：guest
        //3. 创建Connection
        Connection connection = connectionFactory.newConnection();
        //4. 创建Channel
        Channel channel = connection.createChannel();
        //5. 从Queue接收消息
        /*
         * basicConsume方法参数：
         * 1. queue：队列名称
         * 2. autoAck：是否自动确认,消费者收到消息后自动确认
         * 3. callback：回调对象 - Consumer接口 - DefaultConsumer实现类 - 回调方法：handleDelivery
         *            -> 回调方法：handleDelivery，当接收到消息后自动执行的方法
         *               参数：
         *                  1. consumerTag：标识
         *                  2. envelope：获取一些信息，比如交换机，路由key等
         *                  3. properties：配置信息
         *                  4. body：从Queue获取的消息数据
         */
        com.rabbitmq.client.Consumer consumer = new DefaultConsumer(channel) {
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                System.out.println("consumerTag: " + consumerTag);
                System.out.println("exchange: " + envelope.getExchange());
                System.out.println("routingKey: " + envelope.getRoutingKey());
                System.out.println("body: " + new String(body));
            }
        };
        channel.basicConsume("HelloWorld",true,consumer);
        //6. 不要释放资源，消费者相当于是监听队列中是否存在消息，存在就获取来消费掉
    }
}
```

------



### RabbitMQ工作模式

**工作队列模式：Work Queues**

![](https://www.rabbitmq.com/img/tutorials/python-two.png)

与简单模式相比，多了一个或一些消费端，多个消费端共同消费同一个队列中的消息

1. 消息的生产者将消息放入队列，消费者可以有多个，同时监听同一个队列，C1 C2共同争抢当前的消息队列内容，谁先拿到谁负责消费消息(隐患,高并发情况下，默认会产生某一个消息被多个消费者共同使用，可以设置一个开关(syncronize,与同步锁的性能不一样) 保证一条消息只能被一个消费者使用)
2. 应用场景:抢红包；大项目中的资源调度(任务分配系统不需知道哪一个任务执行系统在空闲,直接将任务扔到消息队列中,空闲的系统自动争抢)；对于任务过重或任务较多的情况下使用，可以提高任务处理速度

消费者之间为**竞争**关系

演示案例与简单模式相比改动不大，更改了对应的队列名称-WorkQueues（创建了一个新的队列）且消费者启动了多个实例，同时竞争消费生产者产生的消息



**订阅模式：Pub/Sub**

![](https://www.rabbitmq.com/img/tutorials/python-three.png)

在这个模式中，多了一个**Exchange**交换机角色

1. P，生产者，发送消息到X（交换机）

2. X，交换机，接收生产者发送的消息。另外，还知道如何处理消息，例如递交给某个特别的消息队列，或者递交给所有的消息队列。这样的操作取决于交换机的类型

   交换机的类型：

   * **Fanout**：广播，将消息交给所有绑定到交换机的队列
   * **Direct**：定向，将消息交给符合指定RoutingKey的消息队列
   * **Topic**：通配符，将消息交给符合RoutingPattern（路由模式）的消息队列

   **Exchange交换机只负责转发消息，不具备存储消息的能力**，如果没有任何消息队列和交换机绑定，那么消息就会丢失

3. Queue，消息队列，接收并缓存消息

4. C，消费者，从消息队列获取消息消费

5. 应用场景：邮件群发，群聊天，广播(广告)

这是一个**共享资源**模式，**消费者监听自己的消息队列**

演示案例

Producer

```java
public class Producer {
    public static void main(String[] args) throws Exception {
        //订阅模式 - 需要设置Exchange交换机
        //1. 创建连接工厂
        ConnectionFactory connectionFactory = new ConnectionFactory();
        //2. 设置参数
        connectionFactory.setHost("192.168.xx.xx");//ip       默认值：localhost
        connectionFactory.setPort(5672);           //端口     默认值：5672
        connectionFactory.setVirtualHost("/wyh");  //虚拟机  默认值：/
        connectionFactory.setUsername("wyh");      //用户名 默认值：guest
        connectionFactory.setPassword("wyh");      //密码  默认值：guest
        //3. 创建Connection
        Connection connection = connectionFactory.newConnection();
        //4. 创建Channel
        Channel channel = connection.createChannel();
        //5. 创建Exchange - 交换机
        /*
         * exchangeDeclare方法参数：
         * 1. exchange：交换机名称 - String
         * 2. type：交换机类型 - BuiltinExchangeType枚举
         *                  ① DIRECT：定向
         *                  ② FANOUT：广播
         *                  ③ TOPIC： 通配符
         *                  ④ HEADERS：参数匹配
         * 3. durable 是否持久化 - boolean
         * 4. autoDelete：是否自动删除 - boolean
         * 5. internal：是否内部使用 - boolean - 一般为false
         * 6. arguments：参数 - Map
         */
        channel.exchangeDeclare("wyh-fanout", BuiltinExchangeType.FANOUT,true,false,false,null);
        //6. 创建多个Queue
        /*
         * queueDeclare方法参数：
         * 1. queue：队列名称 - String
         * 2. durable 是否持久化，MQ重启之后还会存在 - boolean
         * 3. exclusive： - boolean
         *              ①是否独占，只能有一个消费者监听这队列
         *              ②当Connection关闭时，是否删除队列
         * 4. autoDelete：是否自动删除，当没有Consumer时，会自动删除掉 - boolean
         * 5. arguments：参数信息，例如如何删除队列信息 - Map
         */
        //如果没有名为wyh-fanout-queue-1 / wyh-fanout-queue-2的队列，那么会自动创建，有则直接使用
        channel.queueDeclare("wyh-fanout-queue-1",true,false,false,null);
        channel.queueDeclare("wyh-fanout-queue-2",true,false,false,null);
        //7. 绑定队列和交换机 - Binding
        /*
         * queueBind方法参数：
         * 1. queue：队列名称
         * 2. exchange：交换机名称
         * 3. routingKey：路由键，绑定规则
         *              - 当交换机为Fanout广播类型时，不需要指定routingKey，设置为("")
         */
        channel.queueBind("wyh-fanout-queue-1","wyh-fanout","");
        channel.queueBind("wyh-fanout-queue-2","wyh-fanout","");
        //8. 发送消息到Exchange
        /*
         * basicPublish方法参数：
         * 1. exchange：交换机名称，简单模式下使用默认交换机("")
         * 2. routingKey：路由名称，简单模式下需要和队列名称一致才可以找到对应队列
         * 3. props：配置信息
         * 4. body：要发送的消息数据 - byte[]
         */
        channel.basicPublish("wyh-fanout","",null,"WangYihuan Will Change The World!".getBytes());
        //9. 释放资源
        channel.close();
        connection.close();
    }
}
```

Consumer

```java
public class Consumer {
    public static void main(String[] args) throws Exception {
        //订阅模式 - 消费者不需要设置Exchange交换机 - 只需要连接指定队列
        //1. 创建连接工厂
        ConnectionFactory connectionFactory = new ConnectionFactory();
        //2. 设置参数
        connectionFactory.setHost("192.168.xx.xx");//ip       默认值：localhost
        connectionFactory.setPort(5672);           //端口     默认值：5672
        connectionFactory.setVirtualHost("/wyh");  //虚拟机  默认值：/
        connectionFactory.setUsername("wyh");      //用户名 默认值：guest
        connectionFactory.setPassword("wyh");      //密码  默认值：guest
        //3. 创建Connection
        Connection connection = connectionFactory.newConnection();
        //4. 创建Channel
        Channel channel = connection.createChannel();
        //5. 从Queue接收消息
        /*
         * basicConsume方法参数：
         * 1. queue：队列名称
         * 2. autoAck：是否自动确认,消费者收到消息后自动确认
         * 3. callback：回调对象 - Consumer接口 - DefaultConsumer实现类 - 回调方法：handleDelivery
         *            -> 回调方法：handleDelivery，当接收到消息后自动执行的方法
         *               参数：
         *                  1. consumerTag：标识
         *                  2. envelope：获取一些信息，比如交换机，路由key等
         *                  3. properties：配置信息
         *                  4. body：从Queue获取的消息数据
         */
        com.rabbitmq.client.Consumer consumer = new DefaultConsumer(channel) {
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                System.out.println("消费者1已获取消息,将把消息写入日志并存入数据库");
                System.out.println("消息: " + new String(body));
            }
        };
        channel.basicConsume("wyh-fanout-queue-1",true,consumer);
        //6. 不要释放资源，消费者相当于是监听队列中是否存在消息，存在就获取来消费掉
    }
}
```



**路由模式：Routing**

![](https://www.rabbitmq.com/img/tutorials/python-four.png)

1. 交换机类型为Direct
2. 队列和交换机**绑定需要指定RoutingKey**
3. 生产者向交换机**发送消息时也要指定消息的RoutingKey**
4. **交换机根据消息的RoutingKey发送消息**到相应绑定的消息队列
5. 消费者监听的队列接收到的消息需要满足所在队列与交换机绑定的RoutingKey
6. 应用场景：error 通知；EXCEPTION；传统意义的错误通知；客户通知；利用key路由,可以将程序中的错误封装成消息传入到消息队列中,开发者可以自定义消费者,实时接收错误

演示案例

Producer

```java
public class Producer {
    public static void main(String[] args) throws Exception {
        //路由模式 - 需要设置Exchange交换机
        //1. 创建连接工厂
        ConnectionFactory connectionFactory = new ConnectionFactory();
        //2. 设置参数
        connectionFactory.setHost("192.168.88.92");//ip       默认值：localhost
        connectionFactory.setPort(5672);           //端口     默认值：5672
        connectionFactory.setVirtualHost("/wyh");  //虚拟机  默认值：/
        connectionFactory.setUsername("wyh");      //用户名 默认值：guest
        connectionFactory.setPassword("wyh");      //密码  默认值：guest
        //3. 创建Connection
        Connection connection = connectionFactory.newConnection();
        //4. 创建Channel
        Channel channel = connection.createChannel();
        //5. 创建Exchange - 交换机
        channel.exchangeDeclare("wyh-direct", BuiltinExchangeType.DIRECT,true,false,false,null);
        //6. 创建多个Queue
        //如果没有名为wyh-direct-queue-error / wyh-direct-queue-info的队列，那么会自动创建，有则直接使用
        channel.queueDeclare("wyh-direct-queue-error",true,false,false,null);
        channel.queueDeclare("wyh-direct-queue-info",true,false,false,null);
        //7. 绑定队列和交换机 - Binding
        channel.queueBind("wyh-direct-queue-error","wyh-direct","error");
        channel.queueBind("wyh-direct-queue-error","wyh-direct","all");//绑定一样的routingKey，都能获取相同的消息

        channel.queueBind("wyh-direct-queue-info","wyh-direct","info");
        channel.queueBind("wyh-direct-queue-info","wyh-direct","warning");
        channel.queueBind("wyh-direct-queue-info","wyh-direct","exception");
        channel.queueBind("wyh-direct-queue-info","wyh-direct","all");//绑定一样的routingKey，都能获取相同的消息
        //8. 发送消息到Exchange
        channel.basicPublish("wyh-direct","info",null,"WangYihuan Will Change The World!-info".getBytes());
        channel.basicPublish("wyh-direct","warning",null,"WangYihuan Will Change The World!-warning".getBytes());
        channel.basicPublish("wyh-direct","exception",null,"WangYihuan Will Change The World!-exception".getBytes());
        channel.basicPublish("wyh-direct","error",null,"WangYihuan Will Failed?-NO".getBytes());
        channel.basicPublish("wyh-direct","all",null,"WangYihuan is very cool!".getBytes());
        //9. 释放资源
        channel.close();
        connection.close();
    }
}
```

ErrorConsumer

```java
public class ErrorConsumer {
    public static void main(String[] args) throws Exception {
        //路由模式 - 消费者不需要设置Exchange交换机 - 只需要连接指定队列
        //1. 创建连接工厂
        ConnectionFactory connectionFactory = new ConnectionFactory();
        //2. 设置参数
        connectionFactory.setHost("192.168.88.92");//ip       默认值：localhost
        connectionFactory.setPort(5672);           //端口     默认值：5672
        connectionFactory.setVirtualHost("/wyh");  //虚拟机  默认值：/
        connectionFactory.setUsername("wyh");      //用户名 默认值：guest
        connectionFactory.setPassword("wyh");      //密码  默认值：guest
        //3. 创建Connection
        Connection connection = connectionFactory.newConnection();
        //4. 创建Channel
        Channel channel = connection.createChannel();
        //5. 从Queue接收消息
        Consumer consumer = new DefaultConsumer(channel) {
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                System.out.println("error消费者已获取消息,将把消息写入日志并存入数据库");
                System.out.println("消息: " + new String(body));
            }
        };
        channel.basicConsume("wyh-direct-queue-error",true,consumer);
        //6. 不要释放资源，消费者相当于是监听队列中是否存在消息，存在就获取来消费掉
    }
}
```

InfoConsumer

```java
public class InfoConsumer {
    public static void main(String[] args) throws Exception {
        //路由模式 - 消费者不需要设置Exchange交换机 - 只需要连接指定队列
        //1. 创建连接工厂
        ConnectionFactory connectionFactory = new ConnectionFactory();
        //2. 设置参数
        connectionFactory.setHost("192.168.88.92");//ip       默认值：localhost
        connectionFactory.setPort(5672);           //端口     默认值：5672
        connectionFactory.setVirtualHost("/wyh");  //虚拟机  默认值：/
        connectionFactory.setUsername("wyh");      //用户名 默认值：guest
        connectionFactory.setPassword("wyh");      //密码  默认值：guest
        //3. 创建Connection
        Connection connection = connectionFactory.newConnection();
        //4. 创建Channel
        Channel channel = connection.createChannel();
        //5. 从Queue接收消息
        Consumer consumer = new DefaultConsumer(channel) {
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                System.out.println("info消费者已获取消息,将把消息及时处理");
                System.out.println("消息: " + new String(body));
            }
        };
        channel.basicConsume("wyh-direct-queue-info",true,consumer);
        //6. 不要释放资源，消费者相当于是监听队列中是否存在消息，存在就获取来消费掉
    }
}
```



**通配符模式/主题模式：Topics**

![](https://www.rabbitmq.com/img/tutorials/python-five.png)

1. 交换机类型为topic
2. 路由功能添加模糊匹配
3. 交换机根据key的规则模糊匹配到对应的队列,由队列的监听消费者接收消息消费

属于是路由模式的一种，增加了模糊匹配

topics模式可以实现订阅模式和路由模式

Topic模式下的**绑定规则匹配**：

* *（星号）：**表示一个单词**，必须有一个单词
* #（井号）：表示**任意数量的单词**

|       匹配键       | topic.\*.\* | topic.# |
| :----------------: | :---------: | :-----: |
|   topic.order.id   |    true     |  true   |
|   order.topic.id   |    false    |  false  |
| topic.ssm.order.id |    false    |  true   |
|    topic.ssm.id    |    true     |  true   |
|   topic.id.order   |    true     |  true   |
|      topic.id      |    false    |  true   |
|    topic.order     |    false    |  true   |

演示案例与路由模式的案例差不多，只是RoutingKey为模糊匹配而已

------



### Spring整合RabbitMQ

步骤

生产者：

1. 创建生产者工程
2. 添加依赖
3. 配置整合
4. 编写代码发送消息

消费者：

1. 创建消费者工程
2. 添加依赖
3. 配置整合
4. 编写监听者监听消息队列接收消息



**配置文件**

rabbitmq.properties

```properties
rabbitmq.host=192.168.xxx.xxx
rabbitmq.port=5672
rabbitmq.username=wyh
rabbitmq.password=wyh
rabbitmq.virtual-host=/wyh
```

spring-rabbitmq-producer.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:rabbit="http://www.springframework.org/schema/rabbit"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/rabbit
       http://www.springframework.org/schema/rabbit/spring-rabbit.xsd">
    <!--加载配置文件-->
    <context:property-placeholder location="classpath:rabbitmq.properties"/>
    <!-- 定义rabbitmq connectionFactory -->
    <rabbit:connection-factory id="connectionFactory" host="${rabbitmq.host}"
                               port="${rabbitmq.port}"
                               username="${rabbitmq.username}"
                               password="${rabbitmq.password}"
                               virtual-host="${rabbitmq.virtual-host}"/>
    <!--定义管理交换机、队列-->
    <rabbit:admin connection-factory="connectionFactory"/>

    <!--
        定义持久化队列，不存在则自动创建；不绑定到交换机则绑定到默认交换机
        默认交换机类型为direct，名字为：""，路由键为队列的名称
        参数：
        id：bean的名称
        name：queue的名称
        auto-declare：自动创建
        auto-delete：自动删除
        exclusive：是否独占
        durable：是否持久化
    -->
    <rabbit:queue id="spring-queue" name="spring-queue" auto-declare="true"/>

    <!-- ~~~~~~~~~~~~~~~~~~~~~~~~~~~~广播；所有队列都能收到消息~~~~~~~~~~~~~~~~~~~~~~~~~~~~ -->
    <!--定义广播交换机中的持久化队列，不存在则自动创建-->
    <rabbit:queue id="spring-fanout-queue-1" name="spring-fanout-queue-1" auto-declare="true"/>
    <!--定义广播交换机中的持久化队列，不存在则自动创建-->
    <rabbit:queue id="spring-fanout-queue-2" name="spring-fanout-queue-2" auto-declare="true"/>
    <!--定义广播类型交换机；并绑定上述两个队列-->
    <rabbit:fanout-exchange id="spring-fanout-exchange" name="spring-fanout-exchange" auto-declare="true">
        <rabbit:bindings>
            <rabbit:binding queue="spring-fanout-queue-1"/>
            <rabbit:binding queue="spring-fanout-queue-2"/>
        </rabbit:bindings>
    </rabbit:fanout-exchange>

    <!--Direct交换机 - Routing Mode-->
    <rabbit:queue id="spring-direct-queue-info" name="spring-direct-queue-info" auto-declare="true"/>
    <rabbit:queue id="spring-direct-queue-error" name="spring-direct-queue-error" auto-declare="true"/>
    <rabbit:direct-exchange name="spring-direct-exchange" id="spring-direct-exchange" auto-declare="true">
        <rabbit:bindings>
            <!-- 参数key就是绑定的RoutingKey queue就是指定和哪个队列绑定-->
            <rabbit:binding key="wyh.pubsub.info" queue="spring-direct-queue-info"/>
            <rabbit:binding key="wyh.pubsub.warning" queue="spring-direct-queue-info"/>
            <rabbit:binding key="wyh.pubsub.orders" queue="spring-direct-queue-info"/>
            <rabbit:binding key="wyh.pubsub.exception" queue="spring-direct-queue-info"/>
            <rabbit:binding key="wyh.pubsub.error" queue="spring-direct-queue-error"/>
            <rabbit:binding key="wyh.pubsub.exception" queue="spring-direct-queue-error"/>
        </rabbit:bindings>
    </rabbit:direct-exchange>

    <!-- ~~~~~~~~~~~~~~~~~~~~~~~~~~~~通配符；*匹配一个单词，#匹配多个单词 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~ -->
    <!--定义交换机中的持久化队列，不存在则自动创建-->
    <rabbit:queue id="spring-topic-queue-star" name="spring-topic-queue-star" auto-declare="true"/>
    <!--定义交换机中的持久化队列，不存在则自动创建-->
    <rabbit:queue id="spring-topic-queue-well" name="spring-topic-queue-well" auto-declare="true"/>
    <!--定义交换机中的持久化队列，不存在则自动创建-->
    <rabbit:queue id="spring-topic-queue-well2" name="spring-topic-queue-well2" auto-declare="true"/>
    <rabbit:topic-exchange id="spring-topic-exchange" name="spring-topic-exchange" auto-declare="true">
        <rabbit:bindings>
            <rabbit:binding pattern="wyh.*" queue="spring-topic-queue-star"/>
            <rabbit:binding pattern="wyh.#" queue="spring-topic-queue-well"/>
            <rabbit:binding pattern="System.wyh.#" queue="spring-topic-queue-well2"/>
        </rabbit:bindings>
    </rabbit:topic-exchange>

    <!--定义rabbitTemplate对象操作可以在代码中方便发送消息-->
    <rabbit:template id="rabbitTemplate" connection-factory="connectionFactory"/>
</beans>
```

spring-rabbitmq-consumer.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:rabbit="http://www.springframework.org/schema/rabbit"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/rabbit
       http://www.springframework.org/schema/rabbit/spring-rabbit.xsd">
    <!--加载配置文件-->
    <context:property-placeholder location="classpath:rabbitmq.properties"/>
    <!-- 定义rabbitmq connectionFactory -->
    <rabbit:connection-factory id="connectionFactory" host="${rabbitmq.host}"
                               port="${rabbitmq.port}"
                               username="${rabbitmq.username}"
                               password="${rabbitmq.password}"
                               virtual-host="${rabbitmq.virtual-host}"/>

    <bean id="springQueueListener" class="com.wyh.rabbitmq.listener.SpringQueueListener"/>
    <bean id="fanoutListener1" class="com.wyh.rabbitmq.listener.FanoutListener1"/>
    <bean id="fanoutListener2" class="com.wyh.rabbitmq.listener.FanoutListener2"/>
    <bean id="topicListenerStar" class="com.wyh.rabbitmq.listener.TopicListenerStar"/>
    <bean id="topicListenerWell" class="com.wyh.rabbitmq.listener.TopicListenerWell"/>
    <bean id="topicListenerWell2" class="com.wyh.rabbitmq.listener.TopicListenerWell2"/>
    <bean id="directListenerInfo" class="com.wyh.rabbitmq.listener.DirectInfoListener"/>
    <bean id="directListenerError" class="com.wyh.rabbitmq.listener.DirectErrorListener"/>

    <rabbit:listener-container connection-factory="connectionFactory">
        <rabbit:listener ref="springQueueListener" queue-names="spring-queue"/>
        <rabbit:listener ref="fanoutListener1" queue-names="spring-fanout-queue-1"/>
        <rabbit:listener ref="fanoutListener2" queue-names="spring-fanout-queue-2"/>
        <rabbit:listener ref="topicListenerStar" queue-names="spring-topic-queue-star"/>
        <rabbit:listener ref="topicListenerWell" queue-names="spring-topic-queue-well"/>
        <rabbit:listener ref="topicListenerWell2" queue-names="spring-topic-queue-well2"/>
        <rabbit:listener ref="directListenerInfo" queue-names="spring-direct-queue-info"/>
        <rabbit:listener ref="directListenerError" queue-names="spring-direct-queue-error"/>
    </rabbit:listener-container>
</beans>
```



案例演示

ProducerTest

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:spring-rabbitmq-producer.xml")
public class ProducerTest {
    @Autowired
    private RabbitTemplate rabbitTemplate;//1.注入RabbitTemplate
    /**
     * 简单模式
     */
    @Test
    public void SimpleMode() {
        //2.发送消息
        rabbitTemplate.convertAndSend("spring-queue","Change The World!");
    }
    /**
     * 工作模式
     */
    @Test
    public void WorkQueues() {
        //2.发送消息
        rabbitTemplate.convertAndSend("spring-queue","Change The World!");
    }
    /**
     * 订阅模式
     */
    @Test
    public void pubSub() {
        //2.发送消息
        rabbitTemplate.convertAndSend("spring-fanout-exchange","","spring-fanout-queue-PubSub-iWyh2");
    }
    /**
     * 路由模式
     */
    @Test
    public void Routing() {
        //2.发送消息
        rabbitTemplate.convertAndSend("spring-direct-exchange","wyh.pubsub.info","spring-direct-exchange-Routing-iWyh2");
        rabbitTemplate.convertAndSend("spring-direct-exchange","wyh.pubsub.warning","spring-direct-exchange-Routing-iWyh2");
        rabbitTemplate.convertAndSend("spring-direct-exchange","wyh.pubsub.orders","spring-direct-exchange-Routing-iWyh2");
        rabbitTemplate.convertAndSend("spring-direct-exchange","wyh.pubsub.exception","spring-direct-exchange-Routing-iWyh2");
        rabbitTemplate.convertAndSend("spring-direct-exchange","wyh.pubsub.error","spring-direct-exchange-Routing-iWyh2");
    }
    /**
     * 话题模式
     */
    @Test
    public void Topics() {
        //2.发送消息
        rabbitTemplate.convertAndSend("spring-topic-exchange","wyh.info","spring-topic-exchange-Topics-iWyh2");
        rabbitTemplate.convertAndSend("spring-topic-exchange","wyh.info.info","spring-topic-exchange-Topics-iWyh2 will change the world!");
        rabbitTemplate.convertAndSend("spring-topic-exchange","System.wyh.info","spring-topic-exchange-Topics-iWyh2 will change the world!");
    }
}
```

ConsumerTest

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:spring-rabbitmq-consumer.xml")
public class ConsumerTest {
    @Test
    public void Listeners() {
        while (true) {
            //持续监听所有的队列 接收所有的队列的消息
        }
    }
}
```

------



### SpringBoot整合RabbitMQ

步骤

生产者：

1. 创建SpringBoot-生产者工程

2. 引入依赖

   ```xml
   <dependency>
   	<groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-amqp</artifactId>
   </dependency>
   ```

3. 配置yml

4. 定义交换机，队列，绑定关系的配置类

5. 注入RabbitTemplate，调用方法，发送消息

消费者：

1. 创建SpringBoot-生产者工程

2. 引入依赖

   ```xml
   <dependency>
   	<groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-amqp</artifactId>
   </dependency>
   ```

3. 配置yml

4. 使用@RabbitListener定义监听类监听队列



**基本配置信息都在yml中，而队列，交换机，绑定关系的定义都使用Bean的方式**

**RabbitTemplate发送消息，@RabbitListener定义监听接收消息**



案例演示

yml配置

```yml
#配置rabbitmq
spring:
  rabbitmq:
    host: 192.168.xxx.xxx
    port: 5672
    username: wyh
    password: wyh
    virtual-host: /wyh
```

生产端：

RabbitConfig

```java
/**
 * RabbitMQ配置类
 * 配置：
 * 1.Exchange交换机
 * 2.Queue队列
 * 3.Binding绑定关系
 */
@Configuration
public class RabbitConfig {
    //1.Exchange交换机
    @Bean("topicExchange")
    public Exchange TopicExchange() {
        return ExchangeBuilder.topicExchange("bootTopicExchange")
                .durable(true)
                .build();
    }
    //2.Queue队列
    @Bean("topicErrorQueue")
    public Queue TopicErrorQueue() {
        return QueueBuilder.durable("bootTopicErrorQueue").build();
    }
    @Bean("topicInfoQueue")
    public Queue TopicInfoQueue() {
        return QueueBuilder.durable("bootTopicInfoQueue").build();
    }
    //3.Binding绑定关系
    @Bean
    public Binding BindingError(@Qualifier("topicErrorQueue") Queue queue, @Qualifier("topicExchange")Exchange exchange) {
        return BindingBuilder.bind(queue).to(exchange).with("*.wyh.error").noargs();
    }
    @Bean
    public Binding BindingError_Info_Exception(@Qualifier("topicErrorQueue") Queue queue, @Qualifier("topicExchange")Exchange exchange) {
        return BindingBuilder.bind(queue).to(exchange).with("*.wyh.info.exception").noargs();
    }
    @Bean
    public Binding BindingInfo(@Qualifier("topicInfoQueue") Queue queue, @Qualifier("topicExchange")Exchange exchange) {
        return BindingBuilder.bind(queue).to(exchange).with("*.wyh.info.*").noargs();
    }
}
```

ProducerTest

```java
@SpringBootTest
public class ProducerTest {
    @Autowired
    private RabbitTemplate rabbitTemplate;//1.注入RabbitTemplate
    @Test
    public void Topics() {
        //2.发送消息
        rabbitTemplate.convertAndSend("bootTopicExchange","System.wyh.error","Topic-iWyh2-error");
        rabbitTemplate.convertAndSend("bootTopicExchange","System.wyh.info.exception","Topic-iWyh2-info-exception");
        rabbitTemplate.convertAndSend("bootTopicExchange","System.wyh.info.warning","Topic-iWyh2-info-warning");
        rabbitTemplate.convertAndSend("bootTopicExchange","System.wyh.info.orders","Topic-iWyh2-info-orders");
    }
}
```

消费端

RabbitMQListener

```java
@Component
public class RabbitMQListener {
    @RabbitListener(queues = {"bootTopicErrorQueue"})
    public void TopicErrorQueueListener(Message message) {
        System.out.println("TopicErrorQueue's message: "+new String(message.getBody()));
    }
    @RabbitListener(queues = {"bootTopicInfoQueue"})
    public void TopicInfoQueueListener(Message message) {
        System.out.println("TopicInfoQueue's message: "+new String(message.getBody()));
    }
}
```

------



## 高级

### RabbitMQ高级特性

#### 消息可靠性

1. 持久化：
   * exchange持久化
   * queue持久化
   * message持久化
2. 生产端确认模式confirm
3. 消费端手动确认Ack
4. Broker高可用

##### 消息可靠性投递

RabbitMQ消息投递的路径：

**producer ===> rabbitmq broker ===> exchange ===> queue ===> consumer**

在我们使用RabbitMQ时，作为**消息的发送方希望杜绝任何消息发送失败或者消息丢失的场景**

所以，RabbitMQ为我们提供了两种方式来控制消息的投递可靠性模式

* confirm 确认模式

  ：消息**从producer到exchange**会返回一个**confirmCallback**

* return 返回模式

  ：消息**从exchange到queue投递失败时**，会返回一个**returnCallback**



案例 - Spring整合版（SpringBoot整合版请看基础篇的SpringBoot整合RabbitMQ案例）

配置文件 rabbitmq.properties

```properties
rabbitmq.host=192.168.xx.xxx
rabbitmq.port=5672
rabbitmq.username=wyh
rabbitmq.password=wyh
rabbitmq.virtual-host=/wyh
```

配置文件 spring-rabbitmq-producer.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:rabbit="http://www.springframework.org/schema/rabbit"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/rabbit
       http://www.springframework.org/schema/rabbit/spring-rabbit.xsd">
    <!--加载配置文件-->
    <context:property-placeholder location="classpath:rabbitmq.properties"/>
    <!-- 定义rabbitmq connectionFactory -->
    <rabbit:connection-factory id="connectionFactory" host="${rabbitmq.host}"
                               port="${rabbitmq.port}"
                               username="${rabbitmq.username}"
                               password="${rabbitmq.password}"
                               confirm-type="CORRELATED"
                               publisher-returns="true"
                               virtual-host="${rabbitmq.virtual-host}"/>
    <!--定义管理交换机、队列-->
    <rabbit:admin connection-factory="connectionFactory"/>
    <!--定义rabbitTemplate对象操作可以在代码中方便发送消息-->
    <rabbit:template id="rabbitTemplate" connection-factory="connectionFactory"/>

    <!--消息可靠性投递-->
    <rabbit:queue id="Confirm-queue" name="Confirm-queue" auto-declare="true"/>
    <rabbit:direct-exchange name="Confirm-direct-exchange" auto-declare="true">
        <rabbit:bindings>
            <rabbit:binding queue="Confirm-queue" key="wyh.confirm"/>
        </rabbit:bindings>
    </rabbit:direct-exchange>

</beans>
```

ProducerTest

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:spring-rabbitmq-producer.xml")
public class ProducerTest {
    @Autowired
    private RabbitTemplate rabbitTemplate;

    /**
     * confirm 确认模式
     * 1.开启确认模式（connectionFactory中开启confirm-type="CORRELATED"
     * 2.使用RabbitTemplate定义confirmCallback回调函数
     */
    @Test
    public void Confirm() {
        //定义回调
        rabbitTemplate.setConfirmCallback(new RabbitTemplate.ConfirmCallback() {
            /**
             * ConfirmCallback的confirm方法参数
             * @param correlationData：相关配置信息
             * @param b - ack：交换机是否接收到了消息
             * @param s - cause：交换机接收失败的原因
             */
            @Override
            public void confirm(CorrelationData correlationData, boolean b, String s) {
                System.out.println("This is the ConfirmCallback");
                if (!b) {
                    System.out.println("failed cause: " + s);
                    //失败后消息处理 比如再次发送消息
                }
            }
        });
        //发送消息
        rabbitTemplate.convertAndSend("Confirm-direct-exchange","wyh.confirm","iWyh2-Confirm");
    }
    
    /**
     * return 回退模式
     * 1.开启回退模式（connectionFactory中开启publisher-returns="true"
     * 2.设置交换机Exchange处理消息的模式：
     *                               ① 如果消息没有路由到Queue，则丢弃消息（默认处理）
     *                               ② 如果消息没有路由到Queue，返回给消息发送方的ReturnCallback
     * 3.设置ReturnCallback
     */
    @Test
    public void Return() {
        //2.设置交换机处理消息失败之后，将消息返回给发送方，ReturnCallback中就可以获取这条返回的消息
        rabbitTemplate.setMandatory(true);
        //3.设置ReturnCallback
        rabbitTemplate.setReturnCallback(new RabbitTemplate.ReturnCallback() {
            /**
             * ReturnCallback的returnedMessage方法参数
             * @param message 消息对象
             * @param i - replyCode：错误码
             * @param s - replyText：错误信息
             * @param s1 - exchange：交换机名称
             * @param s2 - routingKey：消息的路由键
             */
            @Override
            public void returnedMessage(Message message, int i, String s, String s1, String s2) {
                System.out.println("This is the ReturnCallback");
                System.out.println("message: "+new String(message.getBody()));
                System.out.println("replyCode: "+i);
                System.out.println("replyText: "+s);
                System.out.println("exchange: "+s1);
                System.out.println("routingKey: "+s2);
                //对消息的二次处理
            }
        });
        //4.发送消息 - RoutingKey错误，将会失败
        rabbitTemplate.convertAndSend("Return-direct-exchange","wyh.return.confirm","iWyh2-Return");
    }
}
```

------



##### Consumer Ack

Ack为Acknowledge - 确认，表示消费端收到消息之后的确认方式

也就是**consumer到rabbitmq broker之间的消息可靠性保障**

有三种确认方式：

* 自动确认：acknowledge="none"（默认方式）

  ：当消息被消费端接收到后，会自动确认收到，并将message从RabbitMQ的消息缓存中移除。

  ​    但在实际业务之中，消息确认收到之后，进行业务处理时，出现异常会导致消息丢失，但是消息缓存中已没有了消息，这是不安全的

* 手动确认：acknowledge="manual"

  ：在业务处理中，自己手动确认收到消息，并成功处理，可以让消息缓存移除消息了

  ​	**channel.basicAck()** - 手动签收

  ​	**channel.basicNack()** - 让其自动重新发送消息

* 根据异常确认：acknowledge="auto"（这种方式很麻烦，不做要求）

案例 - Spring整合版（SpringBoot整合版请看基础篇的SpringBoot整合RabbitMQ案例）

配置文件 rabbitmq.properties

```properties
rabbitmq.host=192.168.xx.xxx
rabbitmq.port=5672
rabbitmq.username=wyh
rabbitmq.password=wyh
rabbitmq.virtual-host=/wyh
```

配置文件 spring-rabbitmq-producer.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:rabbit="http://www.springframework.org/schema/rabbit"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/rabbit
       http://www.springframework.org/schema/rabbit/spring-rabbit.xsd">
    <!--加载配置文件-->
    <context:property-placeholder location="classpath:rabbitmq.properties"/>
    <!-- 定义rabbitmq connectionFactory -->
    <rabbit:connection-factory id="connectionFactory" host="${rabbitmq.host}"
                               port="${rabbitmq.port}"
                               username="${rabbitmq.username}"
                               password="${rabbitmq.password}"
                               virtual-host="${rabbitmq.virtual-host}"/>
    <!--定义包扫描-->
    <context:component-scan base-package="com.wyh.listener"/>
    <!--定义监听器容器-->
    <rabbit:listener-container connection-factory="connectionFactory" acknowledge="manual">
        <rabbit:listener ref="ackListener" queue-names="Confirm-queue"/>
    </rabbit:listener-container>
</beans>
```

消费端

AckListener

```java
@Component
public class AckListener implements ChannelAwareMessageListener {
    /**
     * Consumer Ack
     * 1.设置手动确认（spring-rabbitmq-consumer.xml中的rabbit:listener-container设置acknowledge="manual"
     * 2.让监听器实现ChannelAwareMessageListener接口
     * 3.如果消息成功处理，调用basicAck确认签收
     * 4.如果消息处理失败，调用basicNack拒绝签收，并让其重新发送
     */
    @Override
    public void onMessage(Message message, Channel channel) throws Exception {
        try {
            System.out.println("This is a manual confirmation message");
            System.out.println("The business is being processed...");
            //int i = 1/0;
            //如果处理成功，手动签收，移除缓存
            /*
             * basicAck方法参数
             * 1.deliveryTag：当前收到的消息的递送过来的Tag值 - long
             * 2.multiple：是否签收多条消息 - boolean -true为签收全部
             */
            channel.basicAck(message.getMessageProperties().getDeliveryTag(),true);
        } catch (Exception e) {
            //出现异常 拒绝签收
            /*
             * basicNack方法参数
             * 1.deliveryTag：当前收到的消息的递送过来的Tag值 - long
             * 2.multiple：是否签收多条消息 - boolean -true为签收全部
             * 3.requeue：是否重回队列 - boolean -true为消息重新回到queue，且broker会重新发送该消息到消费端
             */
            channel.basicNack(message.getMessageProperties().getDeliveryTag(),true,true);
        }
    }

}
```

ConsumerTest

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = {"classpath:spring-rabbitmq-consumer.xml"})
public class ConsumerTest {

    @Test
    public void ManualConfirmation() throws InterruptedException {
        Thread.sleep(10000);
    }
}
```

------



#### 消费端限流

也就是"削峰填谷"

限制一次性发送到系统的消息量，避免系统承载不住而宕机

案例演示 - Spring整合版（SpringBoot整合版请看基础篇的SpringBoot整合RabbitMQ案例）

配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:rabbit="http://www.springframework.org/schema/rabbit"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/rabbit
       http://www.springframework.org/schema/rabbit/spring-rabbit.xsd">
    <!--加载配置文件-->
    <context:property-placeholder location="classpath:rabbitmq.properties"/>
    <!-- 定义rabbitmq connectionFactory -->
    <rabbit:connection-factory id="connectionFactory" host="${rabbitmq.host}"
                               port="${rabbitmq.port}"
                               username="${rabbitmq.username}"
                               password="${rabbitmq.password}"
                               virtual-host="${rabbitmq.virtual-host}"/>
    <!--定义包扫描-->
    <context:component-scan base-package="com.wyh.listener"/>
    <!--定义监听器容器-->
    <rabbit:listener-container connection-factory="connectionFactory" acknowledge="manual" prefetch="1">
        <rabbit:listener ref="qosListener" queue-names="Confirm-queue"/>
    </rabbit:listener-container>
</beans>
```

QosListener

```java
@Component
public class QosListener implements ChannelAwareMessageListener {
    /**
     * 消费端限流
     * 1.首先要开启手动确认（spring-rabbitmq-consumer.xml中的rabbit:listener-container设置acknowledge="manual"
     * 2.配置rabbit:listener-container中的属性：prefetch="1",表示消费端每次从MQ拉取一条消息消费，直到被手动确认，才会继续拉取下一条消息
     */
    @Override
    public void onMessage(Message message, Channel channel) throws Exception {
        try {
            System.out.println("This is a manual confirmation message");
            System.out.println("The business is being processed...");
            System.out.println("message: "+new String(message.getBody()));
            //如果处理成功，手动签收，移除缓存
            /*
             * basicAck方法参数
             * 1.deliveryTag：当前收到的消息的递送过来的Tag值 - long
             * 2.multiple：是否签收多条消息 - boolean -true为签收全部
             */
            channel.basicAck(message.getMessageProperties().getDeliveryTag(),true);
        } catch (Exception e) {
            /*
             * basicNack方法参数
             * 1.deliveryTag：当前收到的消息的递送过来的Tag值 - long
             * 2.multiple：是否签收多条消息 - boolean -true为签收全部
             * 3.requeue：是否重回队列 - boolean -true为消息重新回到queue，且broker会重新发送该消息到消费端
             */
            channel.basicNack(message.getMessageProperties().getDeliveryTag(),true,true);
        }
    }

}
```

ConsumerTest

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = {"classpath:spring-rabbitmq-consumer.xml"})
public class ConsumerTest {

    @Test
    public void ManualConfirmation() throws InterruptedException {
        Thread.sleep(30000);
    }
}
```

------



#### TTL

TTL：Time To Live（存活时间）

当消息到达存活时间之后，还没有被消费，就会被自动清除

这是用于生产端的配置

应用场景："请在三十分钟之内完成支付"

RabbitMQ可以**对消息设置TTL**，也可以**对整个队列设置TTL**

我们可以直接在RabbitMQManagement页面自己设置：添加队列 - 设置**x-message-ttl**（也就是给整个队列设置了TTL）

案例演示 - Spring整合版（SpringBoot整合版请看基础篇的SpringBoot整合RabbitMQ案例）

配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:rabbit="http://www.springframework.org/schema/rabbit"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/rabbit
       http://www.springframework.org/schema/rabbit/spring-rabbit.xsd">
    <!--加载配置文件-->
    <context:property-placeholder location="classpath:rabbitmq.properties"/>
    <!-- 定义rabbitmq connectionFactory -->
    <rabbit:connection-factory id="connectionFactory" host="${rabbitmq.host}"
                               port="${rabbitmq.port}"
                               username="${rabbitmq.username}"
                               password="${rabbitmq.password}"
                               confirm-type="CORRELATED"
                               virtual-host="${rabbitmq.virtual-host}"/>
    <!--定义管理交换机、队列-->
    <rabbit:admin connection-factory="connectionFactory"/>
    <!--定义rabbitTemplate对象操作可以在代码中方便发送消息-->
    <rabbit:template id="rabbitTemplate" connection-factory="connectionFactory"/>

    <!--消息可靠性投递-->
    <rabbit:queue id="Confirm-queue" name="Confirm-queue" auto-declare="true"/>
    <rabbit:direct-exchange name="Confirm-direct-exchange" auto-declare="true">
        <rabbit:bindings>
            <rabbit:binding queue="Confirm-queue" key="wyh.confirm"/>
        </rabbit:bindings>
    </rabbit:direct-exchange>

    <!--队列的TTL配置-->
    <rabbit:queue id="TTL-queue" name="TTL-queue" auto-declare="true">
        <!--队列的参数设置-->
        <rabbit:queue-arguments>
            <!--x-message-ttl：队列全部消息的存活时间-->
            <entry key="x-message-ttl" value="8000" value-type="java.lang.Integer"/>
        </rabbit:queue-arguments>
    </rabbit:queue>

    <!--配置交换机 绑定上面配置的队列-->
    <rabbit:topic-exchange name="TTL-exchange" id="TTL-exchange" auto-declare="true">
        <rabbit:bindings>
            <rabbit:binding pattern="wyh.ttl.#" queue="TTL-queue"/>
        </rabbit:bindings>
    </rabbit:topic-exchange>
</beans>
```

ProducerTest

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:spring-rabbitmq-producer.xml")
public class ProducerTest {
    @Autowired
    private RabbitTemplate rabbitTemplate;
    /**
     * TTL
     * 1.Queue的统一过期时间
     * 2.消息的单独过期时间
     * 注：
     * 如果即设置了消息的过期时间，又设置了队列的统一过期时间，那么【以时间短的为准】
     * 消息过期之后，只有在队列顶端，队列才会判断这条消息是否过期该移除掉
     * （所以哪怕十条消息中有一条的存活时间短，但是它并没有在队列的顶端，是依然会存在队列中，等到队列全部过期移除掉）
     */
    @Test
    public void TTL() {
        //这十条消息将会遵从Queue的统一过期时间
        for (int i = 0; i < 10; i++) {
            rabbitTemplate.convertAndSend("TTL-exchange","wyh.ttl.info","iWyh2-TTL-info");
        }
        //消息的单独过期时间
        //1.设置消息后处理对象，设置消息的参数信息
        MessagePostProcessor messagePostProcessor = new MessagePostProcessor() {
            @Override
            public Message postProcessMessage(Message message) throws AmqpException {
                //1.设置message的信息
                //Expiration，单位ms，当该消息在队列顶端时，会单独判断该消息是否过期
                message.getMessageProperties().setExpiration("3000");//设置消息的过期时间 - 3000ms
                //2.返回该消息
                return message;
            }
        };
        rabbitTemplate.convertAndSend("TTL-exchange","wyh.ttl.info","iWyh2-TTL-info",messagePostProcessor);
    }
}
```

------



#### 死信队列

死信队列，DLX：Dead Letter Exchange（死信交换机，由于其他的MQ产品没有交换机的概念，所以其他的死信队列为Dead Letter Queue，RabbitMQ为死信交换机）

也就是当消息成为Dead Message之后（比如过期了）可以被发送给另外一个交换机，这个交换机就是DLX

![](https://m.haicoder.net/uploads/pic/mq/rabbitmq/rabbitmq-advanced/11_RabbitMQ%E6%AD%BB%E4%BF%A1%E9%98%9F%E5%88%97.png)

消息成为死信（Dead Message）的三种情况：

1. **队列消息长度达到限制**
2. **消费者没有签收消息，并且不把消息重新放回原目标队列**（basicNack的requeue设为false）
3. 原**队列存在消息过期TTL设置**，消息到达存活时间之后没有被消费

队列绑定死信交换机：**给队列设置参数**

* x-dead-letter-exchange
* x-dead-letter-routing-key

死信队列和死信交换机和普通的队列和交换机**没有区别**

一个队列里的消息如果成了死信，且这个队列绑定了额外的死信交换机，该条死信就会被死信交换机路由到死信队列中

案例演示 - Spring整合版（SpringBoot整合版请看基础篇的SpringBoot整合RabbitMQ案例）

spring-rabbitmq-producer.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:rabbit="http://www.springframework.org/schema/rabbit"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/rabbit
       http://www.springframework.org/schema/rabbit/spring-rabbit.xsd">
    <!--加载配置文件-->
    <context:property-placeholder location="classpath:rabbitmq.properties"/>
    <!-- 定义rabbitmq connectionFactory -->
    <rabbit:connection-factory id="connectionFactory" host="${rabbitmq.host}"
                               port="${rabbitmq.port}"
                               username="${rabbitmq.username}"
                               password="${rabbitmq.password}"
                               confirm-type="CORRELATED"
                               virtual-host="${rabbitmq.virtual-host}"/>
    <!--定义管理交换机、队列-->
    <rabbit:admin connection-factory="connectionFactory"/>
    <!--定义rabbitTemplate对象操作可以在代码中方便发送消息-->
    <rabbit:template id="rabbitTemplate" connection-factory="connectionFactory"/>

    <!--
        死信队列
        1.声明正常的队列和交换机
        2.声明死信队列和死信交换机
        3.将正常队列绑定死信交换机
              设置两个参数：
                        ① x-dead-letter-exchange：死信交换机名称
                        ② x-dead-letter-routing-key：消息发送给死信交换机的routingKey
    -->
    <rabbit:queue id="NormalQueue" name="NormalQueue" auto-declare="true">
        <rabbit:queue-arguments>
            <!--x-dead-letter-exchange：死信交换机名称-->
            <entry key="x-dead-letter-exchange" value="DeadLetterExchange"/>
            <!--x-dead-letter-routing-key：消息发送给死信交换机的routingKey 要符合死信交换机和死信队列绑定的routingKey-->
            <entry key="x-dead-letter-routing-key" value="wyh.dlx.info"/>
            <!--设置死信消息的完成前提-->
            <!--1.设置队列的ttl-->
            <entry key="x-message-ttl" value="10000" value-type="java.lang.Integer"/>
            <!--2.设置队列的长度限定-->
            <entry key="x-max-length" value="10" value-type="java.lang.Integer"/>
        </rabbit:queue-arguments>
    </rabbit:queue>
    <rabbit:direct-exchange name="NormalExchange" id="NormalExchange" auto-declare="true">
        <rabbit:bindings>
            <rabbit:binding queue="NormalQueue" key="wyh.info"/>
        </rabbit:bindings>
    </rabbit:direct-exchange>

    <rabbit:queue id="DeadLetterQueue" name="DeadLetterQueue" auto-declare="true"/>
    <rabbit:direct-exchange name="DeadLetterExchange" id="DeadLetterExchange" auto-declare="true">
        <rabbit:bindings>
            <rabbit:binding queue="DeadLetterQueue" key="wyh.dlx.info"/>
        </rabbit:bindings>
    </rabbit:direct-exchange>
</beans>
```

ProducerTest

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:spring-rabbitmq-producer.xml")
public class ProducerTest {
    @Autowired
    private RabbitTemplate rabbitTemplate;

    /**
     * 测试死信：
     * 1.消息过期
     * 2.超过队列长度
     * 3.消费端拒收
     */
    @Test
    public void DLX() {
        //1.消息过期
        //rabbitTemplate.convertAndSend("NormalExchange","wyh.info","iWyh2-王钇欢YYDS");
        //2.超过队列长度
        for (int i = 0; i < 15; i++) {
            rabbitTemplate.convertAndSend("NormalExchange","wyh.info","iWyh2-王钇欢YYDS");
        }
        //3.消费者拒收消息
        //消费者监听NormalQueue的消息，并对其中的消息拒绝接受，且消息不重新回到该队列
    }
}
```

spring-rabbitmq-consumer.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:rabbit="http://www.springframework.org/schema/rabbit"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/rabbit
       http://www.springframework.org/schema/rabbit/spring-rabbit.xsd">
    <!--加载配置文件-->
    <context:property-placeholder location="classpath:rabbitmq.properties"/>
    <!-- 定义rabbitmq connectionFactory -->
    <rabbit:connection-factory id="connectionFactory" host="${rabbitmq.host}"
                               port="${rabbitmq.port}"
                               username="${rabbitmq.username}"
                               password="${rabbitmq.password}"
                               virtual-host="${rabbitmq.virtual-host}"/>
    <!--定义包扫描-->
    <context:component-scan base-package="com.wyh.listener"/>
    <bean id="deadLetterQueueListener" class="com.wyh.listener.DeadLetterQueueListener"/>
    <!--定义监听器容器-->
    <rabbit:listener-container connection-factory="connectionFactory" acknowledge="manual" prefetch="1">
        <rabbit:listener ref="DLXlistener" queue-names="NormalQueue"/>
<!--        <rabbit:listener ref="deadLetterQueueListener" queue-names="DeadLetterQueue"/>-->
    </rabbit:listener-container>
</beans>
```

DLXlistener

```java
@Component
public class DLXlistener implements ChannelAwareMessageListener {
    @Override
    public void onMessage(Message message, Channel channel) throws Exception {
        try {
            System.out.println("Normal Queue's message: "+new String(message.getBody()));
            Random random = new Random();
            int i = random.nextInt(2);
            //随机接收或者拒收
            if (i == 1) {
                channel.basicAck(message.getMessageProperties().getDeliveryTag(),true);
                System.out.println("The message was received");
            } else {
                channel.basicNack(message.getMessageProperties().getDeliveryTag(),true,false);
                System.out.println("The message was rejected");
            }
            Thread.sleep(1000);
        } catch (Exception e) {
            channel.basicNack(message.getMessageProperties().getDeliveryTag(),true,false);
        }

    }
}
```

ConsumerTest

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:spring-rabbitmq-consumer.xml")
public class ConsumerTest {
    @Test
    public void DLX() {
        while (true) {}
    }
}
```

------



#### 延迟队列

延迟队列，即**消息进入队列后不会被立即消费，只有到达指定时间之后才会被消费**

应用场景：

1. 下单后，三十分钟未支付，取消订单，回滚库存
2. 新用户注册成功七天之后，发送短信问候

实现方式：

1. 定时器（不优雅，且有延迟）
2. 延迟队列

```txt
																  ==支付==> 什么都不做
订单系统 ===> MQ的延迟队列 ==三十分钟后消费消息==> 库存系统 ==判断订单状态==> 
																  ==未支付==> 取消订单，回滚库存
```

但是，RabbitMQ并没有直接现成的延迟队列

But，我们可以使用：**TTL+死信队列** 实现延迟队列，而**监听器应该监听的是死信队列**

![](https://img-blog.csdnimg.cn/20210415175203205.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Bhbl9oMTk5NQ==,size_16,color_FFFFFF,t_70)

案例演示 - Spring整合版（SpringBoot整合版请看基础篇的SpringBoot整合RabbitMQ案例）

spring-rabbitmq-producer.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:rabbit="http://www.springframework.org/schema/rabbit"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/rabbit
       http://www.springframework.org/schema/rabbit/spring-rabbit.xsd">
    <!--加载配置文件-->
    <context:property-placeholder location="classpath:rabbitmq.properties"/>
    <!-- 定义rabbitmq connectionFactory -->
    <rabbit:connection-factory id="connectionFactory" host="${rabbitmq.host}"
                               port="${rabbitmq.port}"
                               username="${rabbitmq.username}"
                               password="${rabbitmq.password}"
                               confirm-type="CORRELATED"
                               virtual-host="${rabbitmq.virtual-host}"/>
    <!--定义管理交换机、队列-->
    <rabbit:admin connection-factory="connectionFactory"/>
    <!--定义rabbitTemplate对象操作可以在代码中方便发送消息-->
    <rabbit:template id="rabbitTemplate" connection-factory="connectionFactory"/>
    <!--
        延迟队列实现：
            1.定义正常队列和交换机
            2.定义死信队列和死信交换机
            3.将正常队列和死信交换机绑定，并设置正常队列的TTL（三十分钟）
    -->
    <rabbit:queue id="OrderNormalQueue" name="OrderNormalQueue" auto-declare="true">
        <rabbit:queue-arguments>
            <!--x-dead-letter-exchange：死信交换机名称-->
            <entry key="x-dead-letter-exchange" value="OrderDeadLetterExchange"/>
            <!--x-dead-letter-routing-key：消息发送给死信交换机的routingKey 要符合死信交换机和死信队列绑定的routingKey-->
            <entry key="x-dead-letter-routing-key" value="wyh.dlx.order.ack"/>
            <!--设置队列的ttl 以十秒代替三十分钟-->
            <entry key="x-message-ttl" value="10000" value-type="java.lang.Integer"/>
        </rabbit:queue-arguments>
    </rabbit:queue>
    <rabbit:topic-exchange name="OrderNormalExchange" id="OrderNormalExchange" auto-declare="true">
        <rabbit:bindings>
            <rabbit:binding queue="OrderNormalQueue" pattern="wyh.order.#"/>
        </rabbit:bindings>
    </rabbit:topic-exchange>

    <rabbit:queue id="OrderDeadLetterQueue" name="OrderDeadLetterQueue" auto-declare="true"/>
    <rabbit:topic-exchange name="OrderDeadLetterExchange" id="OrderDeadLetterExchange" auto-declare="true">
        <rabbit:bindings>
            <rabbit:binding queue="OrderDeadLetterQueue" pattern="wyh.dlx.order.#"/>
        </rabbit:bindings>
    </rabbit:topic-exchange>

</beans>
```

ProducerTest

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = {"classpath:spring-rabbitmq-producer.xml"})
public class ProducerTest {
    @Autowired
    private RabbitTemplate rabbitTemplate;
    @Test
    public void DelayQueue() {
        //模拟下单成功
        System.out.println("You have successfully placed your order");
        //模拟三十分钟内用户任未支付
        //发送确认订单状态消息
        rabbitTemplate.convertAndSend("OrderNormalExchange","wyh.order.ack","Whether the order is paid or not?");
    }
}
```

spring-rabbitmq-consumer.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:rabbit="http://www.springframework.org/schema/rabbit"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/rabbit
       http://www.springframework.org/schema/rabbit/spring-rabbit.xsd">
    <!--加载配置文件-->
    <context:property-placeholder location="classpath:rabbitmq.properties"/>
    <!-- 定义rabbitmq connectionFactory -->
    <rabbit:connection-factory id="connectionFactory" host="${rabbitmq.host}"
                               port="${rabbitmq.port}"
                               username="${rabbitmq.username}"
                               password="${rabbitmq.password}"
                               virtual-host="${rabbitmq.virtual-host}"/>
    <!--定义包扫描-->
    <context:component-scan base-package="com.wyh.listener"/>
    <!--定义监听器容器-->
    <rabbit:listener-container connection-factory="connectionFactory" acknowledge="manual" prefetch="1">
        <rabbit:listener ref="delayListener" queue-names="OrderDeadLetterQueue"/>
    </rabbit:listener-container>
</beans>
```

DelayListener

```java
@Component
public class DelayListener implements ChannelAwareMessageListener {
    @Override
    public void onMessage(Message message, Channel channel) throws Exception {
        System.out.println("30 minutes ago...");//模拟用户三十分钟未完成支付
        System.out.println("The message form OrderSystem: " + new String(message.getBody()));
        System.out.println("Then we are checking the order's state...");
        System.out.println("Oh,no. we find that this order was not paid");
        System.out.println("So,we will cancel the order, and then we will rollback transaction");
        channel.basicAck(message.getMessageProperties().getDeliveryTag(),true);
    }
}
```

ConsumerTest

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = {"classpath:spring-rabbitmq-consumer.xml"})
public class ConsumerTest {
    @Test
    public void CheckOrderState() {
        System.out.println("is listening the queue: OrderDeadLetterQueue..");
        while (true) {}
        }
}
```

------



#### 日志与监控

RabbitMQ默认日志存放路径：/var/log/rabbitmq/rabbit@xxx.log（Linux）

我们可以通过**web控制台**监控RabbitMQ

还可以通过rabbitmqctl命令进行管理和监控

* rabbitmqctl list_queues：查看队列
* rabbitmqctl list_exchanges：查看交换机
* rabbitmqctl list_users：查看用户
* rabbitmqctl list_connections：查看连接
* rabbitmqctl list_consumers：查看消费者信息
* rabbitmqctl environment：查看环境变量
* rabbitmqctl list_queues name messages_unacknowledged：查看未被确认的队列
* rabbitmqctl list_queues name memory：查看单个队列的内存使用
* rabbitmqctl list_queues name messages_ready：查看准备就绪的队列
* rabbitmqctl list_vhosts：查看虚拟机

------



#### 消息可靠性分析与追踪

**消息追踪**

在使用任何消息中间件的过程中，难免会出现某条消息异常消失的情况。对于RabbitMQ而言，可能是因为生产者或者消费者与RabbitMQ断开了连接，而它们又与RabbitMQ采用了不同的确认机制。又有可能是因为交换机和队列之间不同的转发策略。甚至是交换器并没有与任何队列进行绑定，生产者又不感知，或者没有采取相应的措施。另外RabbitMQ本身的集群策略也可能导致消息的丢失。这个时候就需要有一个较好的机制跟踪记录消息的投递过程，以此协助开发和运维人员进行问题的定位

在RabbitMQ中，可以使用Firehose和rabbitmq_tracing插件功能来实现消息的追踪



**Firehose**

Firehose的机制是将生产者投递给RabbitMQ的消息，RabbitMQ投递给消费者的消息按照指定的格式发送到默认的交换机上。这个默认的交换机就是**amq.rabbitmq.trace**，是一个topic类型的交换机。发送到这个交换机上的消息的routingKey为：publish.exchangename和deliver.queuename。其中exchangename和queuename为实际的交换机和队列的名称，分别对应了生产者投递到交换机的消息，和消费者从队列获取消息

【注】打开trace功能会影响消息的写入功能，适当打开之后需要关闭

* rabbitmqctl trace_on：开启Firehose
* rabbitmqctl trace_off：关闭Firehose



**rabbitmq_tracing**

rabbitmq_tracing和Firehose在实现上如出一辙，只是rabbitmq_tracing的方式比Firehose多了一层GUI的包装，更容易使用和管理

1. 启用插件：rabbitmq-plugins enable rabbitmq_tracing
2. 如果创建trace时使用自己创建的虚拟机报错时，为自己创建的虚拟机添加权限即可（web监控台之间双击虚拟机进去，添加guest用户所有权限）

一般开发用就可，生产时用**会影响性能**，最好别用

------



### RabbitMQ应用问题

#### 消息可靠性保障

需求：百分百确保消息发送成功

**消息补偿**

![](https://haicoder.net/uploads/pic/mq/rabbitmq/rabbitmq-advanced/14_RabbitMQ%E6%B6%88%E6%81%AF%E8%A1%A5%E5%81%BF.png)

------



#### 消息幂等性处理

幂等性指的是：一次或多次请求某一个资源，对于资源本身应该具有同样的结果。（在金融行业中应用颇多）

也就是说，任意多次执行对资源本身所产生的影响均与一次执行的影响相同

在MQ中，就是**消费多条相同的消息，得到与消费一条该消息相同的结果**

**乐观锁机制**

![](https://haicoder.net/uploads/pic/mq/rabbitmq/rabbitmq-advanced/15_RabbitMQ%E6%B6%88%E6%81%AF%E8%A1%A5%E5%81%BF.png)

------



### RabbitMQ集群搭建

#### RabbitMQ高可用集群搭建

摘要：实际生产应用中都会采用消息队列的集群方案，如果选择RabbitMQ那么有必要了解下它的集群方案原理

一般来说，如果只是为了学习RabbitMQ或者验证业务工程的正确性那么在本地环境或者测试环境上使用其单实例部署就可以了，但是出于MQ中间件本身的可靠性、并发性、吞吐量和消息堆积能力等问题的考虑，在生产环境上一般都会考虑使用RabbitMQ的集群方案。

##### 集群方案的原理

RabbitMQ这款消息队列中间件产品本身是基于Erlang编写，Erlang语言天生具备分布式特性（通过同步Erlang集群各节点的magic cookie来实现）。因此，RabbitMQ天然支持Clustering。这使得RabbitMQ本身不需要像ActiveMQ、Kafka那样通过ZooKeeper分别来实现HA方案和保存集群的元数据。集群是保证可靠性的一种方式，同时可以通过水平扩展以达到增加消息吞吐量能力的目的。

![1565245219265](.\pic\1566073768274.png)


##### 单机多实例部署

由于某些因素的限制，有时候你不得不**在一台机器上去搭建一个rabbitmq集群**，这个有点类似zookeeper的单机版。真实生成环境还是要配成多机集群的。有关怎么配置多机集群的可以参考其他的资料，这里主要论述如何在单机中配置多个rabbitmq实例。

主要参考官方文档：https://www.rabbitmq.com/clustering.html

首先确保RabbitMQ运行没有问题

```shell
rabbitmqctl status
```

停止rabbitmq服务

```shell
service rabbitmq-server stop
```

启动第一个节点：

```shell
RABBITMQ_NODE_PORT=5673 RABBITMQ_NODENAME=rabbit1 rabbitmq-server start
```

【注】5672端口给HAProxy使用，访问15672端口的web控制台**只能登录guest用户了**

启动第二个节点：

> web管理插件端口占用,所以还要指定其web插件占用的端口号。
>
> 要访问的话记得提前关闭Linux防火墙

```shell
RABBITMQ_NODE_PORT=5674 RABBITMQ_SERVER_START_ARGS="-rabbitmq_management listener [{port,15674}]" RABBITMQ_NODENAME=rabbit2 rabbitmq-server start
```

结束命令：

```shell
rabbitmqctl -n rabbit1 stop
rabbitmqctl -n rabbit2 stop
```

rabbit1操作作为主节点：

```shell
rabbitmqctl -n rabbit1 stop_app  
rabbitmqctl -n rabbit1 reset	 
rabbitmqctl -n rabbit1 start_app
```

rabbit2操作为从节点：

```shell
rabbitmqctl -n rabbit2 stop_app
rabbitmqctl -n rabbit2 reset
rabbitmqctl -n rabbit2 join_cluster rabbit1@'super'         #'super'是主机名，换成自己的
rabbitmqctl -n rabbit2 start_app
```

查看集群状态：

```shell
rabbitmqctl cluster_status -n rabbit1
```

web监控：

![1566065096459](.\pic\1566065096459.png)

------



##### 集群管理

**rabbitmqctl join_cluster {cluster_node} [–ram]**
将节点加入指定集群中。在这个命令执行前需要停止RabbitMQ应用并重置节点。

**rabbitmqctl cluster_status**
显示集群的状态。

**rabbitmqctl change_cluster_node_type {disc|ram}**
修改集群节点的类型。在这个命令执行前需要停止RabbitMQ应用。

**rabbitmqctl forget_cluster_node [–offline]**
将节点从集群中删除，允许离线执行。

**rabbitmqctl update_cluster_nodes {clusternode}**

在集群中的节点应用启动前咨询clusternode节点的最新信息，并更新相应的集群信息。这个和join_cluster不同，它不加入集群。考虑这样一种情况，节点A和节点B都在集群中，当节点A离线了，节点C又和节点B组成了一个集群，然后节点B又离开了集群，当A醒来的时候，它会尝试联系节点B，但是这样会失败，因为节点B已经不在集群中了。

**rabbitmqctl cancel_sync_queue [-p vhost] {queue}**
取消队列queue同步镜像的操作。

**rabbitmqctl set_cluster_name {name}**
设置集群名称。集群名称在客户端连接时会通报给客户端。Federation和Shovel插件也会有用到集群名称的地方。集群名称默认是集群中第一个节点的名称，通过这个命令可以重新设置。

------



#### RabbitMQ镜像集群配置

> 上面已经完成RabbitMQ默认集群模式，但并不保证队列的高可用性
>
> 尽管交换机绑定这些可以复制到集群里的任何一个节点，但是队列内容不会复制。虽然该模式解决一项目组节点压力，但队列节点宕机直接导致该队列无法应用，只能等待重启，所以要想在队列节点宕机或故障也能正常应用，就要复制队列内容到集群里的每个节点，必须要创建镜像队列。
>
> 镜像队列是基于普通的集群模式的，然后再添加一些策略，所以你还是得先配置普通集群，然后才能设置镜像队列，我们就以上面的集群接着做。

**设置的镜像队列可以通过开启的网页的管理端Admin->Policies，也可以通过命令。**

```shell
rabbitmqctl set_policy my_ha "^" '{"ha-mode":"all"}'
```

![1566072300852](.\pic\1566072300852.png)

> - Name：策略名称
> - Pattern：匹配的规则，如果是匹配所有的队列，是^.
> - Definition：使用ha-mode模式中的all，也就是同步所有匹配的队列。问号链接帮助文档。

------



#### 负载均衡-HAProxy

HAProxy提供高可用性、负载均衡以及基于TCP和HTTP应用的代理，支持虚拟主机，是免费、快速并且可靠的一种解决方案

包括Twitter，Reddit，StackOverflow，GitHub在内的多家知名互联网公司在使用

HAProxy实现了一种事件驱动、单一进程模型，此模型支持非常大的并发连接数。

###### 安装HAProxy

```shell
#下载依赖包
yum install gcc vim wget
#上传haproxy源码包 - 拖拽文件到FinalShell
#解压
tar -zxvf haproxy-1.6.5.tar.gz -C /usr/local
#进入目录、进行编译、安装
cd /usr/local/haproxy-1.6.5
make TARGET=linux31 PREFIX=/usr/local/haproxy
make install PREFIX=/usr/local/haproxy
#赋权
groupadd -r -g 149 haproxy
useradd -g haproxy -r -s /sbin/nologin -u 149 haproxy
#创建haproxy配置文件
mkdir /etc/haproxy
vim /etc/haproxy/haproxy.cfg
```

------




###### 配置HAProxy

配置文件路径：/etc/haproxy/haproxy.cfg

```shell
#logging options
global
	log 127.0.0.1 local0 info
	maxconn 5120
	chroot /usr/local/haproxy
	uid 99
	gid 99
	daemon
	quiet
	nbproc 20
	pidfile /var/run/haproxy.pid

defaults
	log global
	
	mode tcp

	option tcplog
	option dontlognull
	retries 3
	option redispatch
	maxconn 2000
	contimeout 5s
   
     clitimeout 60s

     srvtimeout 15s	
#front-end IP for consumers and producters

#上面配置不需要管 但下面开始的配置就是我们可以更改的地方
listen rabbitmq_cluster    #rabbitmq_cluster为名称,可以随意更改
	bind 0.0.0.0:5672      #5672就是设置的对外提供代理服务的端口
	
	mode tcp
	#balance url_param userid
	#balance url_param session_id check_post 64
	#balance hdr(User-Agent)
	#balance hdr(host)
	#balance hdr(Host) use_domain_only
	#balance rdp-cookie
	#balance leastconn
	#balance source //ip
	
	balance roundrobin
	
        server node1 127.0.0.1:5673 check inter 5000 rise 2 fall 2   #节点对应的ip+端口可以更改
        server node2 127.0.0.1:5674 check inter 5000 rise 2 fall 2	 #节点对应的ip+端口可以更改

listen stats
	bind 192.168.xxx.xxx:8100    #8100为HAProxy提供的默认的监控访问端口 ip为服务所在ip
	mode http
	option httplog
	stats enable
	stats uri /rabbitmq-stats
	stats refresh 5s
```

启动HAproxy负载

```shell
/usr/local/haproxy/sbin/haproxy -f /etc/haproxy/haproxy.cfg
#查看haproxy进程状态
ps -ef | grep haproxy

#访问如下地址对mq节点进行监控
http://192.168.88.92:8100/rabbitmq-stats
```

代码中访问mq集群地址，则变为访问haproxy地址:5672（记得开放linux中的8100端口）



代码访问案例

```java
public class Producer {
    public static void main(String[] args) throws Exception {
        //简单模式 - 无需设置Exchange交换机
        //1. 创建连接工厂
        ConnectionFactory connectionFactory = new ConnectionFactory();
        //2. 设置参数
        connectionFactory.setHost("192.168.xxx.xxx");//HAProxy的ip        默认值：localhost
        connectionFactory.setPort(5672);           //HAProxy的端口       默认值：5672
        //3. 创建Connection
        Connection connection = connectionFactory.newConnection();
        //4. 创建Channel
        Channel channel = connection.createChannel();
        //5. 创建Queue
        channel.queueDeclare("wyh-helloWorld-queue",true,false,false,null);
        //6. 发送消息到Queue
        channel.basicPublish("","wyh-helloWorld-queue",null,"WangYihuan Will Change The World!".getBytes());
        //7. 释放资源
        channel.close();
        connection.close();
        System.out.println("Send message success");
    }
}
```

------

> © 2023 iWyh2
