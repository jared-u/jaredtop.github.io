---
title: springcloud——Eureka入门
tags: 
  - springcloud
  - Eureka
categories:
  - - springcloud
  - - Eureka
comments: true
description: springcloud Eureka
abbrlink: 43f5250c
date: 2020-11-01 10:19:31
top:
---

# Eureka

## 服务治理

![服务治理](http://img.jared.top/202011011027_580.png)

## 服务注册与发现

![服务注册与发现](http://img.jared.top/202011011027_96.png)

## Eureka系统结构

![Eureka系统结构](http://img.jared.top/202011011028_379.png)

## Eureka的两个组件

### Eureka Server

**Eureka Server提供服务注册服务**

各个微服务节点通过配置启动后，会在EurekaServer中进行注册，这样EurekaServer中的服务注册表中将会存储所有可用服务节点的信息，服务节点的信息可以在界面中直观看到。

### Eureka Client

**EurekaClient通过注册中心进行访问**

是一个Java客户端，用于简化Eureka Server的交互，客户端同时也具备一个内置的、使用轮询（round-robin）负载算法的负载均衡器。在应用启动后，将会向Eureka Server发送心跳（默认周期为30秒）。如果Eureka Server在多个心跳周期内没有接受到某个节点的心跳，EurekaServer将会从服务注册表中将这个服务节点移除（默认90秒）



## 单机Eureka构建步骤

### IDEA生成EurekaServer端服务注册中心

**建module**

**cloud-eureka-server7001**

**改POM**

```xml
<!--eureka-server-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency>
```

**写YML**

```yml
server:
  port: 7001
eureka:
  instance:
    hostname: localhost #eureka服务端的实例名称
  client:
    # false 表示不向注册中心注册自己
    register-with-eureka: false
    # false 表示自己端就是注册中心，我的职责就是维护服务实例，并不需要去检索服务
    fetch-registry: false
    service-url:
      #设置与Eureka Server交互的地址查询服务和注册服务都需要依赖这个地址
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/

```

**主启动**

```java
@SpringBootApplication
@EnableEurekaServer
public class EurekaMain7001 {
    public static void main(String[] args) {
        SpringApplication.run(EurekaMain7001.class,args);
    }
}
```

**测试**

![](http://img.jared.top/202011011049_957.png)

### EurekaClient端cloud-provider-payment8001

将注册进EurekaServer成为服务提供者provider

**改POM**

```xml
<!--eureka-client-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

**写YML**

```yml
server:
  port: 8001
spring:
  application:
    name: cloud-payment-service
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource #当前数据源操作类型
    driver-class-name: com.mysql.jdbc.Driver    #mysql驱动包
    url: jdbc:mysql://localhost:3306/db2019?useUnicode=true&characterEncoding=utf-8&useSSL=false
    username: root
    password: 123456

eureka:
  client:
    #表示是否将自己注册进EurekaServer默认为true。
    register-with-eureka: true
    #是否从EurekaServer抓取已有的注册信息，默认为true。单节点无所谓，集群必须设置为true才能配合ribbon使用负载均衡
    fetchRegistry: true
    service-url:
      #单机版
      defaultZone: http://localhost:7001/eureka

mybatis:
  mapper-locations: classpath:mapper/*.xml
  type-aliases-package: com.atguigu.springcloud.entities #所有Entity别名类所在包

```

**主启动**

```java
@SpringBootApplication
@EnableEurekaClient
public class PaymentMain8001 {
    public static void main(String[] args) {
        SpringApplication.run(PaymentMain8001.class,args);
    }
}
```

**测试**

![](http://img.jared.top/202011011054_205.png)

### EurekaClient端cloud-consumer-order80

将注册进EurekaServer成为服务消费者consumer

**改POM**

```xml
<!--eureka-client-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

**写YML**

```yml
server:
  port: 80

spring:
  application:
    name: cloud-order-service

eureka:
  client:
    #表示是否将自己注册进EurekaServer默认为true。
    register-with-eureka: true
    #是否从EurekaServer抓取已有的注册信息，默认为true。单节点无所谓，集群必须设置为true才能配合ribbon使用负载均衡
    fetchRegistry: true
    service-url:
#      单机版
      defaultZone: http://localhost:7001/eureka

```

**主启动**

```java
@SpringBootApplication
@EnableEurekaClient
public class PaymentMain8001 {
    public static void main(String[] args) {
        SpringApplication.run(PaymentMain8001.class,args);
    }
}
```

**测试**

**测试**

![](http://img.jared.top/202011011102_602.png)

## 集群Eureka构建步骤

服务注册：将服务信息注册进注册中心

服务发现：从注册中心上获取服务信息

实质：存key服务命令 取value调用地址

步骤：

1. 先启动eureka注册中心
2. 启动服务提供者payment支付服务
3. 支付服务启动后会把自身信息（比如服务地址以别名方式注册进eureka）
4. 消费者order服务在需要调用接口时，使用服务别名去注册中心获取实际的RPC远程调用地址
5. 消费者获取调用地址后，底层实际是利用HttpClient技术实现远程调用
6. 消费者获得服务地址后会缓存在本地jvm内存中，默认每间隔30秒更新一次服务调用地址

微服务RPC远程服务调用最核心的是**高可用**，如果注册中心只有一个，出故障就over了。

解决办法：搭建Eureka注册中心集群，实现负载均衡+故障容错



### 參考cloud-eureka-server7001

### 新建cloud-eureka-server7002

### 改pom

### 修改映射配置改hosts

`127.0.0.1	eureka7001.com
127.0.0.1	eureka7002.com`

### 7001改yml

```yml
server:
  port: 7001
eureka:
  instance:
    hostname: eureka7001.com #eureka服务端的实例名称
  client:
    # false 表示不向注册中心注册自己
    register-with-eureka: false
    # false 表示自己端就是注册中心，我的职责就是维护服务实例，并不需要去检索服务
    fetch-registry: false
    service-url:
      #设置与Eureka Server交互的地址查询服务和注册服务都需要依赖这个地址
      defaultZone: http://eureka7002.com:7002/eureka/

```

### 7002改yml

```yml
server:
  port: 7002
eureka:
  instance:
    hostname: eureka7002.com #eureka服务端的实例名称
  client:
    # false 表示不向注册中心注册自己
    register-with-eureka: false
    # false 表示自己端就是注册中心，我的职责就是维护服务实例，并不需要去检索服务
    fetch-registry: false
    service-url:
      #设置与Eureka Server交互的地址查询服务和注册服务都需要依赖这个地址
      defaultZone: http://eureka7001.com:7001/eureka/

```

### 7002主启动

```java
@SpringBootApplication
@EnableEurekaServer
public class EurekaMain7002 {
    public static void main(String[] args) {
        SpringApplication.run(EurekaMain7002.class,args);
    }
}
```



### @LoadBalanced 注解赋予RestTemplate负载均衡的能力



## actuator微服务信息完善

```yml
eureka:
  instance:
    instance-id: payment8001
    prefer-ip-address: true  # 访问路径可以显示ip地址
```



## 服务发现Discovery

对于注册进Eureka里面的微服务，可以通过服务发现来获得该服务的信息

```java
@Resource
private DiscoveryClient discoveryClient;
@GetMapping(value = "/payment/discovery")
    public Object discovery(){
        List<String> list=discoveryClient.getServices();
        for (String element : list) {
            log.info("******element: "+element);
        }
        List<ServiceInstance> instances = discoveryClient.getInstances("CLOUD-PAYMENT-SERVICE");
        for (ServiceInstance instance : instances) {
            log.info("******instance: "+instance.getServiceId()+"\t"+instance.getHost()+"\t"+instance.getPort()+"\t"+instance.getUri());
        }
        return this.discoveryClient;
    }
```

## Eureka自我保护

导致原因：某时刻某一个微服务不可用了，eureka不会立刻清理，依旧会对该微服务的信息进行保存，属于CAP里面的AP分支

