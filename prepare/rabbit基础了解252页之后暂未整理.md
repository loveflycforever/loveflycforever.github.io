MQ 高效可靠、平台无关

MOM 面向消息的

在C端和S端之间提供同步和异步的连接， 并且在任何时刻都可以将消息进行传送或者存储转发，这也是它比远程过程调用的更加进步的原因


解耦、冗余（存储）、扩展性、削峰、可恢复性、顺序保证（一定程度）、缓冲、异步通信


JMS，通过提供公共 Java API 的方式，隐藏单独 MQ 产品供应商提供的实际接口，即使用单独标准化接口来胶合众多不同的接口。


AMQP，是应用层协议的一个开放标准，以解决众多消息中间件的需求和拓扑结构问题

RabbitMQ 最初版本实现了 AMQP 个关键特性:使用协议本身就可以对队列和交换器（Exchange) 这样的资源进行配置

消息一般可以包含两个部分:消息体（payload）和标签（Label） 

生产者 封装成消息发送 （AMQP 协议里这个动 作对应的命令为 Basic . Publish)
消费者订阅并接收消息 CAMQP 协议里这个 动作对应的命令为 Basic.Co口surne 或者 Basic. Get)，


Queue: 队列，是 RabbitMQ 的内部对象，用于存储消息。RabbitMQ 中消息都只能存储在队列中，这一点和 Katka 这种消息中间件相反。 


Katka 将消 息存储在 topic C主题)这个逻辑层面，而相对应的队列逻辑只是 topic 实际存储文件中的位移 标识。


多个消费者可以订阅同一个队列，这时队列中的消息会被平均分摊CRound-Robin，即轮询) 给多个消费者进行处理，而不是每个消费者都收到所有的消息井处理

RabbitMQ 不支持     队列层面      的广播消费

实际上 在 RabbitMQ 中 不会发生生产者将消息投递到队列中    

生产者将消息发送到 Exchange (交换器，通常也 可以用大写的 "X" 来表示)，由交换器将消息路由到一个或者多个队列中



如果路由不到，或 许会返回给生产者，或许直接丢弃。


RabbitMQ 中的交换器有四种类型，不同的类型有着不同的路由策略


生产者将消息发给交换器的时候， 一般会指定一个 RoutingKey，用 来指定这个消息的路由规则，而这个 RoutingKey 需要与交换器类型和绑定键 (BindingKey) 联 合使用才能最终生效。


在交换器类型和绑定键 (BindingKey) 固定的情况下，生产者可以在发送消息给交换器时，
通过指定 RoutingKey 来决定消息流向哪里。


 RabbitMQ 中通过绑定（Binding）将交换器与队列关联起来，在绑定的时候一般会指定一个绑定键 (BindingKey)，就知道如何正确地将消息路由到队列。BindingKey 其实也属于路由键中的一种，官方解释为: the routing key to use for the binding。 可以翻译为:在绑定的时候使用的路由键。

生产者将消息发送给交换器时， 需要一个 RoutingKey， 当 BindingKey 和 RoutingKey 相匹 配时， 消息会被路由到对应的队列中。在绑定多个队列到同一个交换器的时候， 这些绑定允许 使用相同的 BindingKey0 BindingKey 并不是在所有的情况下都生效，它依赖于交换器类型， 比 如。在 direct 交换器类型下， RoutingKey 和 BindingKey 需要完全匹配才能使用，在 fanout 类型的交换器就会无视 BindingKey， 而是将消息路由到所有绑定到该交换器的队列中 。是在 topic 交换器类型下， RoutingKey 和 BindingKey 之间需要做模糊匹配，两者并不是相同的。

 交换器相当于投递包裹的邮箱， RoutingKey 相当于填写在包裹上的 地址， BindingKey 相当于包裹的 目 的地， 当填写在包裹上的地址和实际想要投递的地址相匹配 时， 那么这个包裹就会被正确投递到 目 的地， 最后这个目 的地的"主人"一一队列可以保留这 个包裹。如果填写的地址出错，邮递员不能正确投递到目的地， 包裹可能会回退给寄件人，也 有可能被丢弃。

