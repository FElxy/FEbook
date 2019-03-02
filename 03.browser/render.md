# 浏览器渲染原理

## 工作流程

1. 解析HTML/SVG/XHTML产生一个DOM Tree，解析CSS会产生CSS规则树，Javascript，主要是通过DOM API和CSSOM API来操作DOM Tree和CSS Rule Tree。

2. 解析完成后，浏览器引擎会通过DOM Tree 和 CSS Rule Tree 来构造 Rendering Tree。

   - Rendering Tree 渲染树并不等同于DOM树，因为一些像Header或display:none的东西就没必要放在渲染树中了。

   - CSS 的 Rule Tree主要是为了完成匹配并把CSS Rule附加上Rendering Tree上的每个Element。也就是DOM结点。也就是所谓的Frame。

   - 然后，计算每个Frame（也就是每个Element）的位置，这又叫layout和reflow过程。

3. 最后通过调用操作系统Native GUI的API绘制。

Webkit不像Firefox要用两个树来干这个，Webkit也有Style对象，它直接把这个Style对象存在了相应的DOM结点上了。

1. 计算CSS样式
2. 构建Render Tree
3. Layout – 定位坐标和大小，是否换行，各种position, overflow, z-index属性 ……

- Repaint——屏幕的一部分要重画，比如某个CSS的背景色变了。但是元素的几何尺寸没有变。

- Reflow——意味着元件的几何尺寸变了，我们需要重新验证并计算Render Tree。是Render Tree的一部分或全部发生了变化。这就是Reflow，或是Layout。（**HTML使用的是flow based layout，也就是流式布局，所以，如果某元件的几何尺寸发生了变化，需要重新布局，也就叫reflow**）reflow 会从<html>这个root frame开始递归往下，依次计算所有的结点几何尺寸和位置，在reflow过程中，可能会增加一些frame，比如一个文本字符串必需被包装起来。



- 当你增加、删除、修改DOM结点时，会导致Reflow或Repaint
- 当你移动DOM的位置，或是搞个动画的时候。
- 当你修改CSS样式的时候。
- 当你Resize窗口的时候（移动端没有这个问题），或是滚动的时候。
- 当你修改网页的默认字体时。

display:none会触发reflow，而visibility:hidden只会触发repaint，因为没有发现位置变化。

基本上来说，reflow有如下的几个原因：

- Initial。网页初始化的时候。
- Incremental。一些Javascript在操作DOM Tree时。
- Resize。其些元件的尺寸变了。
- StyleChange。如果CSS的属性发生变化了。
- Dirty。几个Incremental的reflow发生在同一个frame的子树上。



页面初次渲染

浏览器窗口大小改变

元素尺寸、位置、内容发生改变

元素字体大小变化

添加或者删除可见的 dom 元素

激活 CSS 伪类（例如：:hover）

查询某些属性或调用某些方法 

- clientWidth、clientHeight、clientTop、clientLeft
- offsetWidth、offsetHeight、offsetTop、offsetLeft
- scrollWidth、scrollHeight、scrollTop、scrollLeft
- getComputedStyle()
- getBoundingClientRect()
- scrollTo()

## reflow/repaint

**重绘(repaint)**: 当元素样式的改变不影响布局时，浏览器将使用重绘对元素进行更新，此时由于只需要UI层面的重新像素绘制，因此 **损耗较少**

**回流(reflow)**: 当元素的尺寸、结构或触发某些属性时，浏览器会重新渲染页面，称为回流。此时，浏览器需要重新经过计算，计算后还需要重新页面布局，因此是较重的操作。

## 减少reflow/repaint

* 不要一条一条地修改DOM的样式，使用class修改

* 把DOM离线后修改
  * 使用documentFragment 对象在内存里操作DOM
  * 先把DOM给display:none(有一次reflow)，然后你想怎么改就怎么改。比如修改100次，然后再把他显示出来。
  * clone一个DOM结点到内存里，然后想怎么改就怎么改，改完后，和在线的那个的交换一下。
* 把DOM节点存为局部变量
* 尽可能的修改层级比较低的DOM
* 为动画的HTML元件使用fixed或absoult的position，那么修改他们的CSS是不会reflow的。
* 不要使用table布局

## Load 和 DOMContentLoaded 区别

Load 事件触发代表页面中的 DOM，CSS，JS，图片已经全部加载完毕。

DOMContentLoaded 事件触发代表初始的 HTML 被完全加载和解析，不需要等待 CSS，JS，图片加载。

## 图层

