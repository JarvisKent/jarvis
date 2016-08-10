---
title: rabbitMQ入门及环境搭建
date: 2016-08-10 09:36:16
categories: [rabbitMQ]
tags: [rabbitMQ环境搭建]
---
因为项目需要接触到了rabbitMQ，在此之前没有接触过关于这方面的内容，所以打算记录下对rabbitMQ的学习历程，这一篇是个人对rabbitMQ的一些基础概念的了解和环境的搭建。<!-- more -->

# 一、rabbitMQ

rabbitMQ中MQ是message queue的简写。而rabbitMQ是作为一个中间件，使用于应用与应用之间的通信，两个应用之间无需知道互相的存在，不仅有解耦作用，相对于应用之间的直接通信也更加安全，可靠。

# 二、rabbitMQ的一些基础概念

下面是一些知道基础概念，需要了解清楚，不然开发很容易陷入一些坑。

## 1、ConnectionFactory、Connection、Channel

ConnecitonFactory：从名称可以看出是一个Connection创建工厂，Channel是应用于MQ通信的通道，大部分的业务逻辑都是在Channel中实现的。包括定义Queue、Exchange、绑定RoutingKey，发布消息等等。

## 2、Queue

存放消息的队列，发布的消息存放到Queue中，消息的消费者则从Queue中取走消息，每个Queue都可以有多个的消费者。当队列中的消息比较多时，消息会平均分给订阅该队列的几个消费者去处理，而不是每个消费者都收到消息进行处理。

## 3、***Message acknowledgment***

消息回执：在使用的时候，为了防止消息被消费者取走后，因为某些问题而没有处理，使得消息丢失。RabbitMQ要求消费者在取走消息后需给定一个回执。RabbitMQ收到回执后才会在Queue中把被取走的消息移除掉。必须给定消息回执，在编写业务逻辑的时候需要注意。

## 4、Exchange

交换区：我们在向rabbitMQ发送消息的时候，消息是会先发送到Exchange中去，Exchange会根据某些规则把消息放置到符合规则的一个或多个Queue中。Exchange有topic、direct、fount、headers四种类型，每种类型的规则都略有不同。

## 5、RoutingKey

一般情况下，我们在创建队列后，会把该队列绑定到我们自己创建的Exchange中，而绑定的时候，需要加入一个RoutingKey，这个RoutingKey就是上面所说的规则。消息传递到Exchange后，会根据Routingkey，把消息发送到相应的队列。

## 6、RPC模式（Remote Procedure Call，远程过程调用）

从上面的几个概念中，我们知道了，当消费者取到消息后是要给定回执，告诉RabbitMQ已经收到消息了，你可以删掉那条消息了，等处理完消息后，再另外给RabbitMQ发布处理结果。而RPC模式的区别就在于，应用收到消息后，不会马上告诉RabbitMQ收到消息了，而是把消息处理完后，带着处理结果一起返回给了RabbitMQ。

# 三、安装和配置RabbitMQ

讲完了RabbitMQ中几个需要知道的概念后，就可以开始动手试试了。当然，在开始练手前，得先把rabbitMQServer安装好才行。
rabbitMQ的服务端是基于Erlang语言编写的，所以在安装rabbitMQServer前，需要先安装Erlang环境再安装rabbitMQServer。安装完后需通过下面几个步骤配置一下rabbitMQ。

1、 安装完后需要先进行配置，打开RabbitServer中的RabbitMQ Command Prompt，在安装目录的sbin下，输入命令

```
rabbitmq-plugins enable rabbitmq_management    
```
如果没有出现下面的内容则需要重新以管理员身份运行命令台，再输入上面的命令。
![rabbitMQ输出结果](http://obl32g9cf.bkt.clouddn.com/rabbitMQ%E5%91%BD%E4%BB%A4%E8%A1%8C.png)

2、接着分别输入如下命令

```
rabbitmq-service.bat  stop
rabbitmq-service.bat  install
rabbitmq-service.bat  start
```
3、通过上面两个步骤后，rabbitMQServer就配置好了，我们可以打开它的控制台看看，是否能正常登录。在浏览器输入
```
 http://localhost:55672/mgmt    
```
rabbitMQ有提供一个账号和密码都为**guest**的测试账号供我们进行测试。控制台界面如下：
![RabbitMQ控制台](http://obl32g9cf.bkt.clouddn.com/rabbitMQ%E6%8E%A7%E5%88%B6%E5%8F%B0.png)

到此，MQ的环境搭建和配置就完成了。接下来，我们就可以通过代码进行熟悉和了解rabbitMQ了。