在某些情形下， RoutingKey 与 BindingKey 可以看作同一个东西。 可以这么理解:
在使用绑定的时候，其中需要的路由键是 BindingKey 
在发送消息的时候，其中需要的路由键是 RoutingKey 

RabbitMQ 常用的交换器类型有 fanout、 direct、 topic、 headers 这四种。 AMQP 协议里还提
到另外两种类型: System 和自定义


fanout 它会把所有发送到该交换器的消息路由到所有与该交换器绑定的队列中。

direct 类型的交换器路由规则也很简单，它会把消息路由到那些 BindingKey 和 RoutingKey
完全匹配的队列中。

topic 类型的交换器在匹配规则上进行了扩展，它与 direct 类型的交换器相似，也是将消息路由到 BindingKey RoutingKey 相匹配的队列中
约定:
BindingKey RoutingKey 是点号"."分隔的字符串;
BindingKey 中可以存在两种特殊字符串"*"和"#"，用于做模糊匹配，其中"*"用于匹配1个单词，"#"用于匹配多规格单词(可以是零个)。
没有匹配任何路由键的消息将会被丢弃或者返回给生产者(需要设置mandatory 参数)

headers 类型的交换器不依赖于路由键的匹配规则来路由消息，而是根据发送的消息内容中的 headers 属性进行匹配。、
RabbitM 会获取到该消息的 headers (也是一个键值对的形式) ，对比其中的键值对是否完全匹配队列和交换器绑定时指定的键值对，如果完全匹配则消息会路由到该队列
headers 类型的交换器性能会很差，而且也不实用，基本上不会看到它的存在。


无论是生产者还是消费者，都需要和 RabbitMQ Broker 建立连接，这个连接就是一条 TCP 连接，也就是Connection， TCP 连接建立起来，客户端紧接着可以创建一个 AMQP 信道 (Channel) ，每个信道都会被指派一个唯一ID 。信道是建立在 Connection 之上的虚拟连接， RabbitMQ 处理的每条 AMQP 指令都是通过信道完成的。

RabbitMQ 采用类似 NIO (Non-blocking 1/0) 的做法，选择 TCP 连接复用，不仅可以减少性能开销，同时也便于管理


AMQP 协议本身包括三层。
~ Module Layer: 位于协议最高层，主要定义了一些供客户端调用的命令，客户端可以利
用这些命令实现自己的业务逻辑。例如，客户端可以使用 Queue Declare 命令声明
个队列或者使用 Basic.Consume 订阅消费一个队列中的消息。
~ Session Layer: 位于中间层，主要负责将客户端的命令发送给服务器，再将服务端的应
答返回给客户端，主要为客户端与服务器之间的通信提供可靠性同步机制和错误处理。
~ Transport Layer: 位于最底层，主要传输二进制数据流，提供帧的处理、信道复用、错
误检测和数据表示等。

AMQP 说到底还是 个通信协议，通信协议都会涉及报文交互，从 low-level 举例来说，
AMQP 本身是应用层的协议，其填充于 TC 协议层的数据部分。而从 high-level 来说， AMQP 
是通过协议命令进行交互的。 AMQP 协议可以看作一系列结构化命令的集合，这里的命令代表
一种操作，类似于 HTT 中的方法 (GET POST PUT DELETE 等)

