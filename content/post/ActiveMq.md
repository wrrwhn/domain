+++
date = "2017-11-15T22:07:46+08:00"
title = "ActiveMq"
draft = false
tags = ["整理","ActiveMq "]
share = true
+++


## 地址
- 入门：http://www.cnblogs.com/xwdreamer/archive/2012/02/21/2360818.html
- 下载：http://activemq.apache.org/download-archives.html


## 运行

- 解压至 D:\server\activemq\apache-activemq-5.9.1
    + 注：D:\Program Files 等路径上存在空格等的不适合当其路径，会影响项目正常启动
- 点击 /bin/activemq.bat 启动 ActiveMQ 程序
- 进入 http://localhost:8161/admin/ ，并输入账号/密码 admin/admin
- 创建一个名为 FirstQueue 的 Queue
- 运行代码（如下）


## 代码
### 依赖
```
<dependency>
    <groupId>org.apache.activemq</groupId>
    <artifactId>activemq-core</artifactId>
    <version>5.5.0</version>
</dependency>
```
### 模式 - Queue
#### 发送
```
public class Sender {
    private static final int SEND_NUMBER = 5;
    public static void main(String[] args) {
        ConnectionFactory connectionFactory;
        Connection connection = null;
        Session session;
        Destination destination;
        MessageProducer producer;
        connectionFactory = new ActiveMQConnectionFactory(
                ActiveMQConnection.DEFAULT_USER,
                ActiveMQConnection.DEFAULT_PASSWORD,
                "tcp://localhost:61616");
        try {
            connection = cfile:/D:/WorkSpace/work4IDEA/HelloWorld4Maven/SpringMvc/src/main/java/com/yao/springmvc/activeMq/hello/Receiver.java
file:/D:/WorkSpace/work4IDEA/HelloWorld4Maven/SpringMvc/src/main/java/com/yao/springmvc/activeMq/hello/Sender.javaonnectionFactory.createConnection();
            connection.start();
            session = connection.createSession(Boolean.TRUE,
                    Session.AUTO_ACKNOWLEDGE);queue是一个服务器的queue，须在在ActiveMq的console配置
            destination = session.createQueue("FirstQueue");
            producer = session.createProducer(destination);
            producer.setDeliveryMode(DeliveryMode.NON_PERSISTENT);
            sendMessage(session, producer);
            session.commit();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try {
                if (null != connection)
                    connection.close();
            } catch (Throwable ignore) {
            }
        }
    }
    public static void sendMessage(Session session, MessageProducer producer)
            throws Exception {
        for (int i = 1; i <= SEND_NUMBER; i++) {
            TextMessage message = session
                    .createTextMessage("ActiveMq 发送的消息" + i);
            System.out.println("发送消息：" + "ActiveMq 发送的消息" + i);
            producer.send(message);
        }
    }
}
```
#### 接收
```
public class Receiver {
    public static void main(String[] args) {
        ConnectionFactory connectionFactory;
        Connection connection = null;
        Session session;
        Destination destination;
        MessageConsumer consumer;
        connectionFactory = new ActiveMQConnectionFactory(
                ActiveMQConnection.DEFAULT_USER,
                ActiveMQConnection.DEFAULT_PASSWORD,
                "tcp://localhost:61616");
        try {
            connection = connectionFactory.createConnection();
            connection.start();
            session = connection.createSession(Boolean.FALSE,
                    Session.AUTO_ACKNOWLEDGE)
            destination = session.createQueue("FirstQueue");
            consumer = session.createConsumer(destination);
            while (true) {
                TextMessage message = (TextMessage) consumer.receive(100000);
                if (null != message) {
                    System.out.println("收到消息" + message.getText());
                } else {
                    break;
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try {
                if (null != connection)
                    connection.close();
            } catch (Throwable ignore) {
            }
        }
    }
}
```
### 模式 - Topic
#### 发送
```
public class Sender {
    public static void main(String[] args) throws JMSException {
        ActiveMQConnectionFactory factory = new ActiveMQConnectionFactory("tcp://localhost:61616");
        Connection connection = factory.createConnection();
        connection.start();
        Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
        Topic topic = session.createTopic("myTopic.messages");
        MessageProducer producer = session.createProducer(topic);
        producer.setDeliveryMode(DeliveryMode.NON_PERSISTENT);
        for(int i =0;i<10;i++){
            TextMessage message = session.createTextMessage();
            message.setText("message_" + System.currentTimeMillis());
            producer.send(message);
            System.out.println("Sent message: " + message.getText());
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        producer.close();
        connection.close();
    }
}
```
#### 接收
```
public class Receiver {
    public static void main(String[] args) {
        ActiveMQConnectionFactory factory = new ActiveMQConnectionFactory("tcp://localhost:61616");
        try {
            Connection connection = factory.createConnection();
            connection.start();
            Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
            Topic topic = session.createTopic("myTopic.messages");
            MessageConsumer consumer = session.createConsumer(topic);
            consumer.setMessageListener(new MessageListener() {
                public void onMessage(Message message) {
                    TextMessage tm = (TextMessage) message;
                    try {
                        System.out.println("Received message: " + tm.getText());
                    } catch (JMSException e) {
                        e.printStackTrace();
                    }
                }
            });
        } catch (JMSException e) {
            e.printStackTrace();
        }
    }
}
```


