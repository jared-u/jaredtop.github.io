---
title: springcloud——consul入门
tags:
  - springcloud
  - consul
categories:
  - - springcloud
  - - consul
comments: true
description: springcloud——consul入门
abbrlink: ca26ff8a
date: 2020-11-05 17:54:49
top:
---

# consul 入门

Consul是一款由[HashiCorp](https://www.hashicorp.com/)公司开源的，用于服务治理的软件，[Spring Cloud Consul](https://spring.io/projects/spring-cloud-consul)对其进行了封装。Consul具有如下特点:

1. 服务注册 - 自动注册和取消注册服务实例的网络位置
2. 运行状况检查 - 检测服务实例何时启动并运行
3. 分布式配置 - 确保所有服务实例使用相同的配置

Consul agent有两种运行模式：Server和Client。这里的Server和Client只是Consul集群层面的区分，与搭建在Cluster之上 的应用服务无关。 以Server模式运行的Consul agent节点用于维护Consul集群的状态，官方建议每个Consul Cluster至少有3个或以上的运行在Server mode的Agent，Client节点不限。

## 安装consul

Consul下载地址：https://www.consul.io/downloads.html

## 常用命令

查看版本：`consul --version`

启动：`consul agent -dev`

访问consul首页：`http://localhost:8500`