|名称|是否包含内容体|对应客户端中的方法|简要描述|
|:--:|:--:|:--:|:--:|
|Connection.start|否|factory.newConnection|建立连接相关|
|Connection.Start-Ok|否|同上|同上|
|Connection.Tune|否|同上|同上|
|Connection.Tune-Ok|否|同上|同上|
|Connection.Open|否|同上|同上|
|Connection.Open-Ok|否|同上|同上|
|Connection.Close|否|connection.close|关闭连接|
|Connection.Close-Ok|否|同上|同上|
|Channel.Open|否|connection.openChannel|开启信道|
|Channel.Open-Ok|否|同上|同上|
|Cbannel.Close|否|channel.close|关闭信道|
|Channel.Close-Ok|否|同上|同上|
|Exchange.Declare|否|channel.exchangeDeclare|声明交换器|
|Exchange.Declare-Ok|否|同上|同上|
|Exchange.Delete|否|channel.exchangeDelete|删除交换器|
|Exchange.Delete-Ok|否|向上|同上|
|Exchange.Bind|否|channel.exchangeBind|交换器与交换器绑定|
|Exchange.Bind-Ok|否|同上|同上|
|Exchange.Unbind|否|channel.exchangeUnbind|交换器与交换器解绑|
|Exchange.Unbind-Ok|否|同上|同上|
|Queue.Declare|否|channel.queueDeclare|声明队列|
|Queue.Declare-Ok|否|同上|同上|
|Queue.Bind|否|channel.queueBind|队列与交换器绑定|
|Queue.Bind-Ok|否|同上|同上|
|Queue.Purge|否|channel.queuePurge|清除队列中的内容|
|Queue.Purge-Ok|否|同上|同上|
|Queue.Delete|否|channel.queueDelete|删除队列|
|Queue.Delete-Ok|否|同上|向上|
|Queue.Unbind|否|channel.queueUnbind|队列与交换器解绑|
|Queue.Unbind-Ok|否|同上|同上|
|Basic.Qos|否|channel.basicQos|设置未被确认消费的个数|
|Basic.Qos-Ok|否|同上|同上|
|Basic.Consume|否|channel.basicConsume|消费消息(推模式)|
|BasiιConsurne-Ok|否|同上|同上|
|Basic.Cancel|否|channel.basicCancel|取消|
|Basic.Cancel-Ok|否|同上|同上|
|Basic.Publish|是|channel.basicPublish|发送消息|
|Basic.Retur咀|是|无|未能成功路由的消息返回|
|Basic.Deliver|是|无|Broker|推送消息|
|Basic.Get|否|channel.basicGet|消费消息(拉模式)|
|Basic.Get-Ok|是|同上|同上|
|Basic.Ack|否|channel.basicAck|确认|
|Basic.Reject|否|channel.basicReject|拒绝(单条拒绝)|
|Basic.Recover|否|channel.basicRecover|请求Broker重新发送未被确认的消息|
|Basic.Recover-Ok|否|向上|同上|
|Basic.Nack|否|channel.basicNack|拒绝(可批量拒绝)|
|Tx.Select|否|channel.txSelect|开启事务|
|TX.Select-Ok|否|同上|同上|
|Tx.Cornmit|否|channel.txCommit|事务提交|
|TX.Commit-Ok|否|同上|同上|
|Tx.Rollback|否|channel.txRollback|事务回滚|
|TX.Rollback-Ok|否|同上|同上|
|Confirrn.Select|否|channel.confinnSelect|开启发送端确认模式|
|Confinn.Select-Ok|否|同上|同上|



 RabbitMQ 提供了消息确认机制( message acknowledgement)。 消费者在订阅队列时，可以指定 autoAck 参数，当 autoAck 等于 false 时， RabbitMQ 会等待消费者显式地回复确认信号后才从内存(或者磁盘)中移去消息(实质上
是先打上删除标记，之后再删除)。当 autoAck 等于 true 时， RabbitMQ 会自动把发送出去的 消息置为确认，然后从内存(或者磁盘)中删除，而不管消费者是否真正地消费到了这些消息。



mandatory 和 immediate 是 channel . basicPublish 方法中的两个参数
当 mandatory 参数设为 true 时，交换器无法根据自身的类型和路由键找到一个符合条件 的队列，那么 RabbitMQ 会调用 Basic.Return 命令将消息返回给生产者,反之丢弃

生产者可以通过调用 channel . addReturnListener 来添加 ReturnListener 监昕器实现。


当 imrnediate 参数设为 true 时，如果交换器在将消息路由到队列时发现队列上并不存在 任何消费者，那么这条消息将不会存入队列中。当与路由键匹配的所有队列都没有消费者时， 该消息会通过 Basic . Return 返回至生产者。



