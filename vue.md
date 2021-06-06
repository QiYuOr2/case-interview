---
description: Vue相关面试题
---

# Vue

## 对 MVVM 的理解

MVVM 即 Model-View-ViewModel，View 通过 ViewModel 的 DOM Listeners 将事件绑定到 Model 上，而 Model 则通过 ViewModel 的 Data Bindings 来管理 View 中的数据，ViewModel 起到一个桥梁作用

## Vue 双向绑定

Vue2.x 利用 Object.definePropetry 监听数据变化（Vue3 利用 Proxy），使数据具有可观测性，结合发布订阅模式，在数据变化时更新视图

- 利用 definePropetry 或 Proxy 生成 Observer 针对对象或对象的属性进行“数据劫持”，在属性发生变化后通知订阅者
- 解析器 Complie 解析模板中的指令，收集指令所依赖的方法和数据，等待数据变化然后渲染
- Watcher 作为 Observer 和 Complie 的桥梁，它将接收到的 Observer 产生的变化，并根据 Complie 提供的指令进行视图渲染，使得视图发生变化。

## defineProperty 和 Proxy 的优劣

Object.defineProperty 兼容性较好，但不能直接监听数组的变化，只能监听对象的属性(有时需要深层遍历)

Proxy 可以直接监听数组的变化和对象的变化

## computed 和 watch 的区别

**computed**:

- 用来计算值

- 支持缓存，只有依赖的数据发生了变化才会重新计算
- 不支持异步，当 computed 中有异步操作时，无法监听数据的变化

**watch**：

- 用来观察（比如监听路由变化）
- 不支持缓存，页面重新渲染时会重新执行
- 接收两个参数，第一个是新的值，第二个是旧的值

## v-if 和 v-for

v-if 和 v-for 不能一起使用，因为 v-for 的优先级比 v-if 高，当处于同一节点时 v-if 会分别运行在每个循环中
