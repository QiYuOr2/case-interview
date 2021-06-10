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

## 生命周期

### Vue2.x

分为四个过程，创建（`beforeCreate`, `created`）、挂载（`beforeMount`, `mounted`）、更新（`beforeUpdate`, `updated`）、销毁（`beforeDestroy`, `destroyed`）

- beforeCreate：实例初始化，初始化非响应式变量
- created：实例初始化完成（未挂载 DOM）
- beforeMount：编译模板，调用 render 函数
- Mounted：完成双向绑定，完成 DOM 挂载，可以在此进行数据请求（ajax）
- beforeUpdate：响应式数据更新时触发，DOM 还未重新渲染
- Updated：DOM 重新渲染和打补丁
- beforeDestroy：销毁前，可做一些删除提示，销毁定时器，解绑全局事件
- destroyed：实例销毁后调用

用`keep-alive`包裹的组件在切换时不会销毁，而是缓存到内存中并执行 `deactivated` 钩子函数，命中缓存渲染后会执行 `activated` 钩子函数。

### Vue3.0

- beforeCreate 和 created -> setup()
- beforeMount -> onBeforeMount
- mounted -> onMounted
- beforeUpdate -> onBeforeUpdate
- updated -> onUpdated
- beforeDestroy -> onBeforeUnmount
- destroyed -> onUnmounted

## 子组件和父组件执行顺序

**加载渲染：**

父 beforeCreate -> 父 created -> 父 beforeMount -> 子 beforeCreate -> 子 created -> 子 beforeMount -> 子 mounted -> 父 mounted

**更新：**

父 beforeUpdate -> 子 beforeUpdate -> 子 updated -> 父 updated

**销毁：**

父 beforeDestory -> 子 beforeDestory-> 子 destoryed-> 父 destoryed

## 路由懒加载

使用箭头函数配合 import 动态加载

```javascript
const router = new VueRouter({
  routes: [{ path: '/list', component: () => import('@/components/list.vue') }],
});
```

## 路由 History 模式和 Hash 模式

### Hash 模式

默认模式，`http://abc.com/#/list`，hash 值为`#/list`

因为 hash 值不会出现在 HTTP 请求中，因此改变 hash 值不会重新加载页面

原理：监听 hash 变化`onhashchange`

```javascript
window.onhashchange = function (event) {
  console.log(event.oldURL, event.newURL);
  let hash = location.hash.slice(1);
};
```

### History 模式

传统的路由模式，`http://abc.com/list`

相对来说比 hash 更好看，但是需要后台配置支持

要做到修改 url 又不发送请求，使用了 HTML5 History 的 API`pushState()`和`replaceState()`

```javascript
// 启用history的前台配置
const router = new VueRouter({
  mode: 'history',
  routes: [...]
});
```

## 动态路由（路由传参）

### param

路由定义

```javascript
const router = new VueRouter({
  routes: [{ name: 'list', path: '/list/:id', component: List }],
});
```

跳转

```html
<router-link :to="{ name: 'list', params: { id: userId } }"></router-link>
<router-link :to="'/list/' + userId"></router-link>
```

```javascript
// vue2.x
this.$router.push({ name: 'list', params: { id: userId } });
this.$router.push('/list/' + userId);
// vue3
const router = useRouter();
router.push({ name: 'list', params: { id: userId } });
router.push('/list/' + userId);
```

获取参数`this.$route.params.id`，vue3 中使用`useRoute`和`route.params.id`

### query

query 传参使用普通的路由定义即可（`{path: '/list'}`）

跳转时将 params 变为 query`{ name: 'list', query: { id: userId } }`，如果使用拼接字符串的方式，则需要通过`?`、`&`和`=`将各个参数连接起来`/list?title=abc&author=ef`

获取参数通过`this.$query.id`，vue3 中使用`useRoute`和`route.query.id`

