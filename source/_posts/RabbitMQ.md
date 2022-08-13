---
title: RabbitMQ
date: 2022-08-13 22:44:42
tags:
---

# RabbitMQ



## 一、下载地址

链接：https://pan.baidu.com/s/1vcxPyQHY-hNWFvav9RuiJQ 
提取码：9898



## 二、安装

### 1.安装erl

首先运行otp_win64_24.1.7.exe安装erl

Erlang目录设置环境变量path中



### 2.安装rabbitmq

然后安装rabbitmq-server-3.9.11.exe

sbin目录设置环境变量path中



### 3.开启启动界面

安装好后进入rebbitmq目录中的sbin目录，输入cmd

RabbitMQ-Plugins相当于是一个管理界面，方便我们在浏览器界面查看RabbitMQ各个消息队列以及exchange的工作情况。

```bat
rabbitmq-plugins enable rabbitmq_management
```

上面的命令执行成功之后，我们就可以通过[http://localhost:15672](http://localhost:15672/)来访问web端的管理界面





## 三、.RabbitMQ界面使用

### 1.创建虚拟主机

![image-20220813230417518](\img\image-20220813230417518.png)



### 2.创建交换机

![image-20220813230932304](\img\image-20220813230932304.png)



### 3.创建消息队列

![image-20220813230702073](\img\image-20220813230702073.png)



### 4.队列与交换机绑定

点击队列名

![image-20220813231223270](\img\image-20220813231223270.png)

绑定

![image-20220813231458326](\img\image-20220813231458326.png)

测试消息发生

![image-20220813231621145](\img\image-20220813231621145.png)



## 四、java代码

### 1.maven依赖

```xml
<dependency>
    <groupId>com.rabbitmq</groupId>
    <artifactId>amqp-client</artifactId>
    <version>5.2.0</version>
</dependency>
```



### 2.生产者

```java
package com.mudaxian.test;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

public class SimpleProvider {
    public static void main(String[] args) throws IOException, TimeoutException{
        //创建链接工厂对象
        ConnectionFactory connectionFactory = new ConnectionFactory();
        //设置RabbitMQ服务主机地址，默认localhost
        connectionFactory.setHost("localhost");
        //设置端口，默认5672
        connectionFactory.setPort(5672);
        //设置虚拟主机名，默认/
        connectionFactory.setVirtualHost("mudaxian");
        //设置用户名密码
        connectionFactory.setUsername("guest");
        connectionFactory.setPassword("guest");

        //创建链接
        Connection connection = connectionFactory.newConnection();
        //创建通道
        Channel channel = connection.createChannel();
        //声明队列
        //参数1：消息队列名称
        //参数2，是否持久化
        //参数3，是否独占本次connection链接
        //参数4，是否自动删除消息
        //参数5，是否需要传递额外的队列数据Map
        channel.queueDeclare("mu_queue",true,false,false,null);
        //创建消息
        String msg = "100";
        //参数1，使用指定的交换机，不设置则使用默认交换机
        //参数2，指定路由器key，如果是简单模式直接给队列名就行
        //参数3，发生消息时是否需要额外的消息数据
        //参数4，消息内容
        channel.basicPublish("mu_exchanges","key",null,msg.getBytes());
        //关闭资源
        channel.close();
        connection.close();
    }
}
```



### 3.消费者

```java
package com.mudaxian.test;

import com.rabbitmq.client.*;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

public class SimpleConsumer {
    public static void main(String[] args) throws IOException, TimeoutException {
        //创建链接工厂对象
        ConnectionFactory connectionFactory = new ConnectionFactory();
        //设置RabbitMQ服务主机地址，默认localhost
        connectionFactory.setHost("localhost");
        //设置端口，默认5672
        connectionFactory.setPort(5672);
        //设置虚拟主机名，默认/
        connectionFactory.setVirtualHost("mudaxian");
        //设置用户名密码
        connectionFactory.setUsername("guest");
        connectionFactory.setPassword("guest");

        //创建链接
        Connection connection = connectionFactory.newConnection();
        //创建频道
        Channel channel = connection.createChannel();

        //声明队列
        //参数1：消息队列名称
        //参数2，是否持久化
        //参数3，是否独占本次connection链接
        //参数4，是否自动删除消息
        //参数5，是否需要传递额外的队列数据Map
        channel.queueDeclare("mu_queue",true,false,false,null);
        //创建消费者，并且设置消息处理
        //指定频道
        DefaultConsumer defaultConsumer = new DefaultConsumer(channel){
            /**
             *
             * @param consumerTag 消费者名称，如果之前没有指定，则会自动生成一个
             * @param envelope  消息一些额外的参数 channel.basicPublish("","simple_queue1",null,msg.getBytes());
             * @param properties  另外的数据所封装的属性对象 null
             * @param body  消息本身
             * @throws IOException
             */
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                //实现消费的业务逻辑
                System.out.println("consumerTag"+consumerTag);
                System.out.println("交换机："+envelope.getExchange()+"----RoutingKey："+envelope.getRoutingKey()+"----DeliveryTag："+envelope.getDeliveryTag());
                System.out.println("消息本身："+new String(body,"UTF-8"));
            }
        };
        //监听消息
        //参数1，指定要监听的队列的名称
        //参数2，设置消息的应答模式，true自动应答（性能好，消息有可能丢失），false手动应答
        //参数3，
        channel.basicConsume("mu_queue",true,defaultConsumer);


        //关闭资源，建议不关闭一直监听
    }
}
```



### 4.查看结果

此时消费者为一直监听mudaxian虚拟主机的mu_queue队列中的消息，只要收到消息，便会马上System.out.println打印出来！
