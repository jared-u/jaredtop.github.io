---
title: springcloud——Hystrix入门
tags:
  - springcloud
  - Hystrix
categories:
  - - springcloud
  - - Hystrix
comments: true
description: springcloud——Hystrix入门
abbrlink: 8b5c7a85
date: 2020-11-25 13:19:10
top:
---

服务雪崩

当你发现一个模块下的某个实例失败后，这时候这个模块依然还会接受流量，然后这个有问题的模块还调用了其它模块，这样就会发生级联故障，或者叫雪崩。

# Hystrix概述

Hystrix是一个用于处理分布式系统的延迟和容错的开源库，在分布式系统里，许多依赖不可避的会调用失败，比如超时、异常等，Hystrix能够保证在一个依赖出问题的情况下，不会导致整体服务失败，避免级联故障，以提高分布式系统的弹性。

“断路器”本身是一种开关装置，当某个服务单元发生故障之后，通过断路器的故障监控，向调用方返回一个符合预期的、可处理的备选响应（FallBack），而不是长时间的等待或者抛出调用方无法处理的异常，这样就保证了服务调用放的线程不会被长时间、不必要地占用，从而避免了故障在分布式系统中的蔓延，乃至雪崩。

## 重要概念

### 服务降级

服务器忙，请稍后再试，不让客户端等待并立刻返回一个友好提示，fallback

**哪些情况会触发降级**

- 程序运行异常
- 超时
- 服务熔断触发服务降级
- 线程池/信号量打满也会导致服务降级

#### 降级配置

`@HystrixCommand`：一旦调用服务方法失败并抛出了错误信息后，会自动调用`@HystrixCommand`标注好的`fallbackMethod`调用类中的指定方法

```java
@HystrixCommand(fallbackMethod = "paymentInfo_TimeOutHandler",commandProperties = {
            @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds",value = "3000")
    })
```

如果每一个方法都有降级方法，形成代码冗余

所以就有`全局fallback`

```java
//类上加入这行
@DefaultProperties(defaultFallback = "paymentGlobalFallbackMethod")
	//方法上
	@HystrixCommand
	public String paymentInfo_TimeOut(@PathVariable("id")Integer id){}
    
    //下面是全局fallback
	public String paymentGlobalFallbackMethod(){
    return "Global异常处理信息，请稍后再试。o(╯□╰)o";
	}
```

或者为`FeignClient`接口新建一个类实现该接口，统一为接口里面的方法进行异常处理。

```java
@Component
public class PaymentFallbackService implements PaymentHystrixService {
    @Override
    public String paymentInfo_OK(Integer id) {
        return "---- PaymentFallbackService fall back-paymentInfo_OK,o(╥﹏╥)o";
    }

    @Override
    public String paymentInfo_TimeOut(Integer id) {
        return "---- PaymentFallbackService fall back-paymentInfo_TimeOut,o(╥﹏╥)o";
    }
}
```



### 服务熔断

类比保险丝达到最大服务访问后，直接拒绝访问，拉闸限电，然后调用服务降级的方法并返回友好提示

服务的降级→进而熔断→恢复调用链路

#### 断路器

#### 熔断是什么

熔断机制是应对雪崩效应的一种微服务链路保护机制。当扇出链路的某个微服务出错不可用或者响应时间太长时，会进行服务的降级，进而熔断该节点微服务的调用，快速返回错误的响应信息。

**当检测到该节点微服务调用响应正常后，恢复调用链路**

在cloud里面，熔断机制是通过`Hystrix`实现，`Hystrix`会监控微服务间调用的状况，当失败的调用到一定阈值，缺省是5秒内20次调用失败，就会启动熔断机制。熔断机制的注解是`@HystrixCommand`

#### 断路器开启或者关闭的条件

1. 当满足一定的阈值的时候（默认10s内超过20个请求次数）
2. 当失败率达到一定的时候（默认10s内超过50%请求失败）
3. 到达以上阈值，断路器将会开启
4. 当开启的时候，所有请求都不会进行转发
5. 一段时间之后（默认是5s）,这个时候断路器是半开状态，会让其中一个请求进行转发。如果成功，断路器会关闭，若失败，继续开启。重复4和5

#### 断路器打开之后

![](http://img.jared.top/202011251803_823.png)



### 服务限流

秒杀高并发等操作，严禁一蜂窝的过来拥挤，大家排队，一秒钟N个，有序进行



## `Hystrix`工作流程

![](https://segmentfault.com/img/bV0mfx?w=1372&h=667/view)

## 服务监控`HystrixDashboard`