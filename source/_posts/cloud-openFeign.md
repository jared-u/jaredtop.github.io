---
title: springcloud——openFeign入门
tags:
  - springcloud
  - openFeign
categories:
  - - springcloud
  - - openFeign
comments: true
description: springcloud——openFeign入门
abbrlink: 4fc25143
date: 2020-11-25 12:46:49
top:
---

## OpenFeign超时控制

服务端故意设置延时时间

### ![OpenFeign超时控制](http://img.jared.top/202011251251_722.png)

![啊啊啊](http://img.jared.top/202011251253_728.png)

客户端默认等待一秒钟，超过后报错

有时候需要设置Feign客户端的超时时间。在yml文件中开启配置

```yml
# 设置Feign客户端超时时间
ribbon:
  # 指的是建立连接所用的时间，适用于网络状况正常的情况下，两端连接所用的时间
  ReadTimeout: 5000
  # 指的是建立连接后从服务器读取到可用资源所用的时间
  ConnectTimeout: 5000
```



## OpenFeign日志打印功能

Feign提供了日志打印功能，我们可以通过配置来调整日志级别，从而了解Feign中Http请求的细节，就是对Feign接口的调用情况进行监控和输出。

### 日志级别

- NONE：默认的，不显示任何日志；
- BASIC：仅记录请求方法、URL、响应状态码及执行时间；
- HEADERS：除了BASIC中定义的信息之外，还有请求和响应的头信息；
- FULL：除了HEADERS中定义的信息之外，还有请求和响应的正文及元数据。



### 配置日志Bean

![配置日志Bean](http://img.jared.top/202011251312_946.png)

在yml里面配置

```yml
logging:
  level:
    # feign日志以什么级别监控那个接口
    com.atguigu.springcloud.service.PaymentFeignService: debug
```

