---
title: 使用cxf搭建WebService
date: 2016-09-22 22:00:24
categories: [java]
tags: [cxf,Web Service]
---
![配图](http://obl32g9cf.bkt.clouddn.com/webservice_bg.jpg)
搭建WebService的框架有不少，之所有用CXF是因为它是Apache维护的一个开源框架，能搭配Spring一起使用且使用上也很简单。<!--more -->

# 一、下载CXF

可以从Apache下载到最新版的[CXF压缩包](http://cxf.apache.org/download.html)，jar包包含在压缩包的lib文件夹里面。下载这个主要是为了方便使用命令行。如果我们使用maven的话，也可以直接配置pom.xml文件来加载Spring和CXF，方便省事。还可以跳过第二部，直接看如何创建。
# 二、配置CXF到环境变量里

CXF可以使用命令行来根据服务端（客户端）生成客户端（服务端）代码。也可以根据代码（xml）生成xml文件（代码）。所以，配置一下还是很有必要的。配置环境变量需要去Apache官网上下载CXF的压缩包。

下载并解压出来，如我解压的位置是
```
E:\tools\apache-cxf-3.0.10
```

接着，进入配置变量的界面，新建
```
名称为： CXF_Home
变量值为：E:\tools\apache-cxf-3.0.10
```
如图
![config](http://obl32g9cf.bkt.clouddn.com/webservice1.jpg)
 
在path中添加
```
;%CXF_HOME%\bin;
```
![config2](http://obl32g9cf.bkt.clouddn.com/webservice2.jpg)
 
需要注意的是，path中添加才需要加bin，如果新建的变量就添加了bin，会因为有些东西是从lib中加载，而路径配置到了bin而加载不到报下面的错,如图
![config3](http://obl32g9cf.bkt.clouddn.com/webservice3.jpg)

安装成功的话，输入
```
wsdl2java -v
```
会出现如下提示
![config4](http://obl32g9cf.bkt.clouddn.com/webservice4.jpg)
 
# 三、用myeclipse搭建一个webservice

>在搭建项目的时候，我使用了maven来导入cxf的包，而spring的jar包因为以前的项目有配置好的，我直接拿过来用了，所以没有在maven添加了。

webservice的搭建分为两部分，一是服务端，一是客户端。

## 1、webservice服务端的搭建

- 新建一个名为webservice的web project，勾选上Add Maven support。
- 项目创建好后在pom.xml这个文件里加入cxf的内容
```
<dependency>
  <groupId>org.apache.cxf</groupId>
  <artifactId>cxf-api</artifactId>
  <version>2.5.0</version>
</dependency>
<dependency>
  <groupId>org.apache.cxf</groupId>
  <artifactId>cxf-rt-frontend-jaxws</artifactId>
  <version>2.5.0</version>
</dependency>
<dependency>
  <groupId>org.apache.cxf</groupId>
  <artifactId>cxf-rt-bindings-soap</artifactId>
  <version>2.5.0</version>
</dependency>
<dependency>
  <groupId>org.apache.cxf</groupId>
  <artifactId>cxf-rt-transports-http</artifactId>
  <version>2.5.0</version>
</dependency>
<dependency>
   <groupId>org.apache.cxf</groupId>
   <artifactId>cxf-rt-ws-security</artifactId>
   <version>2.5.0</version>
</dependency>  
```

- 加载完毕后就可以编写接口了，接口代码如下
```
package com.mycompany.webservice.server;  
import javax.jws.WebService;

@WebService 
public interface Greeting { 
   public String greeting(String userName); 
}
```

- 实现接口
```
import java.util.Calendar;

import javax.jws.WebService;

@WebService(endpointInterface = "com.mycompany.webservice.server.Greeting")
public class GreetingServiceImpl implements Greeting {

 public String greeting(String userName) {
  return "Hello " + userName + ", currentTime is "
    + Calendar.getInstance().getTime();
 }
}
```

- 在applicationContext.xml进行配置
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns:jaxws="http://cxf.apache.org/jaxws" xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-2.5.xsd
	http://cxf.apache.org/jaxws
        http://cxf.apache.org/schemas/jaxws.xsd">

	<bean id="greetingServiceImpl" class="com.mycompany.webservice.server.GreetingServiceImpl" />
	<jaxws:endpoint id="greetingService" implementor="#greetingServiceImpl" address="/Greeting" />
</beans>
```

- 在web.xml进行配置
```
<!--cxf 配置 webservice 服务 -->
 <servlet>  
        <servlet-name>CXFServlet</servlet-name>  
        <servlet-class>org.apache.cxf.transport.servlet.CXFServlet</servlet-class>  
        <load-on-startup>1</load-on-startup>  
    </servlet>  
    <servlet-mapping>  
        <servlet-name>CXFServlet</servlet-name>  

<!--==这个设置很重要，那么我们的webservice的地址就是http://localhost:8080/yourProgramName/webservice/Greeting===-->
        <url-pattern>/WebService/*</url-pattern>  
    </servlet-mapping>
```
到这边，服务端就完成了。发布可以像普通的web project一样发布。发布完毕，访问[http://localhost:8080/webservice/WebService/Greeting?wsdl](http://localhost:8080/webservice/WebService/Greeting?wsdl)，如果有显示出xml文件的内容来即发布成功。

## 2、webService客户端

用cxf创建webService的客户端有两种方式：
- 通过cxf命令行生成客户端代码。

以之前发布的服务端为例，如果我们要生成它的客户端代码，可通过以下命令
```
wsdl2java -client -encoding utf8 -frontend http://localhost:8080/webservice/WebService/Greeting?wsdl
```
会自动生成java文件，如下图
![config5](http://obl32g9cf.bkt.clouddn.com/webservice5.jpg)
 其中，Greeting_GreetingServiceImplPort_Client.java是测试类，可直接运行，如有参数，也直接修改参数后使用。

- 通过cxf提供的方法直接访问

如果是通过这个方法来访问，则需要知道提供的接口方法和需要传递的参数。
```
import org.apache.cxf.endpoint.Client;
import org.apache.cxf.jaxws.endpoint.dynamic.JaxWsDynamicClientFactory;
public class Test2 {
    public static void main(String[] args) throws Exception {
        JaxWsDynamicClientFactory factory  = JaxWsDynamicClientFactory.newInstance();
         Client client = (Client) factory.createClient("http://localhost:8080/webservice/WebService/Greeting?wsdl");
                   System.out.println("invoke webservice...");
         Object[] result =  client.invoke("greeting", "gray"});   //第一个参数为方法名，第二个参数为需要传递的参数，如果参数有多个，可使用数组进行传值
         System.out.println("message context is:"+result[0]);
    }
}  
```
# 四、测试
当我们服务端运行起来后，运行客户端掉用方法，可看到运行结果
![config6](http://obl32g9cf.bkt.clouddn.com/webservice6.jpg)
 到此，一个简单的webService就搭建好了。