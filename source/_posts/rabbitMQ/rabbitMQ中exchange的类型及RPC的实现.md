---
title: （3）、exchange类型的使用及RPC的实现.md
date: 2016-08-15 23:00:05
categories: [rabbitMQ]
tags: [exchange的类型及RPC的实现]
---
在上一篇里，我们讲了rabbitMQ的通信流程。接下来，通过代码来实现exchange的几种类型，看一下rabbitMQ是如何运用的。<!--more -->

# exchange几种类型的实现方式

## fanout

如果一个exchange的类型被定义为fanout，那么发送到该exchange上的消息会发送给所有监听该exchange的queue中。Fanout类型的exchange不需要处理routingkey，只需要队列监听该exchange就能接收到消息，类似于广播。

接下来，来看一下代码中的实现，首先是消息的消费者

```
public class ReceiveLogs {

	private static final String EXCHANGE_NAME = "LOGSs";

	public static void main(String[] argv) throws Exception {

		ConnectionFactory factory = new ConnectionFactory();
		factory.setHost("localhost");    //设置安装rabbitMQServer的ip，因为我是装本机上，所以直接使用localhost
		Connection connection = factory.newConnection();
		Channel channel = connection.createChannel();

		channel.exchangeDeclare(EXCHANGE_NAME, "fanout");    //声明一个fanout类型的exchange，如果不存在则创建出来
		//该语句得到一个随机名称的Queue，该queue的类型为non-durable、exclusive、auto-delete的，将该queue绑定到上面的exchange上接收消息。
		String queueName = channel.queueDeclare().getQueue();  
		channel.queueBind(queueName, EXCHANGE_NAME, "");    //通道绑定队列

		System.out.println(" [*] Waiting for messages. To exit press CTRL+C");

		QueueingConsumer consumer = new QueueingConsumer(channel); //创建消费者
		channel.basicConsume(queueName, true, consumer);

		while (true) {
			QueueingConsumer.Delivery delivery = consumer.nextDelivery();
			String message = new String(delivery.getBody());
			System.out.println(" [x] Received '" + message + "'");   
		}

	}
}
```

- 消息发送者

```
public class SendMsg{

	private static final String EXCHANGE_NAME = "LOGSs";

	public static void main(String[] argv) throws Exception {
		ConnectionFactory factory = new ConnectionFactory();
		factory.setHost("localhost");

		Connection connection = factory.newConnection();

		Channel channel = connection.createChannel();

		channel.exchangeDeclare(EXCHANGE_NAME, "fanout");    

		String message = "发送的消息";

		channel.basicPublish(EXCHANGE_NAME, "b", null, message.getBytes());    //发送消息到exchange中
		System.out.println(" [x] Sent '" + message + "'");

		channel.close();    
		connection.close();
	}
}
```
## direct

发送到类型为direct的exchange的消息，会根据routingkey来判断消息发送给哪个queue。每个绑定该exchange的queue都需要绑定一个routingkey。如果接收到的消息不符合绑定的每一个routingkey，则消息会被抛弃掉。

- 消息的消费者：和之前的消费者代码差不多，只是多了绑定routingKey这一步和设置类型改为direct

···
/**
*创建一个名为cplcqueue的绑定了3个routingkey的queue，监听direct类型的名为directExchange的exchange。
*
*/
public class ReceiveLogsDirect {
	private static final String EXCHANGE_NAME = "directExchange";

	public static void main(String[] args) throws Exception {

		ConnectionFactory factory = new ConnectionFactory();
		factory.setHost("localhost");
		Connection connection = factory.newConnection();

		Channel channel = connection.createChannel();
		channel.exchangeDeclare(EXCHANGE_NAME, "direct",true);
		String queueName = "cplcqueue";
		String[] a = new String[3];
		a[0] = "cplc_vm_cachedFileRequest35";
		a[1] = "cplc_vm_downloadRequest35";
		a[2] = "cplc_vm_delFileRequest35";

		if(a.length <1){
			System.err.println("Usage: ReceiveLogsDirect [info] [warning] [error]");
			System.exit(1);
		}

		for(int i=0;i<3;i++){    
			channel.queueBind(queueName, EXCHANGE_NAME, a[i]);
		}

		System.out.println(" [*] Waiting for messages. To exit press CTRL+C");

		QueueingConsumer consumer = new QueueingConsumer(channel);
		channel.basicConsume(queueName, true, consumer);
		while (true) {
			QueueingConsumer.Delivery delivery = consumer.nextDelivery();
			String message = new String(delivery.getBody());
			String routingKey = delivery.getEnvelope().getRoutingKey();
			System.out.println(" [x] Received '" + routingKey + "':'" + message + "'");   
		}
	}
}
···

