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

原理：script标签的src不被同源策略约束。（服务端传回的数据用约定好的函数包裹住，`callback({ name: 'xmy', age: 18 })`）

缺点：只支持get请求不支持post请求，无法传json类型的参数；JSONP本质是一种代码注入，存在安全问题

```javascript
function jsonp({ url, cb, success }) {
    const scriptTag = document.createElement('script');
    scriptTag.src = url;
    window[cb] = (res) => {
        success(res);
    };
    document.body.append(scriptTag);
}
```

## 实现ajax

```javascript
function ajax({ method, url, success, fail }) {
    const xhr = new XMLHttpRequest();
    xhr.onreadystatechange = () => {
        if (xhr.readyState === 4) {
            if (xhr.status === 200) {
                success(xhr.response);
            } else {
                fail(xhr.response);
            }
        }
    };
    xhr.onerror = (e) => fail(e);
    xhr.open(method, url, true);
    xhr.send(null);
}
```

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

## 6种继承方式

```js
// 父类
function Animal(name) {
    this.name = name;
}
Animal.prototype.say = function() {
    console.log('我是', this.name);
};
```

**原型链继承**

让实例的原型等于父类的实例来实现继承

```js
function Cat(name) {
    this.name = name;
}
Cat.prototype = new Animal();
let cat = new Cat('cat');
cat.say();
console.log(cat instanceof Animal); // true
```

缺点：无法向父类构造函数传参；单一继承；所有新实例都会共享父类实例的属性（一个实例修改了原型属性，另一个实例也会被修改）

**借助构造函数继承**

用.call()或.apply()将父类构造函数引入子类函数

```js
function Cat(name) {
    Animal.call(this, name);
}
let cat = new Cat('cat');
cat.say(); // error
console.log(cat instanceof Animal); // false
```

缺点：每个新实例都有父类构造函数的副本；只能继承父类构造函数内的属性；无法实现父类构造函数的重用

**组合继承**

原型链继承和借助构造函数继承的结合

```javascript
function Cat(name) {
    Animal.call(this, name);
}
Cat.prototype = new Animal();
let cat = new Cat('cat');
cat.say();
console.log(cat instanceof Animal); // true
```

缺点：调用了两次父类构造函数

**原型式继承**

本质上是对参数对象的一个浅拷贝

```javascript
function extend(obj) {
    function F() {}
    F.prototype = obj;
    return new F();
}
let animal = new Animal();
let cat = extend(animal);
cat.name = 'cat';
cat.say();
console.log(cat instanceof Animal); // true
```

缺点：父类的属性会被所有子类共享；子类构建实例不能传参

**寄生式继承**

原型式继承的增强版

```javascript
function createObject(obj) {
    function F() {}
    F.prototype = obj;
    return new F();
}
function extend(obj) {
    const clone = createObject(obj);
    // 通过某种方式增强这个对象
    clone.name = 'cat';
    return clone;
}
```

没啥用

**寄生组合式继承**

```javascript
function createObject(obj) {
    function F() {}
    F.prototype = obj;
    return new F();
}
function inhertPrototype(subType, superType) {
    // 复制父类原型
    const prototype = createObject(superType.prototype);
    prototype.constructor = subType; // 增强对象
    subType.prototype = prototype;
}
function Cat(name) {
    Animal.call(this, name);
}
inhertPrototype(Cat, Animal);
let cat = new Cat('cat');

```

## 实现map、reduce

```javascript
Array.prototype._map = function (cb) {
    const newArr = [];
    for (let i = 0; i < this.length; i++) {
        newArr.push(cb(this[i], i));
    }
    return newArr;
};
```

```javascript
Array.prototype._reduce = function (cb, init) {
    let result = init || this[0]
    for (let i = init ? 0 : 1; i < this.length; i++) {
        result = cb(result, this[i], i, this);
    }
    return result;
};
```


## 前端的几种存储方式

cookie、localStorage、sessionStorage、indexedDB等

