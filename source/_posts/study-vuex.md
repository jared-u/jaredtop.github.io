---
title: vuex 学习
tags:
  - vue
categories:
  - vue
comments: true
description: vuex学习历程，根据两个小项目
abbrlink: e61720a
date: 2020-09-16 17:21:31
top:
---
# Vuex 是什么？
Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。
这个状态自管理应用包含以下几个部分：
 * state，驱动应用的数据源；
 * view，以声明方式将 state 映射到视图；
 * actions，响应在 view 上的用户输入导致的状态变化。

## 核心概念
### state
 1. vuex管理的状态对象
 2. 它应该是唯一的
    ```javascript
    const state = {
        xxx: initValue
    }
    ```

### mutations
 1. 包含多个直接更新state的方法(回调函数)的对象
 2. 谁来触发：action中的commit('mutation名称')
 3. 只能包含同步的代码，不能写异步代码
    ```javascript
    const mutations = {
        yyy (state, data) {
            // 更新state的某个属性
        }
    }
    ```

### actions
 1. 包含多个事件回调函数的对象
 2. 通过执行：commit()来触发mutation的调用,间接更新state
 3. 谁来触发：组件中：$store.dispatch('action名称', data1) //'zzz'
 4. 可以包含异步代码（定时器，ajax）
    ```javascript
    const actions = {
        zzz({commit, state}, data1) {
            commit('yyy', {data1})
        }
    }
    ```

### getters
 1. 包含多个计算属性（get）的对象
 2. 谁来读取： 组件中： $store.getters.xxx
    ```javascript
    const getters = {
        mmm (state) {
            return ...
        }
    }
    ```

### modules
 1. 包含多个module
 2. 一个module是一个store的配置对象
 3. 与一个组件（包含有共享数据）对应

### 向外暴露store对象
```javascript
export default new Vuex.Store({
  state,
  mutations,
  actions,
  getters
})
```

### 组件中
```javascript
import {mapState}  from 'vuex'
import TodoItem from './TodoItem.vue'
export default {
computed: {
  ...mapState(['todos'])
},
components: {
  TodoItem
}
}
```

### 映射store
```javascript
import store from "./store";
let vm = new Vue({
  store
})
```
### store对象
 1. 所有用vuex管理的组件中都多了一个属性$store，它就是一个store对象
 2. 属性
    * state:注册的state对象
    * getters:注册的getters对象
 3. 方法
    dispath(actionName,data):分发调用action