- 消息生产者

```
public class SendDirect {
	private static final String EXCHANGE_NAME = "directExchange";

	public static void main(String[] args) throws Exception {

		ConnectionFactory factory = new ConnectionFactory();
		factory.setHost("localhost");
		Connection connection = factory.newConnection();

		Channel channel = connection.createChannel();
		channel.exchangeDeclare(EXCHANGE_NAME, "direct",true);
	        String routingKey="cplc_vm_delFileRequest35";
                String message ="发送的消息";
               channel.basicPublish(EXCHANGE_NAME, routingKey, null, message.getBytes());      //在发送信息的时候需要指定routingKey
               channel.close();
               connection.close();      	
       }
}
```
## topic

发送到类型为topic的exchange的消息，会将消息转发到所有匹配topic的queue上。该exchange会将routingKey与某topic进行模糊匹配，队列需要绑定一个一个topic，可以使用通配符进行模糊匹配，“#”匹配一个或多个，而“*”只匹配一个词。如“log.#”可以匹配“log.info.abc”，而“log.*”则只会匹配到类似“log.abc”这样后面只有一个词的routingkey。

topic的消息消费者实现

```
public class ReceiveLogsTopic {

	private static final String EXCHANGE_NAME = "topic_logs";
	public static void main(String[] args) {
		Connection connection = null;
		Channel channel = null;
		try {
			ConnectionFactory factory = new ConnectionFactory();
			factory.setHost("localhost");

			connection = factory.newConnection();
			channel = connection.createChannel();

			channel.exchangeDeclare(EXCHANGE_NAME, "topic");

			String queueName = channel.queueDeclare().getQueue();

			if (arg.length < 1){
				System.err.println("Usage: ReceiveLogsTopic [binding_key]...");
				System.exit(1);
			}
			channel.queueBind(queueName, EXCHANGE_NAME, "st.#");    

			System.out.println(" [*] Waiting for messages. To exit press CTRL+C");

			QueueingConsumer consumer = new QueueingConsumer(channel);
			channel.basicConsume(queueName, true, consumer);

			while (true) {
				QueueingConsumer.Delivery delivery = consumer.nextDelivery();
				String message = new String(delivery.getBody());
				String routingKey = delivery.getEnvelope().getRoutingKey();
				System.out.println(" [x] Received '" + routingKey + "':'" + message + "'");   
			}
		}
		catch  (Exception e) {
			e.printStackTrace();
		}
		finally {
			if (connection != null) {
				try {
					connection.close();
				}
				catch (Exception ignore) {}
			}
		}
	}
```

消息发送者实现

```
public class SendTopic {

	private static final String EXCHANGE_NAME = "topic_logs";

	public static void main(String[] args) {

		Connection connection = null;
		Channel channel = null;

		try {
			ConnectionFactory factory = new ConnectionFactory();
			factory.setHost("localhost");
			connection = factory.newConnection();
			channel = connection.createChannel();

			channel.exchangeDeclare(EXCHANGE_NAME, "topic");

			String routingKey = "st.abc.efg";
			String message = "发送的消息";

			channel.basicPublish(EXCHANGE_NAME, routingKey, null, message.getBytes());
			System.out.println(" [x] Sent '" + routingKey + "':'" + message + "'");
		} catch (Exception e) {
			e.printStackTrace();
		}finally{
			if(connection !=null){
				try {
					connection.close();
				} catch (IOException e) {
				}
			}
		}

	}
}
```
## headers

这个类型的exchange基本不会用到，主要是根据发送的消息中所带的header这个参数来判断的。没有进行深入的了解，就不说了。

# RPC的实现

RPC（远程进程调用）的主要过程是，消费者接收到消息后在对消息进行处理后把处理结果与收到消息的反馈一并发回服务端。这与exchange是哪种类型是无关的，所以，任何一种类型的exchange都可以用来实现rpc模式。