## 处理机制对比
### Queue
- 概要：Point-to-Point 点对点
- 有无：Queue数据默认会在mq服务器上以文件形式保存，比如Active MQ一般保存在$AMQ_HOME\data\kr-store\data下面。也可以配置成DB存储。 - 故在发送前需要先在 ActiveMq/admin 上进行 Queue 的注册
- 完整：Queue保证每条数据都能被receiver接收。
- 丢失：Sender发送消息到目标Queue，receiver可以异步接收这个Queue上的消息。Queue上的消息如果暂时没有receiver来取，也不会丢失。
- 发收：一对一的消息发布接收策略，一个sender发送的消息，只能有一个receiver接收。receiver接收完后，通知mq服务器已接收，mq服务器对queue里的消息采取删除或其他操作。
- 效率：无明显变差情况
### Topic
- 概要：Publish Subscribe messaging 发布订阅消息
- 有无：topic数据默认不落地，是无状态的。
- 完整：并不保证publisher发布的每条数据，Subscriber都能接受到。
- 丢失：一般来说publisher发布消息到某一个topic时，只有正在监听该topic地址的sub能够接收到消息；如果没有sub在监听，该topic就丢失了。
- 发收：一对多的消息发布接收策略，监听同一个topic地址的多个sub都能收到publisher发送的消息。Sub接收完通知mq服务器。
- 效率：随监听客户端的并发增加出现明显下降

## 常用模式

### 同时发送多处
#### 发送
```
Destination destination = session.createQueue("test-queue,test-queue-foo,test-queue-bar,topic://test-topic-foo");
```
#### 接收
```
Destination destination = session.createQueue("test-queue");
Destination destinationFoo = session.createQueue("test-queue-foo");
Destination destinationBar = session.createQueue("test-queue-bar");
Destination destinationTopicFoo = session.createTopic("test-topic-foo");
MessageConsumer consumer = session.createConsumer(destination);
MessageConsumer consumerFoo = session.createConsumer(destinationFoo);
MessageConsumer consumerBar = session.createConsumer(destinationBar);
MessageConsumer consumerTopicFoo = session.createConsumer(destinationTopicFoo);
```

### 指定处理指定数量数据
#### 接收
```
final CountDownLatch latch = new CountDownLatch(1);
consumer.setMessageListener(new Subscriber(latch));
latch.await();
@Override
    public void onMessage(Message message) {
        try {
            if (message instanceof TextMessage) {
                String text = ((TextMessage) message).getText();
                if ("END".equalsIgnoreCase(text)) {
                    System.out.println("Received END message!");
                    countDownLatch.countDown();
                }
                else {
                    System.out.println("Received message:" +text);
                }
            }
        } catch (JMSException e) {
            System.out.println("Got a JMS Exception!");
        }
    }
```

### 独占消费
#### 接收
```
Queue destination = session.createQueue("test-queue?consumer.exclusive=true");
```
#### 特点
- 当在接收信息的时候有一个或者多个备份接收消息者和一个独占消息者的同时接收时候，无论两者创建先后，在接收的时候，均为独占消息者接收。
- 当在接收信息的时候，有多个独占消费者的时候，只有一个独占消费者可以接收到消息。
- 当有多个备份消息者和多个独占消费者的时候，当所有的独占消费者均close的时候，只有一个备份消费者接到到消息。
    + 备注：备份消费者为不带任何参数的消费者。

### 消息查看
#### 接收
```
QueueBrowser browser = session.createBrowser(destination);
Enumeration enumeration = browser.getEnumeration();
while (enumeration.hasMoreElements()) {
    TextMessage message = (TextMessage) enumeration.nextElement();
    System.out.println("Browsing: " + message);
    TimeUnit.MILLISECONDS.sleep(DELAY);
}
```
#### 特点
- 对于mq队列中的消息，有时候需要做监控或者问题跟踪要看看mq的数据，又要保证看后数据不会删除

