---
title: （2）、rabbitMQ的通信流程
date: 2016-08-14 22:22:52
categories: [rabbitMQ]
tags: [rabbitMQ通信流程]
---
![配图](http://obl32g9cf.bkt.clouddn.com/rabbit%E9%80%9A%E4%BF%A1%E6%B5%81%E7%A8%8B%E7%9A%84%E9%85%8D%E5%9B%BE.jpg)
在上一篇中，大致介绍了一下rabbitmq（为了方便，以下简称rmq）中一些比较重要的概念，对rabbitmq也算是有了一些的了解。接下来，我们来看看rabbitmq的消息传递的流程，来熟悉和加深对rabbitmq 的了解。<!-- more-->

# 一、rabbitMQ消息传递的流程

![rabbitMQ示意图](http://obl32g9cf.bkt.clouddn.com/rabbitMQ%E7%A4%BA%E6%84%8F%E5%9B%BE.png)

这是一个简单的示意图。从中可以看到，rmq通信的大致流程是：***消息的生产者（Producer）与rabbitmq server建立连接，并创建通道（channel）；然后把消息传送至rabbitmq server上指定的Exchange中的queue里存放着；当有消息的消费者（Consumer）监听了这个queue时，就会接收该消息。***

其中，消息的消费者和生产者分属于不同应用，这样就能成功的使两个不同的应用通过rabbitmq进行消息的通信，而不用考虑两个应用间直接对接的一些麻烦了。

# 二、一些需要事先清楚的细节

在上面我们讲的通信的流程中，有一些需要注意的东西没有提到；而在真正的使用中，这些细节如果不注意的话会导致一些不必要的麻烦。比如：

- **不管是生产者或者消费者，发送消息或监听queue时都需要指定queue，而每个queue都也都需要绑定到一个exchange上。**如果没有事先创建的话，就需要在发送或监听queue前进行声明，即发送消息或监听的时候通知rabbitmq server创建queue和exchange；至少需要声明queue。（注：如果只声明queue不绑定exchange，则会把声明的queue放置到默认的exchange中，默认的exchange是direct类型的，后文会对exchange的类型进行介绍）。如若没有事先创建，且没有进行声明，会造成连不上rabbitmq server的情况。
- **创建queue或者exchange是需要对它们进行一些参数的设定。**queue的创建必须设置是否是永久的queue（即queue中的消息会进行备份防止丢失）、是否自动删除（没有用户连接到rabbit server时，队列中的数据会被自动删除）；exchange的创建需要指定类型，是否永久，是否自动删除等等，具体情形可参看文档或通过rabbitmq server进行了解。这里有个坑需要注意，比如，我们事先已经创建了一个永久队列，但是在监听的时候，我们仍可能会进行声明以防止切换环境的时候没有预先创建队列导致连不上。但是，如果声明的队列与事先创建的队列参数设置不一致的话也是有可能导致连不上server的问题的。
- **对routingkey的绑定。**在真正使用的时候，我们都会给每个queue进行routingkey的绑定，生产者按routingkey发送消息，接收者按routingkey接收消息，当然如果使用环境比较简单的话，也可以不对routingkey进行绑定。

# 三、需要了解，但可能不大会用到的东西

我们知道消息的消费者和生产者是属于应用端的，而exchange和queue是属于服务端的。在上面的示意图中，可以看出exchange和queue是放在一个叫做virtual host的容器当中，这其实是一个虚拟概念，有点类似于多个工作空间，每个virtual host里包含若干个exchange和queue。在平时使用的时候如果没有对virtual host进行绑定，则默认为rabbitmq server的默认virtual host，如果有创建新的virtual host的话，则在创建exchange和queue时则需要指定。
