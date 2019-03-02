# DOMContentLoaded

当 HTML 文档解析完成就会触发 DOMContentLoaded，而所有资源加载完成之后，load 事件才会被触发。

浏览器向服务器请求到了 HTML 文档后便开始解析，**产物是 DOM（文档对象模型），到这里 HTML 文档就被加载和解析完成了**。如果有 CSS 的会根据 CSS 生成 CSSOM（CSS 对象模型），然后再由 DOM 和 CSSOM 合并产生渲染树。有了渲染树，知道了所有节点的样式，下面便根据这些节点以及样式计算它们在浏览器中确切的大小和位置，这就是布局阶段。有了以上这些信息，下面就把节点绘制到浏览器上

JavaScript 可以阻塞 DOM 的生成，也就是说当浏览器在解析 HTML 文档时，如果遇到 <script>，便会停下对 HTML 文档的解析，转而去处理脚本。如果脚本是内联的，浏览器会先去执行这段内联的脚本，如果是外链的，那么先会去加载脚本，然后执行。在处理完脚本之后，浏览器便继续解析 HTML 文档。

当文档中没有脚本时，浏览器解析完文档便能触发 DOMContentLoaded 事件；如果文档中包含脚本，则脚本会阻塞文档的解析，而脚本需要等 CSSOM 构建完成才能执行。在任何情况下，DOMContentLoaded 的触发不需要等待图片等其他资源加载完成。

## defer async

当 HTML 文档被解析时如果遇见 defer 脚本，则在后台加载脚本，文档解析过程不中断，而等文档解析结束之后，defer 脚本执行。另外，defer 脚本的执行顺序与定义时的位置有关。

当 HTML 文档被解析时如果遇见 async 脚本，则在后台加载脚本，文档解析过程不中断。脚本加载完成后，文档停止解析，脚本执行，执行结束后文档继续解析。

DOMContentLoaded 只有在 defer 脚本执行结束后才会被触发。 

如果 script 标签中包含 async，则 HTML 文档构建不受影响，解析完毕后，DOMContentLoaded 触发，而不需要等待 async 脚本执行、样式表加载等等。

添加了 async 标签的脚本加载完毕的时间无非有两种情况，一种是 HTML 还没有被解析完，那么 HTML 停止解析，去执行脚本；另一种是 HTML 解析完毕，直接去执行脚本。而 DomContentLoaded 事件只关注 HTML 是否被解析完，而不管 async 脚本。

---

如果文档中包含脚本，则脚本会阻塞文档的解析，而脚本需要等 CSSOM 构建完成才能执行，等待的是script标签之前的cssom构建完成

[DOMContentLoaded](http://link.zhihu.com/?target=https%3A//developer.mozilla.org/zh-CN/docs/Web/Events/DOMContentLoaded) 事件必须等待其所属script之前的样式表加载解析完成才会触发。

---

如果script中使用defer属性，表明在HTML解析完成后才执行js的内容，这时候的HTML已经解析完成了，为什么不直接触发DOMContentLoaded事件，而是等待 CSSOM 构建完成和defer脚本的执行完成后才触发DOMContentLoaded事件？

先看 [HTML Standard](http://link.zhihu.com/?target=https%3A//html.spec.whatwg.org/multipage/parsing.html%23the-end) 第 3 条，当有 "ready to be parser-executed" 的标签时，就要执行带有这个标签的脚本，这一步是在第 4 步之前。再到 [HTML Standard](http://link.zhihu.com/?target=https%3A//html.spec.whatwg.org/multipage/scripting.html) 中搜索 "the element has a defer attribute"，就会发现当 script 中 defer 为 true 时，会被打上上述标签。

> https://zhuanlan.zhihu.com/p/25876048
>
> https://www.w3.org/TR/html5/syntax.html#the-end