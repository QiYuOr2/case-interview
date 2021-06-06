---
description: HTML & CSS相关面试题
---

# HTML/CSS

## DOCTYPE 是什么？三种模式的区别？

DOCTYPE 是文档定义类型（DTD），必须声明在 HTML 文档的第一行，用来规范文档使用哪种方式解析 HTML。三种模式分别是标准模式、接近标准模式、怪异模式，标准模式使用 W3C 标准解析渲染页面，怪异模式会模拟旧的浏览器解析，接近标准模式介于两者之间。

## src 与 href 的区别?

`src`会将资源嵌入到当前文档中，而`href`会建立一个关联指向资源。

（网上有许多观点认为`src`属性代表了资源会阻塞浏览器，实际上并非如此，img 标签引入图片也是使用的`src`属性，在实际体验中我们可以看到，如果图片加载较慢会产生一种页面加载完成，只有图片所在的部分是空白的效果，另外 HTML5 中新增的`async`和`defer`属性也能证明）

## script 标签中 async 和 defer 的区别？

- 两者都可以让 script 中的脚本加载不阻塞浏览器
- async 属性会让后续的文档加载与 js 脚本的加载和执行都是并行进行的，并且多个含有 async 属性的 script 标签不保证执行顺序
- defer 属性会让后续的文档加载与 js 脚本的加载并行进行，js 脚本的执行会在文档加载完成后进行，多个含有 defer 属性的 script 会按照顺序执行

## 盒子模型

标准盒模型：`box-sizing: content-box` 设置的 width 和 height 就是内容区域的宽和高，盒子的总宽高是 width/height + padding + border + margin

怪异盒模型：`box-sizing: border-box` 设置的 width 和 height 是包括了 border 和 padding 宽和高，也就是说盒子的总宽高是 width/height + margin

## margin 的塌陷与合并问题

塌陷：父子元素之间的问题，垂直方向上的 margin 会重叠在一起，取其中的最大值

合并：兄弟元素之间的问题，垂直方向上的 margin 会重叠在一起，取其中的最大值

解决方案：都可以通过触发 BFC 解决

## 关于 BFC、IFC

FC：Format Context 格式化上下文，是 W3C 规范中的一个概念，它是页面中的一个渲染区域，并且有一套渲染规则，决定了子元素如何布局，以及和其他元素之间的关系和作用。

BFC 为块级格式化上下文，IFC 为行级格式化上下文，其他常见的还有 GFC 和 FFC

触发 BFC 的方法：

- body 根元素
- float 不为 none
- position 为 absolute 或 fixed
- display 为 inline-block、flex、table-cell
- overflow 不为 visible

## CSS 定位都有哪些，分别相对于谁？

- `static` 默认
- `relative` 相对定位，相对于原位置
- `absolute` 绝对定位，相对于已经设定过的父级元素，没有就相对于 html
- `fixed` 固定定位，相对于浏览器窗口
- `sticky` 粘性定位，新增的，类似于 relative 与 fixed 的结合版

## 雪碧图（精灵图），优缺点

雪碧图是将多个小图标合成一张大图，使用时设置成背景图，然后通过修改`background-position`和`background-size`来控制要显示的图片

优点：

- 减少图片的请求，从而减少 HTTP 请求的数量
- 可以提前加载资源

缺点：

- 维护成本较高，改动图标时要改动整张图
- HTTP2 之后不存在加载优势，HTTP2 采用多路复用，多张图片的请求可以用同一个连接

## 浏览器渲染页面的过程

1. 根据 HTML 解析生成 DOM 树

2. 根据 CSS 解析生成 CSSOM 树

   将 CSS 解析为具有树形结构的样式对象

3. 根据 DOM 树和 CSSOM 树构建 render 树

   将 CSSOM 附加到 DOM 上，根据盒模型进行布局计算，计算各个元素所在的位置。

   完成布局计算之后如果进行 DOM 修改，会导致浏览器重新渲染部分或全部文档，这个过程叫做回流

4. 根据 render 树进行布局和渲染 render layer 分层

   为了方便处理 z-index、定位等问题，浏览器会根据 render 树生成 render layer 分层树

5. 根据计算出来的数据绘制页面

   逐步绘制各个分层

   当页面中元素的样式改变，并且这个样式不影响元素的位置时，浏览器会重新绘制他，这个过程叫做重绘。

回流的开销 > 重绘的开销

避免回流与重绘：

- 不使用 table 布局，一个小改动就可能引起 table 重新进行布局
- 避免一条一条修改 DOM 样式，而是修改类名
- 避免使用 CSS 表达式，比如 calc()，会反复执行不停计算，消耗大量资源
- 避免频繁操作 DOM，可以通过创建一个 documentFragment，在上面执行完 DOM 操作后将 documentFragment 添加到文档中；也可以在操作 DOM 时将其设置为`display:none`，操作完成后显示

## transform 代替定位实现动画的优势

transform 不会引起回流和重绘，同时可以通过 transform 开启 GPU 加速，提高渲染速度，但相应的也会占更多内存

```css
.box {
  transform: translate3d(0, 0, 0);
}
```

## HTML Drag API

- dragstart：事件主体是被拖放的元素，开始拖放时触发
- drag：事件主体是被拖放的元素，拖放时触发
- dragenter：事件主体是目标元素，拖放元素进入某个元素时触发
- dragover：事件主体是目标元素，拖着元素在某个元素内移动触发
- dragleave：事件主体是目标元素，拖放元素离开目标元素时触发
- drop：事件主体是目标元素，在目标元素接收拖放元素时触发
- dragend：事件主体是被拖放的元素，整个拖放操作结束后触发

## 移动端布局解决方案

- Flex 布局
- 百分比结合媒体查询
- 使用 rem

rem 是根元素字体大小，em 是当前元素字体大小