消息发送者:与普通的消息发送者的区别在于，事先设置不立即对接收到的消息进行反馈，而是发送完消息后监听消息队列，等待返回结果，直到收到结果才关闭通道和连接。

```
public class RPCSend{  
    private Connection connection;  
    private Channel channel;  
    private String replyQueueName = "cplcqueue35";  
    private QueueingConsumer consumer;  
  
    public RPCClient() throws Exception {  
        ConnectionFactory factory = new ConnectionFactory();  
        factory.setHost("localhost");  
        connection = factory.newConnection();  
        channel = connection.createChannel();  
  
        consumer = new QueueingConsumer(channel);  
        channel.basicConsume(replyQueueName, true, consumer);  
    }  
  
    public String call(String message) throws Exception {  
        String response = null;  
        String corrId = UUID.randomUUID().toString();  
  
        BasicProperties props = new BasicProperties();  
        props.setReplyTo(replyQueueName);  
        props.setCorrelationId(corrId);  
  
        channel.basicPublish(Exchange, RoutingKey, props, message.getBytes());  
        while (true) {  
            QueueingConsumer.Delivery delivery = consumer.nextDelivery();  
            if (delivery.getProperties().getCorrelationId().equals(corrId)) {  
                response = new String(delivery.getBody());  
                break;  
            }  
        }  
  
        return response;  
    }  
  
    public void close() throws Exception {  
        connection.close();  
    }  
  
    public static void main(String[] argv) {  
        RPCClient fibonacciRpc = null;  
        String response = null;  
        try {  
            fibonacciRpc = new RPCClient();  
  
            response = fibonacciRpc.call("发送的消息");  
		    
            System.out.println(" [.] Got '" + response + "'");  
        } catch (Exception e) {  
            e.printStackTrace();  
        } finally {  
            if (fibonacciRpc != null) {  
                try {  
                    fibonacciRpc.close();  
                } catch (Exception ignore) {  
                }  
            }  
        }  
    }  
}  
```

消息消费者：处理完消息后，把应答机制设置为true，把处理结果和消息反馈一起发给服务器

```

public class RPCClient {  
    private Connection connection;  
    private Channel channel;  
    private String replyQueueName = "RPC_QUEUE";  
    private QueueingConsumer consumer;  
  
    public RPCClient() throws Exception {  
        ConnectionFactory factory = new ConnectionFactory();  
        factory.setHost("localhost");  
        connection = factory.newConnection();  
        channel = connection.createChannel();  
  
        consumer = new QueueingConsumer(channel);  
        channel.basicConsume(replyQueueName, true, consumer);  
    }  
public void RPCReceiver(){
	connection = null;
	channel = null;
	try {
		ConnectionFactory factory = new ConnectionFactory();
		factory.setHost(HOST);
		connection = factory.newConnection();
		channel = connection.createChannel();
		channel.queueDeclare(RPC_QUEUE, true, false, false, null);
		String routingKey = "routingKey";
		channel.queueBind(RPC_QUEUE,EXCHANGE_NAME, routingKey );
		channel.basicQos(1);

		QueueingConsumer consumer = new QueueingConsumer(channel);
		channel.basicConsume(RPC_QUEUE, false, consumer);

		do{
			String response = null;

			QueueingConsumer.Delivery delivery = consumer.nextDelivery();
			String rout = delivery.getEnvelope().getRoutingKey();
				BasicProperties props = delivery.getProperties();
				try {
					String message = new String(delivery.getBody());
					
					response = “处理消息：”+message;
				} catch (Exception e) {
					response = "";
				} finally {
					channel.basicPublish("", props.getReplyTo(), props,response.getBytes());  
					channel.basicAck(delivery.getEnvelope().getDeliveryTag(),false);  
				}
			
		}while (true);
	} catch (Exception e) {
	} finally {
		if (connection != null) {
			try {
				connection.close();
			} catch (Exception ignore) {
			}
		}
	}
}
}
```

注：上述代码测试，如果是先运行生产者的话，生产的消息因为还没有queue监听，会暂存在server上，然后再运行消费者的话，server会自动把消息分配给消费者。所以，不管是先运行消费者或者生产者都可以。