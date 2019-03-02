# 跨域

## 同源策略

不同协议，域名，端口即跨域

限制于

- AJAX 请求不能发送。
- 无法获取DOM元素并进行操作。
- 无法读取Cookie、LocalStorage 和 IndexDB 。

有些请求是不受到跨域限制。例如：WebSocket，script、img、iframe、video、audio标签的`src`属性等。

## proxy

后端提供一个代理，把前端请求转发到跨域请求。

## CORS

Cross-Origin Resource Sharing

服务器端的响应头中一个头信息为`Access-Control-Allow-Origin`，表明接受的跨域请求来源。

如果将`Access-Control-Allow-Origin`的值设置为`*`，则会接受所有域的请求。这时的客户端不需要任何配置即可进行跨域访问。

CORS默认是不会发送cookie, 与`Access-Control-Allow-Origin`相配套的，还有一个叫`Access-Control-Allow-Credentials`的响应头，如果设置为`true`则表明服务器允许该请求内包含cookie信息。

同时，在客户端，还需要在ajax请求中设置`withCredentials`属性为`true`。

## jsonp

只支持`get`请求,`script`标签不受同源策略限制

原理：利用 `<script>` 标签没有跨域限制的漏洞，网页可以得到从其他来源动态产生的 JSON 数据。JSONP请求一定需要对方的服务器做支持才可以。

实现：

* 声明一个回调函数，其函数名(如show)当做参数值，要传递给跨域请求数据的服务器，函数形参为要获取目标数据(服务器返回的data)。
* 创建一个`<script>`标签，把那个跨域的API数据接口地址，赋值给script的src,还要在这个地址中向服务器传递该函数名（可以通过问号传参:?callback=show）。
* 服务器接收到请求后，需要进行特殊的处理：把传递进来的函数名和它需要给你的数据拼接成一个字符串,例如：传递进去的函数名是show，它准备好的数据是`show('我不爱你')`。
* 最后服务器把准备的数据通过HTTP协议返回给客户端，客户端再调用执行之前声明的回调函数（show），对返回的数据进行操作。

## postMessage

* 页面和其打开的新窗口的数据传递
* 多窗口之间消息传递
* 页面与嵌套的iframe消息传递
* 上面三个场景的跨域数据传递

**postMessage()方法允许来自不同源的脚本采用异步方式进行有限的通信，可以实现跨文本档、多窗口、跨域消息传递**。

## websocket

Websocket是HTML5的一个持久化的协议，它实现了浏览器与服务器的全双工通信，同时也是跨域的一种解决方案。WebSocket和HTTP都是应用层协议，都基于 TCP 协议。但是 **WebSocket 是一种双向通信协议，在建立连接之后，WebSocket 的 server 与 client 都能主动向对方发送或接收数据**。同时，WebSocket 在建立连接时需要借助 HTTP 协议，连接建立好了之后 client 与 server 之间的双向通信就与 HTTP 无关了。