### 消息过滤
#### 发送
```
TextMessage message = session.createTextMessage("Message #" + i);
if (i % 2 == 0) {
    message.setStringProperty("intended", "me");
} else {
    message.setStringProperty("intended", "you");
}
producer.send(message);
```
#### 接收
```
MessageConsumer consumer = session.createConsumer(destination, "intended = 'me'");
Message message = consumer.receive(TIMEOUT);
```

### 消息事务处理
#### 发送
```
sender.send(senderSession.createTextMessage("create new message"));
senderSession.commit();        //提交 send 的数据
senderSession.rollback();    //回退本次 send 的数据
```   

### 通配符接收
#### 接收
```
String policyType = System.getProperty("wildcard", ".*");
String receiverTopicName = senderTopic.getTopicName() + policyType;
Topic receiverTopic = receiverSession.createTopic(receiverTopicName);
```
#### 注
```
`.` 用于分隔名称中的路径
`*` 用于在路径匹配的名称
`>` 用于递归匹配从这个名字起的任何目的地
```

### 临时通道
```
estination replyDest = session.createTemporaryQueue();
```

#### 接收
```
MessageConsumer replyConsumer = session.createConsumer(replyDest);
replyConsumer.setMessageListener(new MessageListener() {
    @Override
    public void onMessage(Message message) {
        System.out.println("*** REPLY *** ");
        System.out.println(message.toString());
    }
});
```
#### 发送
```
TextMessage message = session.createTextMessage("I need a response for this, please");
message.setJMSReplyTo(replyDest);
producer.send(message);
```

## 基础概念

### 工作类
- ConnectionFactory ：连接工厂，JMS 用它创建连接
- Connection ：JMS 客户端到JMS Provider 的连接
- Destination ：消息的目的地
- Session： 一个发送或接收消息的线程
- MessageConsumer： 由Session 对象创建的用来接收消息的对象
### 消息格式
- StreamMessage -- Java原始值的数据流
- MapMessage--一套名称-值对
- TextMessage--一个字符串对象
- ObjectMessage--一个序列化的 Java对象
- BytesMessage--一个未解释字节的数据流
### 消息确认模式
#### 非事务性会话中，应用程序创建的会话有5 种确认模式,而在事务性会话中，确认模式被忽略。
#### 确认模式:
- ♣ AUTO_ACKNOWLEDGE：自动确认模式。一旦接收方应用程序的方法调用从处理消息处返回，会话对象就会确认消息的接收。
- ♣ CLIENT_ACKNOWLEDGE：客户端确认模式。会话对象依赖于应用程序对被接收的消息调用一个acknowledge()方法。一旦这个方法被调用，会话会确认最后一次确认之后所有接收到的消息。这种模式允许应用程序以一个调用来接收，处理并确认一批消息。注意：在管理控制台中，如果连接工厂的Acknowledge Policy（确认方针）属性被设置为"Previous"（提前），但是你希望为一个给定的会话确认所有接收到的消息，那么就用最后一条消息来调用acknowledge()方法。
- ♣ DUPS_OK_ACKNOWLEDGE：允许副本的确认模式。一旦接收方应用程序的方法调用从处理消息处返回，会话对象就会确认消息的接收；而且允许重复确认。在需要考虑资源使用时，这种模式非常有效。注意：如果你的应用程序无法处理重复的消息的话，你应该避免使用这种模式。如果发送消息的初始化尝试失败，那么重复的消息可以被重新发送。
- ♣ NO_ACKNOWLEDGE：不确认模式。不确认收到的消息是需要的。消息发送给一个NO_ACKNOWLEDGE 会话后，它们会被WebLogic 服务器立即删除。在这种模式下，将无法重新获得已接收的消息，而且可能导致下面的结果：1. 消息可能丢失；和（或者）另一种情况：2. 如果发送消息的初始化尝试失败，会出现重复消息被发送的情况。
- ♣ MULTICAST_NO_ACKNOWLEDGE：IP组播下的不确认模式，同样无需确认。发送给一个MULTICAST_NO_ACKNOWLEDGE会话的消息， 会共享之前所述的NO_ACKNOWLEDGE 确认模式一样的特征。这种模式支持希望通过IP 组播方式进行消息通信的应用程序，而且无需依赖会话确认提供的服务质量。注意：如果你的应用程序无法处理消息的丢失或者重复，那么你应该避免使用这种模式。如果发送消息的初始化尝试失败的话，重复的消息可能会被再次发送。
