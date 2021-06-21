---
![](http://img.jared.top/202011261202_693.png)title: springcloud——gateway入门
tags:
  - springcloud
  - gateway
categories:
  - - springcloud
  - - gateway
comments: true
description: springcloud——gateway入门
abbrlink: cb14d55c
date: 2020-11-26 11:35:38
top:
---

# 概述

## 概述

SpringCloud Gateway 使用的Webflux中的reactor-netty响应式编程组件，底层使用了Netty通讯框架。

### 特性

![特性](http://img.jared.top/202011261147_826.png)

### 和zuul的区别

![](http://img.jared.top/202011261149_215.png)

# 三大核心概念

## Route路由

路由是构建网关的基本模块，它由ID，目标URI，一系列的断言和过滤器组成，如果断言为true则匹配该路由

## Predicate断言

参考的是Java8的java.util.function.Predicate

可以匹配HTTP请求中的所有内容（例如请求头和请求参数），如果请求与断言相匹配则进行路由。

## Filter过滤

指的是Spring框架中GatewayFilter的实例，使用过滤器，可以在请求被路由前或者之后对请求进行修改。

![](http://img.jared.top/202011261202_693.png)

# 通过微服务名实现动态路由

默认情况下Gateway会根据注册中心的服务列表，以注册中心上微服务名为路径创建动态路由进行转发，从而实现动态路由的功能

![](http://img.jared.top/202011261612_600.png)