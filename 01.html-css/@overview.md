* meta
* 盒模型
* 层叠上下文
* 居中
* 选择器
* grid
* animate

* 居中为什么要使用transform（为什么不使用marginLeft/Top）

* 介绍css3中position:sticky

* `<a>`标签默认事件禁掉之后做了什么才实现了跳转

* react-router里的`<Link>`标签和`<a>`标签有什么区别



当您的页面链接至使用 target="_blank" 的另一个页面时，新页面将与您的页面在同一个进程上运行。 如果新页面正在执行开销极大的 JavaScript，您的页面性能可能会受影响。

此外，target="_blank" 也是一个安全漏洞。新的页面可以通过 window.opener 访问您的窗口对象，并且它可以使用 window.opener.location = newURL 将您的页面导航至不同的网址。

### link 与 @import 的区别

- `link`功能较多，可以定义 RSS，定义 Rel 等作用，而`@import`只能用于加载 css
- 当解析到`link`时，页面会同步加载所引的 css，而`@import`所引用的 css 会等到页面加载完才被加载
- `@import`需要 IE5 以上才能使用
- `link`可以使用 js 动态引入，`@import`不行



---

- [x] **盒模型**  border-box, content-box
- [x] **BFC**  8种，float，position，flex，grid，root，table，inline-block, overflow
- [x] **层叠上下文**  position, z-index
- [x] **垂直居中** 6种， line-height, flex,  table-cell, absolute+transform/margin, verticle-align, calc
- [x] **选择器优先级** !important > 行内样式 > #id > .class > tag > * > 继承 > 默认，样式从右向左解析
- [ ] **动画** css动画，JS动画，二者差异



**script标签到defer和async属性有什么作用和区别**

async属性，表示后续文档的加载和渲染与js脚本的加载和执行是并行进行的，即异步执行。

defer属性，加载后续文档的过程和js脚本的加载(此时仅加载不执行)是并行进行的(异步)，js脚本的执行需要等到文档所有元素解析完成之后，DOMContentLoaded事件触发执行之前。

区别

　　1.defer和async在网络加载过程是一致的，都是异步执行的；

　　2.两者的区别在于脚本加载完成之后何时执行，defer更符合大多数场景对应用脚本加载和执行的要求；

　　3.如果存在多个有defer属性的脚本，那么它们是按照加载顺序执行脚本的；而对于async，它的加载和执行是紧紧挨着的，无论声明顺序如何，只要加载完成就立刻执行，它对于应用脚本用处不大，因为它完全不考虑依赖。