一般来说，可以把普通文档流看成一个图层。特定的属性可以生成一个新的图层。不同的图层渲染互不影响，所以对于某些频繁需要渲染的建议单独生成一个新图层，提高性能。但也不能生成过多的图层，会引起反作用。

通过以下几个常用属性可以生成新图层

- 3D 变换：translate3d、translateZ
- will-change
- video、iframe 标签
- 通过动画实现的 opacity 动画转换
- position: fixed

### 普通图层和复合图层composite

浏览器渲染的图层一般包含两大类：普通图层以及复合图层

普通文档流内可以理解为一个复合图层（这里称为默认复合层，里面不管添加多少元素，其实都是在同一个复合图层中）

absolute布局（fixed也一样），虽然可以脱离普通文档流，但它仍然属于默认复合层。

可以通过硬件加速的方式，声明一个新的复合图层，它会单独分配资源 （当然也会脱离普通文档流，这样一来，不管这个复合图层中怎么变化，也不会影响默认复合层里的回流重绘）

GPU中，各个复合图层是单独绘制的，所以互不影响



将该元素变成一个复合图层，就是传说中的硬件加速技术

- 最常用的方式：translate3d、translateZ
- opacity属性/过渡动画（需要动画执行的过程中才会创建合成层，动画没有开始或结束后元素还会回到之前的状态）
- `<video><iframe><canvas><webgl>`等元素

### 复合图层的作用？

一般一个元素开启硬件加速后会变成复合图层，可以独立于普通文档流中，改动后可以避免整个页面重绘，提升性能

## css加载是否会阻塞dom树渲染？

- css加载不会阻塞DOM树解析（异步加载时DOM照常构建）
- 但会阻塞render树渲染（渲染时需等css加载完毕，因为render树需要css信息）

因为你加载css的时候，可能会修改下面DOM节点的样式， 如果css加载不阻塞render树渲染的话，那么当css加载完之后， render树可能又得重新重绘或者回流了，这就造成了一些没有必要的损耗。 所以干脆就先把DOM树的结构先解析完，把可以做的工作做完，然后等你css加载完之后， 在根据最终的样式来渲染render树，这种做法性能方面确实会比较好一点。

## 浏览器是多进程的

### 浏览器都包含哪些进程？

Browser进程：浏览器的主进程（负责协调、主控），只有一个。作用有

- 负责浏览器界面显示，与用户交互。如前进，后退等
- 负责各个页面的管理，创建和销毁其他进程
- 将Renderer进程得到的内存中的Bitmap，绘制到用户界面上
- 网络资源的管理，下载等

第三方插件进程：每种类型的插件对应一个进程，仅当使用该插件时才创建

GPU进程：最多一个，用于3D绘制等

浏览器渲染进程（浏览器内核）（Renderer进程，内部是多线程的）：默认每个Tab页面一个进程，互不影响。主要作用为

- 页面渲染，脚本执行，事件处理等

页面的渲染，JS的执行，事件的循环，都在渲染进程内进行。

渲染线程包括多个进程：

1. GUI渲染线程

- - 解析HTML，CSS，构建DOM树和RenderObject树，布局和绘制等。
  - 需要重绘（Repaint）或由于某种操作引发回流(reflow)时
  - GUI渲染线程与JS引擎线程是互斥的，当JS引擎执行时GUI线程会被挂起，GUI更新会被保存在一个队列中等到JS引擎空闲时立即被执行。

1. JS引擎线程

- - 也称为JS内核，负责处理Javascript脚本程序。（例如V8引擎）
  - S引擎一直等待着任务队列中任务的到来，然后加以处理

1. 事件触发线程

- - 当对应的事件符合触发条件被触发时，该线程会把事件添加到待处理队列的队尾，等待JS引擎的处理
  - 当JS引擎执行代码块如setTimeOut时（也可来自浏览器内核的其他线程,如鼠标点击、AJAX异步请求等），会将对应任务添加到事件线程中

1. 定时触发器线程

2. 1. setInterval与setTimeout所在线程

3. 异步http请求线程

- - 在XMLHttpRequest在连接后是通过浏览器新开一个线程请求
  - 将检测到状态变更时，如果设置有回调函数，异步线程就产生状态变更事件，将这个回调再放入事件队列中。再由JavaScript引擎执行。



JS引擎是单线程的，而且JS执行时间过长会阻塞页面，可以使用webworker执行耗时的工作

- 创建Worker时，JS引擎向浏览器申请开一个子线程（子线程是浏览器开的，完全受主线程控制，而且不能操作DOM）
- JS引擎线程与worker线程间通过特定的方式通信（postMessage API，需要通过序列化对象来与线程交互特定的数据）