RabbitMQ 提供的备份交换器
(Altemate Exchange) 可以将未能被交换器路由的消息(没有绑定队列或者没有匹配的绑定〉存 储起来，而不用返回给客户端。

Map<String, Object> args = new HashMap<String, Object>(); args.put("a1ternate-exchange", "myAe"); channe1.exchangeDec1are("norma1Exchange", "direct", true, fa1se, args); channe1 . exchangeDec1are("myAe ", "fanout", true, fa1se, nu11) ; channe1 .queueDec1are("norma1Queue" , true, fa1se, fa1se, nu11); channe1 .queueB工nd( " norma1Queue " ， "norma1Exchange", "norma1Key"); channe1 .queueDec1are( "unroutedQueue" , true, fa1se, fa1se, nu11); channel .queueBind( "unroutedQueue", "myAe", ""); 

需要注意的是，消息被重新发送到 备份交换器时的路由键和从生产者发出的路由键是一样的。建议设置备份交换器为 fanout 类型

如果备份交换器和 mandatory 参数一起使用，那么 mandatory 参数无效。


加入 x-message-ttl 参数实现的设置消息的 TTL，这个参数的单位是毫秒

第一种方法是通过队列属性设置，队列中所有消息 都有相同的过期时间。第二种方法是对消息本身进行单独设置，每条消息的 TTL 可以不同。如 果两种方法一起使用，则消息的 TTL 以两者之间较小的那个数值为准。


DLX，全称为 Dead-Letter-Exchange，可以称之为死信交换器，当
消息在一个队列中变成死信 (dead message) 之后，它能被重新被发送到另一个交换器中，这个
交换器就是 DLX，绑定 DLX 的队列就称之为死信队列。
第一种设置队列 TTL 属性的方法，一旦消息过期，就会从队列中抹去，而在第二种方 法中，即使消息过期，也不会马上从队列中抹去，因为每条消息是否过期是在即将投递到消费
者之前判定的。

设置 x-dead-letter-exchange 参数来为这 个队列添加 DLX

```
channel . exchangeDeclare("dlx_exchange" , "direct"); //创建 DLX: dlx_exchange Map<String, Object> args = new HashMap<String, Object>(); args .put("x-dead-letter-exchange", " dlx exchange "); //为队列 myqueue 添加 DLX channel . queueDeclare("myqueue", false, false, false, args);
```


可以为这个 DLX 指定路由键，如果没有特殊指定，则使用原队列的路由键:

```
args.put("x-dead-letter-routing-key", "dlx-routing-key"); 
```


 DLX 配合 TTL 使用可以实现延迟队列的功能

延迟队列存储的对象是对应的延迟消息，所谓"延迟消息"是指当消息被发送以后，并不
想让消费者立刻拿到消息，而是等待特定时间后，消费者才能拿到这个消息进行消费。


假设一个应用中需要将每条消息都设置为 10 秒的延迟， 生产者通过 exchange.normal 这个交换器将发送的消息存储在 queue.normal 这个队列中。消费者 订阅的并非是 queue.normal 这个队列，而是 queue.dlx 这个队列。当消息从 queue.normal 这个队
列中过期之后被存入 queue.dlx 这个队列中，消费者就恰巧消费到了延迟 10 秒的这条消息。



设置队列的 x-max-priority 参数来实现优先级队列

```
Map<String, Object> args =口ew HashMap<String, Object>() ; args . put( "x-rnax-priority" , 10) ; channel.queueDeclare("queue . priority" , true, fa1se, false, args) ; 
```

如果在消费者的消费速度大于生产者的速度
且 Broker 中没有消息堆积的情况下， 对发送的消息设置优先级也就没有什么实际意义。


 RPC 的协议有很多 ，比如最早的 CORBA、 Java RMI, WebService 的 RPC 风格、 Hessian、
Thri负 甚至还有 Restful API。


 RPC 的主要功用是让构建分布式计算更容易，
在提供强大的远程调用能力时不损失本地调用的语义简洁性。


```
String callbackQueueName = channel.queueDeclare() . getQueue(); BasicProperties props = new BasicProperties .Bu工 lder() . replyTo(callbackQueueName) .build(); channel.bas 工 cPubl工 sh( "" ， " rpc queue" , props,message.getBytes()) ; // then code to read a respo口se message from the callback_queue... 
```
~ replyTo: 通常用来设置一个回调队列。
~ correlationId: 用来关联请求 (request) 和其调用 RPC 之后的回复 (response)。

为每个 RPC 请求创建一个回调队列，则是非常低效的。通用的解决方案一一可以为每个客户端创建一个单一的回调队列。

```
String corrld = UUID . randomUUID () . toString (); 
BasicProperties props = new BasicProper ties . Bui lder () . correlationld (corrld) .replyTo(replyQueueName) . bu工 ld () ; channel.basicPublish ("" , request Que ueName, prop s , message .getBytes ()); 
while (true) { QueueingConsumer. Delivery delivery = consumer . nextDel ivery () ; if(delivery .getProperties () . getCorrelationld () .equals(corrld) ) { response = new String ( del工very . getBody ()) ; break; 
```
。。。。


持久化分为三个部分:交换器的持久化、队列的持久化和消息的持久化

交换器的持久化是通过在声明队列是将 durable 参数置为 true 实现
    器不设置持久化，那么在 RabbitMQ 服务重启之后，相关的交换器元数据会丢失， 不过消息不会丢失，只是不能将消息发送到这个交换器中了
    队列的持久化是通过在声明队列时将 durable 参数置为 true 实现
    在 RabbitMQ 服务重启之后，相关队列的元数据会丢失，
    此时数据也会丢失
    消息的投递模式 (BasicProperties 中的 deliveryMode 属性)设置为 2 即可实现消息的持久化
    单单设置消息持久化而不设置队列的持久化显得毫无意义。



首先从消费者来说，如果在订阅消费队列时将 autoAck 参数设置为 true，那么 当消费者接
    收到相关消息之后，还没来得及处理就看机了，这样也算数据丢失。这种情况很好解决，将
    autoAck 参数设置为 false， 并进行手动确认，详细可以参考 3.5 节。
    其次，在持久化的消息正确存入 RabbitMQ 之后，还需要有一段时间(虽然很短，但是不 可忽视〉才能存入磁盘之中。 RabbitMQ 并不会为每条消息都进行同步存盘(调用内核的 fsync 方法)的处理，可能仅仅保存到操作系统缓存之中而不是物理磁盘之中。如果在这段时间内 RabbitMQ 服务节点发生了岩机、重启等异常情况，消息保存还没来得及落盘，那么这些消息将
    会丢失。
    这个问题怎么解决呢?这里可以引入 RabbitMQ 的镜像队列机制(详细参考 9.4节)，相当
    于配置了副本，如果主节点 Cmaster) 在此特殊时间内挂掉，可以自动切换到从节点 Cslave ), 这样有效地保证了高可用性，除非整个集群都挂掉。虽然这样也不能完全保证 RabbitMQ 消息 不丢失，但是配置了镜像队列要比没有配置镜像队列的可靠性要高很多，在实际生产环境中的 关键业务队列一般都会设置镜像队列。
    还可以在发送端引入事务机制或者发送方确认机制来保证消息己经正确地发送并存储至 RabbitMQ 中，前提还要保证在调用 channel.basicPublish 方法的时候交换器能够将消息
    正确路由到相应的队列之中


