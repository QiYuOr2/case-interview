---
description: Vue相关面试题
---
# Vue

## 对MVVM的理解

MVVM即Model-View-ViewModel，View通过ViewModel的DOM Listeners将事件绑定到Model上，而Model则通过ViewModel的Data Bindings来管理View中的数据，ViewModel起到一个桥梁作用

## Vue双向绑定

Vue2.x利用Object.definePropetry监听数据变化（Vue3利用Proxy），使数据具有可观测性，结合发布订阅模式，在数据变化时更新视图

- 利用definePropetry或Proxy生成Observer针对对象或对象的属性进行“数据劫持”，在属性发生变化后通知订阅者
- 解析器Complie解析模板中的指令，收集指令所依赖的方法和数据，等待数据变化然后渲染
- Watcher作为Observer和Complie的桥梁，它将接收到的Observer产生的变化，并根据Complie提供的指令进行视图渲染，使得视图发生变化。

## defineProperty和Proxy的优劣

Object.defineProperty兼容性较好，但不能直接监听数组的变化，只能监听对象的属性(有时需要深层遍历)

Proxy可以直接监听数组的变化和对象的变化

## computed和watch的区别

**computed**:

- 用来计算值

- 支持缓存，只有依赖的数据发生了变化才会重新计算
- 不支持异步，当computed中有异步操作时，无法监听数据的变化

**watch**：

- 用来观察（比如监听路由变化）
- 不支持缓存，页面重新渲染时会重新执行
- 接收两个参数，第一个是新的值，第二个是旧的值

## v-if 和 v-for

v-if和v-for不能一起使用，因为v-for的优先级比v-if高，当处于同一节点时v-if会分别运行在每个循环中

