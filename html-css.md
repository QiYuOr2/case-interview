---
description: HTML & CSS相关面试题
---

# HTML/CSS

## DOCTYPE是什么？三种模式的区别？

DOCTYPE是文档定义类型（DTD），必须声明在HTML文档的第一行，用来规范文档使用哪种方式解析HTML。三种模式分别是标准模式、接近标准模式、怪异模式，标准模式使用W3C标准解析渲染页面，怪异模式会模拟旧的浏览器解析，接近标准模式介于两者之间。

## src与href的区别?

`src`会将资源嵌入到当前文档中，而`href`会建立一个关联指向资源。

（网上有许多观点认为`src`属性代表了资源会阻塞浏览器，实际上并非如此，img标签引入图片也是使用的`src`属性，在实际体验中我们可以看到，如果图片加载较慢会产生一种页面加载完成，只有图片所在的部分是空白的效果，另外HTML5中新增的`async`和`defer`属性也能证明）

## script标签中async和defer的区别？

* 两者都可以让script中的脚本加载不阻塞浏览器
* async属性会让后续的文档加载与js脚本的加载和执行都是并行进行的，并且多个含有async属性的script标签不保证执行顺序
* defer属性会让后续的文档加载与js脚本的加载并行进行，js脚本的执行会在文档加载完成后进行，多个含有defer属性的script会按照顺序执行

## 盒子模型

标准盒模型：`box-sizing: content-box` 设置的width和height就是内容区域的宽和高，盒子的总宽高是width/height + padding + border + margin

怪异盒模型：`box-sizing: border-box` 设置的width和height是包括了border和padding宽和高，也就是说盒子的总宽高是width/height + margin

## margin的塌陷与合并问题

塌陷：父子元素之间的问题，垂直方向上的margin会重叠在一起，取其中的最大值

合并：兄弟元素之间的问题，垂直方向上的margin会重叠在一起，取其中的最大值

解决方案：都可以通过触发BFC解决

## 关于BFC、IFC

FC：Format Context格式化上下文，是W3C规范中的一个概念，它是页面中的一个渲染区域，并且有一套渲染规则，决定了子元素如何布局，以及和其他元素之间的关系和作用。

BFC为块级格式化上下文，IFC为行级格式化上下文，其他常见的还有GFC和FFC

触发BFC的方法：

* body根元素
* float不为none
* position为absolute或fixed
* display为inline-block、flex、table-cell
* overflow不为visible

## CSS定位都有哪些，分别相对于谁？

* `static` 默认
* `relative` 相对定位，相对于原位置
* `absolute` 绝对定位，相对于已经设定过的父级元素，没有就相对于html
* `fixed` 固定定位，相对于浏览器窗口
* `sticky` 粘性定位，新增的，类似于relative与fixed的结合版

## 雪碧图（精灵图），优缺点

雪碧图是将多个小图标合成一张大图，使用时设置成背景图，然后通过修改`background-position`和`background-size`来控制要显示的图片

优点：

* 减少图片的请求，从而减少HTTP请求的数量
* 可以提前加载资源

缺点：

* 维护成本较高，改动图标时要改动整张图
* HTTP2之后不存在加载优势，HTTP2采用多路复用，多张图片的请求可以用同一个连接

## 浏览器渲染页面的过程

1. 根据HTML解析生成DOM树

2. 根据CSS解析生成CSSOM树

   将CSS解析为具有树形结构的样式对象

3. 根据DOM树和CSSOM树构建render树

   将CSSOM附加到DOM上，根据盒模型进行布局计算，计算各个元素所在的位置。

   完成布局计算之后如果进行DOM修改，会导致浏览器重新渲染部分或全部文档，这个过程叫做回流

4. 根据render树进行布局和渲染render layer分层

   为了方便处理z-index、定位等问题，浏览器会根据render树生成render layer分层树

5. 根据计算出来的数据绘制页面

   逐步绘制各个分层

   当页面中元素的样式改变，并且这个样式不影响元素的位置时，浏览器会重新绘制他，这个过程叫做重绘。

回流的开销 > 重绘的开销

避免回流与重绘：

- 不使用table布局，一个小改动就可能引起table重新进行布局
- 避免一条一条修改DOM样式，而是修改类名
- 避免使用CSS表达式，比如calc()，会反复执行不停计算，消耗大量资源
- 避免频繁操作DOM，可以通过创建一个documentFragment，在上面执行完DOM操作后将documentFragment添加到文档中；也可以在操作DOM时将其设置为`display:none`，操作完成后显示

## transform代替定位实现动画的优势

transform不会引起回流和重绘，同时可以通过transform开启GPU加速，提高渲染速度，但相应的也会占更多内存

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

- Flex布局
- 百分比结合媒体查询
- 使用rem

rem是根元素字体大小，em是当前元素字体大小