fsync 在 Linux 中的意义在于同步数据到存储设备上。大多数块设备的数据都是通过缓存进行的，将数据写到文件上通常将该 数据由内核复制到缓存中，如果缓存尚未写满，则不将其排入输出队列上，而是等待其写满或者当内核需要重用该缓存时，再 将该缓存排入输出队列，进而同步到设备上。 这种策略的好处是减少了磁盘读写次数，不足的地方是降低了文件内容的更新速 度，使其不能时刻同步到存储设备上， 当系统发生故障时，这种机制很有可能导致了文件内容的丢失。因此，内核提供了 fsync 接口，用户可以根据自己的需要通过此接口更新数据到存储设备上.


生产者确认的两种解决方式:
令  通过事务机制实现:
令  通过发送方确认 Cpublisher confirm) 机制实现。


与事务机制相关的方法有 三 个: channel . txSelect 、 channel . txCommit 和 channel . txRollbacko channel.txSelect 用于将当前的信道设置成事务模式. channel . txCommit 用于提交事务. channel . txRollback 用于事务回 滚。在通过 channel.txSelect 方法开启事务之后，我们便可以发布消息给 RabbitMQ 了， 如果事务提交成功，则消息一定到达了 RabbitMQ 中，如果在事务提交执行之前由于 RabbitMQ 异常崩溃或者其他原因抛出异常，这个时候我们便可以将其捕获，进而通过执行
channel . txRollback 方法来实现事务回夜。注意这里的 RabbitMQ 中的事务机制与大多数数据库中的事务概念井不相同，需要注意区分。
事务机制在一条消息发送之后会使发送端阻塞，以等待 RabbitMQ 的回应，之后才能继续 发送下一条消息。
所以使用事务机制会"吸干" RabbitMQ 的性能，



