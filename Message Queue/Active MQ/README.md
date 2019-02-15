#### 备忘
```txt
介绍：
  ActiveMQ 是Apache出品，最流行的，能力强劲的开源消息总线。
  ActiveMQ 是一个完全支持JMS1.1和J2EE 1.4规范的 JMS Provider实现
  尽管JMS规范出台已经是很久的事情了，但是JMS在当今的J2EE应用中间仍然扮演着特殊的地位。
  消息队列中间件是分布式系统中重要的组件，主要解决应用耦合，异步消息，流量削锋等问题。
  消息队列是实现高性能，高可用，可伸缩和最终一致性架构。是大型分布式系统不可缺少的中间件。
  目前在生产环境，使用较多的消息队列有 ActiveMQ，RabbitMQ，ZeroMQ，Kafka，MetaMQ，RocketMQ等。
  消息通讯是指，消息队列一般都内置了高效的通信机制，因此也可以用在纯的消息通讯。比如实现点对点消息队列，或聊天室等
  
  Kafka：    接收用户日志的消息队列。
  Logstash： 做日志解析，统一成JSON输出给Elasticsearch。
  Elasticsearch：  实时日志分析服务的核心，一个schemaless，实时的数据存储，通过index组织数据，兼具强大的搜索和统计
  Kibana： 基于Elasticsearch的数据可视化组件，超强的数据可视化能力是众多公司选择ELK stack的重要原因

特性列表:
  1 多种语言和协议编写客户端。语言: Java,C,C++,C#,Ruby,Perl,Python,PHP。
    应用协议： OpenWire,Stomp REST,WS Notification,XMPP,AMQP
  2 完全支持JMS1.1和J2EE 1.4规范 （持久化，XA消息，事务)
  3 对Spring的支持，ActiveMQ可以很容易内嵌到使用Spring的系统里面去，而且也支持Spring2.0的特性
  4 通过了常见J2EE服务器（如 Geronimo,JBoss 4,GlassFish,WebLogic）的测试
    其中通过JCA 1.5 resource adaptors的配置，可以让ActiveMQ可以自动的部署到任何兼容J2EE 1.4 商业服务器上
  5 支持多种传送协议：in-VM,TCP,SSL,NIO,UDP,JGroups,JXTA
  6 支持通过JDBC和journal提供高速的消息持久化...
  7 从设计上保证了高性能的集群，客户端-服务器，点对点
  8 支持Ajax
  9 支持与Axis的整合
  10 可以很容易的调用内嵌JMS provider，进行测试
```
#### JMS架构
```txt
Java 消息服务（Java Message Service，简称JMS）是用于访问企业消息系统的开发商中立的API。
企业消息系统可以协助应用软件通过网络进行消息交互。JMS 在其中扮演的角色与JDBC 很相似
正如JDBC 提供了一套用于访问各种不同关系数据库的公共API，JMS 也提供了独立于特定厂商的企业消息系统访问方式。
使用JMS 的应用程序被称为JMS 客户端
处理消息路由与传递的消息系统被称为JMS Provider
而JMS 应用则是由多个JMS 客户端和一个JMS Provider 构成的业务系统。
发送消息的JMS 客户端被称为生产者（producer）
接收消息的JMS 客户端则被称为消费者(consumer)
同一JMS 客户端既可以是生产者也可以是消费者。
JMS 的编程过程很简单，概括为：应用A发送一条消息到消息服务器（JMS Provider）的某个目得地(Destination)
然后消息服务器把消息转发给应用程序B。因为应用A和应用B没有直接的代码关连，所以两者实现了解偶。
```
#### 消息的组成
```txt
1. 头（head）
每条JMS 消息都必须具有消息头。头字段包含用于路由和识别消息的值。可以通过多种方式来设置消息头的值：
a. 由JMS 提供者在生成或传送消息的过程中自动设置
b. 由生产者客户机通过在创建消息生产者时指定的设置进行设置
c. 由生产者客户机逐一对各条消息进行设置
 
2. 属性（property）
消息可以包含称作属性的可选头字段。他们是以属性名和属性值对的形式制定的。
可以将属性是为消息头得扩展，其中可以包括如下信息：创建数据的进程、数据的创建时间以及每条数据的结构。
JMS提供者也可以添加影响消息处理的属性，如是否应压缩消息或如何在消息生命周期结束时废弃消息。
 
3. 主体（body）
包含要发送给接收应用程序的内容。每个消息接口特定于它所支持的内容类型。
JMS为不同类型的内容提供了他们各自的消息类型，但是所有消息都派生自Message接口。
  StreamMessage     一种主体中包含Java基元值流的消息。其填充和读取均按顺序进行。
  MapMessage	      一种主体中包含一组键--值对的消息。没有定义条目顺序。
  TextMessage       一种主体中包含Java字符串的消息（例如，XML消息）。
  ObjectMessage     一种主体中包含序列化Java对象的消息。
  BytesMessage      一种主体中包含连续字节流的消息。
```
#### 消息的传递模型
```txt
JMS支持两种消息传递模型：点对点（point-to-point，简称PTP）和发布/订阅（publish/subscribe,简称pub/sub）。
这两种消息传递模型非常相似，但有以下区别:
	  a. PTP消息传递模型规定了一条消息只能够传递给一个接收方。
	  b. Pub/sub消息传递模型允许一条消息传递给多个接收方
每个模型都通过扩展公用基类来实现。例如：javax.jms.Queue和Javax.jms.Topic都扩展自javax.jms.Destination类。

1. 点对点消息传递：
  通过点对点的消息传递模型，一个应用程序可以向另外一个应用程序发送消息。
  在此传递模型中，目标类型时队列。消息首先被传送至队列目标，然后从改对垒将消息传送至对此队列进行监听的某个消费者
  在此模型中，消息不是自动推动给客户端的，而是要由客户端从队列中请求获得。

2. 发布/订阅消息传递：
  通过发布/订阅消息传递模型，应用程序能够将一条消息发送到多个接收方。
  在此传送模型中，目标类型是主题。消息首先被传送至主题目标，然后传送至所有已订阅此主题的或送消费者
  主题目标也支持长期订阅。长期订阅表示消费者已注册了主题目标，但在消息到达目标时改消费者可以处于非活动状态。
  当消费者再次处于活动状态时，将会接收该消息。
  如果消费者均没有注册某个主题目标，该主题只保留注册了长期订阅的非活动消费者的消息。
  与PTP消息传递模型不同，pub/sub消息传递模型允许多个主题订阅者接收同一条消息。
  JMS一直保留消息，直至所有主题订阅者都接收到消息为止。
  在该模型中，消息会自动广播，消费者无须通过主动请求或轮询主题的方法来获得新的消息。
  
上面两种消息传递模型里，我们都需要定义消息生产者和消费者
生产者吧消息发送到JMS Provider的某个目标地址（Destination），消息从该目标地址传送至消费者。
消费者可以同步或异步接收消息，一般而言，异步消息消费者的执行和伸缩性都优于同步消息接收者，体现在：
	1. 异步消息接收者创建的网络流量比较小。
  单向对东消息，并使之通过管道进入消息监听器。管道操作支持将多条消息聚合为一个网络调用。
  
	2. 异步消息接收者使用线程比较少。
  异步消息接收者在不活动期间不使用线程。
  同步消息接收者在接收调用期间内使用线程，结果线程可能会长时间保持空闲，尤其是如果该调用中指定了阻塞超时。
  
	3.对于服务器上运行的应用程序代码，使用异步消息接收者几乎总是最佳选择，尤其是通过消息驱动Bean。
  使用异步消息接收者可以防止应用程序代码在服务器上执行阻塞操作。
  而阻塞操作会是服务器端线程空闲，甚至会导致死锁。
  阻塞操作使用所有线程时则发生死锁。如果没有空余的线程可以处理阻塞操作自身解锁所需的操作，这该操作永远无法停止阻塞。
```