**cookie**
`document.cookie = 'username=abc;max-age=86400;path=/';`
- cookie存储以域名划分，不同域名下的cookie是独立的
- 一个域名下存放的cookie是有限的，不同浏览器不一样，一般为20个
- cookie大小有限，不同浏览器不一样，一般为4KB
- cookie会始终在同源的http请求中携带，还可以在服务端中设置`httpOnly`，这样就无法通过js在客户端操作cookie
- cookie可以通过`domain`和`path`设置生效的域名（当前域名下的子域名），`domain=test.com;path=/admin`
- cookie可以设置过期时间`expires`或`max-age`，`expires`设置的是cookie失效的时间点（必须是GMT格式`new Date().toGMTString()`），`max-age`设置的是有效时长（秒）
- `secure`，只有当使用HTTPS或其他安全协议时，包含`secure`的cookie才会被发送

## 原型和原型链
### 对原型和原型链的理解
**原型**
在JavaScript中使用构造函数来创建一个对象，每个构造函数的内部有一个`prototype`属性，它的属性值是一个对象，这个对象包含了可以由该构造函数的所有实例共享的属性和方法。当通过构造函数实例化一个对象后，这个对象内部将包含一个指针指向构造函数`prototype`对应的值，这个指针就被称为对象的原型，在大部分浏览器中通常是`__proto__`（该属性不是规范中规定的），我们可以通过`Object.getPrototypeOf()`来获取对象的原型。
**原型链**
当访问一个对象的属性时，如果这个对象内部不存在这个属性，那么它就回去它的原型对象里找这个属性，这个原型对象又会有自己的原型，依次这样连接下去就形成了原型链。
原型链的尽头一般来说都是`Object.prototype`所以这就是为什么所有对象都有一些功能的方法（例如`toString()`）的原因。
注意：JavaScript对象是通过引用来传递的，创建的每个实例中并没有一份属于自己的原型副本，当修改原型时，与之相关的实例对象也会继承这一改变。

### 原型链的终点
原型链的终点是`Object.prototype.__proto__`，而`Object.prototype.__proto__ === null`，所以原型链的终点是`null`。

## 闭包
### 闭包的理解
闭包是指有权访问另外一个函数作用域中的变量的函数。
在JavaScript中，变量的作用域属于函数作用域，在函数执行后作用域就会被清理，但是由于闭包是建立在一个函数内部的子函数，由于其可以访问上级作用域的原因，即时上级函数执行完，作用域也不会随之销毁，这时的子函数便拥有了访问上级作用域中变量的权限，这样就形成了闭包。
### 闭包解决了什么问题
由于闭包可以缓存上级作用域，那么就使得函数外部打破了函数作用域的束缚，可以访问函数内的变量。由于这样的特性，回调函数拥有了访问和操作上级作用域的能力，提高了极大的便利。
### 闭包的应用
闭包在JavaScript中随处可见，比如Ajax的成功和失败的回调、事件绑定的回调、在一个函数中返回另一个匿名函数。

## 执行上下文
当JavaScript引擎解析到可执行代码的时候，就会创建一个执行上下文
### 分类
- **全局执行上下文**
任何不在函数内部的都是全局执行上下文，它首先会创建一个全局对象（浏览器中是window），并且设置this的值等于这个全局对象，一个程序中只有一个全局执行上下文。
- **函数执行上下文**
每当一个函数被调用时，都会创建一个新的函数执行上下文（不管这个函数是不是被重复调用的）
- **eval函数执行上下文** eval函数独有的

### 执行上下文栈
- JavaScript引擎使用执行上下文栈管理执行上下文
- 当JavaScript执行代码时，首先遇到全局代码，会创建一个全局执行上下文并且压入执行栈中，每当遇到一个函数调用，就会为该函数创建一个新的执行上下文并压入栈顶，引擎会执行位于执行上下文栈顶的函数，当函数执行完成之后，执行上下文从栈中弹出，继续执行下一个上下文。当所有的代码都执行完毕之后，从栈中弹出全局执行上下文。