生产者将信道设置成 confmn C确认)模式，一旦信道进入 confmn 模式，所有在该信道上
面发布的消息都会被指派一个唯一的 IDC从 l 开始)，一旦消息被投递到所有匹配的队列之后， RabbitMQ 就会发送一个确认 CBasic.Ack) 给生产者(包含消息的唯一 ID)，这就使得生产 者知晓消息已经正确到达了目的地了。如果消息和队列是可持久化的，那么确认消息会在消息
写入磁盘之后发出。 RabbitMQ 回传给生产者的确认消息中的 deliveryTag 包含了确认消息
的序号，此外 RabbitMQ 也可以设置 channel . basicAck 方法中的 multiple 参数，表示到 这个序号之前的所有消息都己经得到了处理

 发送方确认机制最大的好处在于它是异步的，一旦发布一条消息，
生产者应用程序就可以在等信道返回确认的同时继续发送下一条消息，当消息最终得到确认之 后，生产者应用程序便可以通过回调方法来处理该确认消息，如果 RabbitMQ 因为自身内部错 误导致消息丢失，就会发送一条 nack CBasi c . Nack) 命令，生产者应用程序同样可以在回调 方法中处理该 nack 命令。

publisher confmn 的优势在于并不一定需要同步确认。 因为相较于事务机制只是少了一个命令帧报文的交互，只能略微提高QPS。

批量 confirm 方法:每发送一批消息后，调用 channel.waitForConfirms 方法，等
待服务器的确认返回 。
异步 confirm 方法:提供一个回调方法，服务端确认了一条或者多条消息后客户端会因
调这个方法进行处理。

批量 confmn 方法中的问题在于出现返回 Basic.Nack 或 者超时情况时，客户端需要将这一批次的消息全部重发，这会带来明显的重复消息数量，并且 当消息经常丢失时，批量 confirm 的性能应该是不升反降的。

异步 confmn 方法的编程实现较为复杂。在客户端 Channel 接口中提供的
addConfirmListener 方法可以添加 ConfirmListener 这个回调接口，这个 ConfirmListener 接口包含两个方法 : handleAck 和 handleNack ，分别用来处理 RabbitMQ 回传的 Basic.Ack 和 Basic.Nack 。在这两个方法中都包含有 一 个参数 deli veryTag (在 publisher confirm 模式下用来标记消息的唯一有序序号)。


事务机制和 publisher confirm机制两者是互斥的，不能共存。 
事务和忡忡口 publisher confum 机制确保的是消息能够正确地发送至 RabbitMQ，这里的 "友送至 RabbitMQ" 的含义是指消息被正确地发往至 RabbitMQ 的交换器，如果此交换器没有
匹配的队列，那么消息也会丢失。所以在使用这两种机制的时候要确保所涉及的交换器能够有 匹配的队列 . 更进一步地讲，发送方要配合 mandatory 参数或者备份交换器一起使用来提高 消息传输的可靠性。


