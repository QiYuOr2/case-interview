---
description: JavaScript相关面试题
---
# JavaScript

## 事件循环机制

首先，JavaScript是一个单线程的语言，这样的设计让它在同一时间只能做同一件事。作为浏览器的脚本语言而言，JavaScript的主要作用是与用户互动以及操作DOM，采用多线程可能会带来一些比较复杂的问题，比如如果有两个线程同时操作同一个DOM节点，浏览器无法确定以哪个线程为准。

> HTML5提出了Web Worker，允许JavaScript创建多个线程，但是这些线程都是受主线程控制的，并且无法操作DOM

单线程就意味着下一个要想执行必须等待上一个任务结束，为了解决这个问题提高CPU利用率，JavaScript将所有任务分为同步任务和异步任务，同步任务会进入主线程执行栈中执行，而异步任务会进入任务队列，当执行栈中的所有任务都执行完毕后，任务队列中的任务会进入执行栈中执行。

主线程会不断重复这个过程，这就是Event Loop。

## 宏任务和微任务

异步任务又分为宏任务和微任务，他们之间执行的优先级并不相同，微任务总是会先于宏任务执行

宏任务：IO操作、所有定时器、requestAnimationFrame

微任务：Promise.then\Promise.catch\Promise.finally、MutationObserver、process.nextTick（nodejs）

## setTimeout为什么会产生误差

如果当执行栈中所花费的时间大于定时器的时间时，就会产生误差

## 对new的过程和原理的理解

new一个对象发生了以下四步：

1. 创建一个空对象
2. 链接该新对象到该函数的原型对象prototype上
3. 使用新对象调用函数，函数中的this指向新对象
4. 返回新创建的对象

```javascript
function _new() {
    const obj = new Object();
   	const _constructor = [].shift.call(arguments);
    obj.__proto__ = _constructor.prototype;
    const ret = _constructor.apply(obj, arguments);
    return typeof ret === 'object' ? ret : res;
}
```

## 跨域的方式？为什么需要跨域？

之所以需要跨域，是因为浏览器同源策略的约束。同源策略拦截了不同源的请求。

跨域的方式：CORS（添加`Access-Control-Allow-Origin`头）、JSONP、proxy代理

## JSONP原理

原理：script标签的src不被同源策略约束。

缺点：只支持get请求不支持post请求，无法传json类型的参数；JSONP本质是一种代码注入，存在安全问题

## 防抖与节流

防抖：在一段时间内，不管触发了多少次函数，都只认最后一次

```javascript
function debounce(fn, delay) {
	let timer = null;
    return (...args) => {
        clearTimeout(timer);
        timer = setTimeout(() => {
            fn(...args);
        }, delay);
    };
}
```

节流：在一段时间内，不管触发了多少次函数，只认第一次

```javascript
function throttle(fn, delay) {
    let timer = null;
    return (...args) => {
        if(!timer) {
            fn(...args);
            timer = setTimeout(() => {
                timer = null;
            }, delay);
        }
    };
}
```

