---
title: vue 分析
tags:
  - [vue]
categories:
  - [vue]
comments: true
description: vue 分析 基于github上的mvvm实现<a href="http://github.com/DMQ/mvvm" target="_blank">mvvm</a>。了解vue的双向数据绑定原理以及核心代码模块，缓解好奇心的同时了解如何实现双向绑定
abbrlink: 48d08c37
date: 2020-09-08 22:32:21
top:
---
## 数据代理
 1. 数据代理：通过一个对象代理对另一个对象中属性的操作（读）
 2. vue数据代理：通过vm对象来代理data对象中所有属性的操作
 3. 好处：更方便的操作data中的数据
 4. 基本实现流程
   1. 通过Object.defineProperty()给vm添加与data对象的属性对应的属性描述符
   2. 所有添加的属性都包含getter/setter
   3. getter/setter 内部去操作data中对应的属性数据
---

## 模板解析
### 模板解析的基本流程
 1. 将el的所有子节点取出，添加到一个新建的文档fragment对象中
 2. 将fragment中的所有层次子节点递归进行编译解析处理
     * 对大括号表达式文本节点进行解析
     * 对元素节点的指令属性进行解析
         * 事件指令解析
         * 一般指令解析
 3.将解析后的fragment添加到el中显示

### 大括号表达式解析
 1. 根据正则对象得到匹配出的表达式字符串：自匹配/RegExp.$1
 2. 从data中取出表达式对应的属性值
 3. 将属性值设置为文本节点的textContent

### 事件指令解析
 1. 从指令名中取出事件名
 2. 根据指令的值（表达式）从methods中得到对应的事件处理函数对象
 3. 给当前元素节点绑定指定事件名和回调函数的dom事件监听
 4. 指令解析完后，移除此指令属性

### 一般指令解析
 1. 得到指令名和指令值（表达式） ：text/html/class msg/myClass
 2. 从data中根据表达式得到对应的值
 3. 根据指令名确定需要操作元素节点的什么属性
    * v-text---textContent属性
    * v-html---innerHTML属性
    * v-class---className属性
 4. 将得到的表达式的值设置到对应的属性上
 5. 移除元素的指令属性
---

## 数据绑定
### 数据绑定
一旦更新了data中的某个属性数据，所有界面上直接使用或间接使用了此属性的节点都会更新
### 数据劫持
 1. 数据劫持是vue中用来实现数据绑定的一种技术
 2. 基本思想：通过defineProperty()来监视data中所有属性（任意层次）数据的变化，一旦变化就去更新界面

### 四个重要对象
 1. Observer
 2. Dep
    它的实例什么时候创建？
        答：初始化时给data的属性进行数据劫持时创建的
    个数？
        答：与data中的属性一一对应
    Dep的结构？
        答：
        id：标识
        subs：[] n个相关的watcher的容器
 3. Compile
    
 4. Watcher
    它的实例什么时候创建？
        答：初始化解析大括号表达式/一般指令时创建
    个数？
        答：与模板中表达式（不包含事件指令）一一对应
    watcher的结构？
        答：
            this.cb = cb; //用于更新界面的回调
            this.vm = vm; // vm
            this.exp = exp; // 对应的表达式
            this.depIds = {depid, dep}; // 相关的n个dep的容器对象
            this.value = this.get(); // 当前表达式对应的value

## Dep与Watcher的关系
### 什么关系？
    答：多对多的关系
        name ==> Dep ==> n个watcher（模板中有多个表达式使用了此属性：{{a}} v-text="a"）
        表达式 ==> Watcher ==> n个Dep（多层表达式：a.b.c）
### 如何建立的？
    答：data中属性的get()中建立
### 什么时候建立？
    答：初始化的解析模板中的表达式创建Watcher对象时
        vm.name = 'abc' ==> data中的name属性值变化 ==> name的set()调用 ==> dep ==> 相关的所有watcher
         ==> cb() ==> updater

## 双向数据绑定
 1. 双向数据绑定是建立在单向数据绑定（model ==> View）的基础之上的
 2. 双向数据绑定的实现流程：
  1. 在解析v-model指令时，给当前元素添加input监听
  2. 当input的value发生变化时，将最新的值赋值给当前表达式所对应的data属性

## MVVM结构图
![MVVM结构图](/images/mvvm.png)