当 RabbitMQ 队列拥有多个消费者时，队列收到的消息将以轮询 (round-robin)的分发方式
发送给消费者。 channel.basicQos 方法允许限制信道上的消费者所能保持的最大 未确认消息的数量。
 RabbitMQ 会保存一个消费者的列表，每发送一条消息都会为对应的消
费者计数，如果达到了所设定的上限，那么 RabbitMQ 就不会向这个消费者再发送任何消息。 直到消费者确认了某条消息之后 ， RabbitMQ将相应的计数减1，之后消费者可以继续接收消息， 直到再次到达计数上限。这种机制可以类比于 TCP!IP中的"滑动窗口"。


 仅凭借RabbitMQ 无法保证消息的顺序性


 Basic.Qos 的使用对于拉模式的消费方式无效.
            一定要在调用 Basic . Consume 之前调用 Basic.Qos
            才能生效。

            当 prefetchCount 参数 设置为 0 则表示 没有上限。
            还有 prefetchSize 这个参数表示消费者所能接收未确认消息的总体大小的上限，单位为 B，设置为 0 则表示没有上限。
            RabbitMQ 为了 提升相关的性能，在 AMQPO-9-1 协议之上重新定义了 global 这个参数
            global 参数 AMQP 0-9-1 RabbitMQ
            false 信道上所有的消费者都需要遵从 prefetchCount 的限定值 信道上新的消费者需要遵从 prefetchCount 的限定值，即每个消费者上限一致
            true 当前通信链路( Connection) 上所有的消费者都需要遵从 prefetchCount 的限定值 信道上所有的消费者都需要遵从 prefetchCount 的限定值，即所有消费者上限总和
            默认 false

                102-104 QueueingConsumer 的不益之处

            一般消息中间件的消息 传输保障分为三个层级。
            At most once: 最多一次。消息可能会丢失，但绝不会重复传输。
            At least once: 最少一次。消息绝不会丢失，但可能会重复传输。
            Exactly once: 恰好一次。每条消息肯定会被传输一次且仅传输一次。
            RabbitMQ 支持其中的"最多一次"和"最少一次"。


                提升数据可靠性有以下一些途径: 设置 mandatory 参数或者备份交换器 (immediate 参数己被陶汰);设置 publisher conflITll机制或者事务机制;设置交换器、队列和消息都为持久 化;设置消费端对应的 autoAck 参数为 false 井在消费完消息之后再进行消息确认。

            TTL、死信队列、延迟队列、优先级队列、 RPC 功能

============================================================================================

vhost

============================================================================================

高级


持久化的消息在到达队列
时就被写入到磁盘，并且如果可以，持久化的消息也会在内存中保存一份备份，这样可以提高 一定的性能，当内存吃紧的时候会从内存中清除。
非持久化的消息一般只保存在内存中 ，在内
存吃紧的时候会被换入到磁盘中，以节省内存空间。

这两种类型的消息的落盘处理都在
RabbitMQ 的"持久层"中完成。

持久层是一个逻辑上的概念，实际包含两个部分: 队列索引 Crabbit queue index) 和 消息存储 (rabbit_msg_store)o rabbit_queue_index 负责维护队列中落盘消息的信息，包 括消息的存储地点、是否己被交付给消费者、是否己被消费者 ack 等。


每个队列都有与之对应的一 个 rabbit queue indexorabbit msg store 以键值对的形式存储消息，它被所有队列共享，
在每个节点中有且只有一个。

从技术层面上来说， rabbit_msg_store 具体还可以分为 msg store persistent 和 msg store transient, 
msg store persistent 负责持久 化消息的持久化，重启后消息不会丢失; 
msg store transient 负责非持久化消息的持久化， 重启后消息会丢失。
