---
title: Spring 常见面试题
tags:
  - [Spring]
  - [面试题]
categories:
  - [Spring]
  - [面试题]
comments: true
description: Spring 常见面试题
abbrlink: '228e4888'
date: 2020-09-04 19:06:17
top:
---
# 常见面试题
## 什么是Spring框架？包含哪些主要模块？
> Spring框架是一个为Java应用程序的开发提供了综合、广泛的基础性支持的Java平台。
> 主要模块： 核心容器、数据访问/集成、Web、AOP（面向切面编程）、工具、消息和测试模块
---
## 使用Spring框架的好处？
 * Dependency Injection（DI）方法使得构造器和JavaBean properties文件中的依赖关系一目了然
 * 与EJB容器相比较，IoC容器更加趋向于轻量级。这样一来IoC容器在有限的内存和CPU资源的情况下进行应用程序的开发和发布就变得十分有利
 * Spring利用了已有的技术，比如ORM框架、logging框架、J2EE、Quartz和JDK Timer，以及其他视图技术
 * Spring框架是按照模块的形式来组织的。
 * Spring提供了一个便捷的事务管理接口
 * 提供相应的测试环境代码

---
## 什么是控制反转（IOC）？什么是依赖注入？

在Java中依赖注入有三种实现方式：构造器注入，setter方法注入，接口注入

## BeanFactory和ApplicationContext 有什么区别？
BeanFactory可以理解为含有bean集合的工厂类。BeanFactory包含了种bean的定义，以便在接收到客户端请求时将对应的bean实例化。    
BeanFactory还能在实例化对象时生成协作类之间的关系。此举将bean自身与bean客户端的配置中解放出来。BeanFactory还包含了bean生命周期的控制，调用客户端的初始化方法(initialization methods)和销毁方法(destruction methods)。  
从表面上看，ApplicationContext如同BeanFactory一样具有bean定义、bean关联关系的设置，根据请求分发bean的功能。但ApplicationContext在此基础上还提供了其它的功能：
> 1. 提供了支持国际化的文本消息
> 2. 统一的资源文件读取方式
> 3. 已在监听器中注册的bean的事件

以下是较常见的ApplicationContext实现方式：
1. ClassPathXmlApplicationContext:从classpath的XML配置文件中读取上下文，并生成上下文定义。应用程序上下文从程序环境变量中
```java
ApplicationContext context = new ClassPathXmlApplicationContext("bean.xml");
```
2. FileSystemXmlApplicationContext:由文件系统中的XML配置文件读取上下文。
```java
ApplicationContext context = new FileSystemXmlApplicationContext("bean.xml");
```
3. XmlWebApplicationContext:由Web应用的XML文件读取上下文。
4. AnnotationConfigApplicationContext(基于Java配置启动容器)
---
## Spring有几种配置方式？
将Spring配置到应用开发中有以下三种方式：
> 1.基于XML的配置
> 2.基于注解的配置
> 3.基于Java的配置

### 如何用基于XML配置的方式配置Spring？
========
### 如何用基于Java配置的方式配置Spring？
========
### 如何用基于注解配置的方式配置Spring？
========
## 解释Spring Bean的生命周期？
========
## Spring Bean的作用域区别？
5个范围
 1. singleton：是默认的，这种范围确保不管接受到多个请求，每个容器中只有一个bean的实例，单例的模式由beanFactory自身来维护
 2. prototype：原型范围与单例范围相反，为每一个bean请求提供一个实例
 3. request：在请求bean范围内会为每一个来自客户端的网络请求创建一个实例，在请求完成以后，bean会失效并被垃圾回收器回收。
 4. session：与请求范围类似，确保每个session中有一个bean的实例，在session过去后，bean会随之失效。
 5. global-session：global-session和Portlet应用有关。当你的应用部署在Portlet容器中工作时，它包含很多portlet。如果你想要声明让所有的portlet共用全局的存储变量的话，那么这全局变量需要存储在global-session中。
全局作用域与Servlet中的session作用域效果相同。

## 请举例说明如何在Spring中注入一个Java Collection？
Spring提供了以下四种集合类的配置元素：
 * `<list>`: 该标签用来装配可重复的list值。
 * `<set>`：该标签用来装配没有重复的set值。
 * `<map>`：该标签可用来注入键和值可以为任何类型的键值对。
 * `<props>`：该标签支持注入键和值都是字符串类型的键值对。

## 请解释自动装配模式的区别？
 * no:这是Spring的默认设置，在该设置下自动装配是关闭的，开发者需要自行在bean定义中用标签明确的设置依赖关系。
 * byName：该选项可以根据bean名称设置依赖关系。当向一个bean中自动装配一个属性时，容器将根据bean的名称自动在配置文件中查询一个匹配的bean。如果找到的话，就装配这个属性，如果没找到的话就报错。
 * byType：该选项可以根据bean的类型设置依赖关系。当想一个bean中自动装配一个属性时，容器将根据bean的类型自动在配置文件中查询一个匹配的bean。如果找到的话，就装配这个属性，如果没找到的话就报错。
 * constructor：构造器的自动装配和byType模式类似，但是仅仅适用于与有构造器相同参数的bean，如果在容器总没有找到与构造器参数类型一致的bean，那么将会抛出异常。
 * autodetect：该模式自动探测使用构造器自动装配或byType自动装配。首先，会尝试找合适的带参数的构造器，如果找到的话就是用构造器自动装配，如果在bean内部没有找到相应的构造器或者是无参构造器，容器就会自动选择byType的自动装配方式。

## 如何开启基于注解的自动装配？
要使用`@Autowired`，需要注册`AutowiredAnnotationBeanPostProcessor`，可以有以下两种方式来实现：
 1. 引入配置文件中的`<beans>`下引入`<context:annotation-config>`
```java
<beans>
    <context:annotation-config>
</beans>
```
 2. 在配置文件中直接引入`AutowiredAnnotationBeanPostProcessor`
```java
<beans>
    <bean class="org.springframework.beans.factory.annotation.AutowiredAnnotationBeanPostProcessor">
</beans>
```
