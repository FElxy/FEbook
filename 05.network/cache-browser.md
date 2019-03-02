# 浏览器缓存

## 强缓存和协商缓存

### 基本过程

- 1）浏览器在加载资源时，根据请求头的`expires`和`cache-control`判断是否命中强缓存，是则直接从缓存读取资源，不会发请求到服务器。
- 2）如果没有命中强缓存，浏览器一定会发送一个请求到服务器，通过`last-modified`和`etag`验证资源是否命中协商缓存，如果命中，服务器会将这个请求返回，但是不会返回这个资源的数据，依然是从缓存中读取资源
- 3）如果前面两者都没有命中，直接从服务器加载资源

相同点：如果命中，都是从客户端缓存中加载资源，而不是从服务器加载资源数据；

不同点：强缓存不发请求到服务器，协商缓存会发请求到服务器。



### 强缓存

#### Expires

Expires是http1.0提出的一个表示资源过期时间的header，它描述的是一个绝对时间，由服务器返回。
Expires 受限于本地时间，如果修改了本地时间，可能会造成缓存失效

```
Expires: Wed, 11 May 2018 07:20:00 GMT
```

#### Cache-Control

Cache-Control 出现于 HTTP / 1.1，优先级高于 Expires ,表示的是相对时间

```
Cache-Control: max-age=315360000
```

`Cache-Control: no-store`: 不缓存数据到本地

`Cache-Control: no-cache`：可以存储在本地缓存区中，在于原始服务器进行新鲜度再验证之前，缓存不能将其提供给客户端使用。

`Cache-Control: public`可以被所有用户缓存（多用户共享），包括终端和CDN等中间代理服务器

`Cache-Control: private`只能被终端浏览器缓存（而且是私有缓存），不允许中继缓存服务器进行缓存

### 协商缓存

当浏览器对某个资源的请求没有命中强缓存，就会发一个请求到服务器，验证协商缓存是否命中，如果协商缓存命中，请求响应返回的http状态为304并且会显示一个Not Modified的字符串

协商缓存是利用的是`【Last-Modified，If-Modified-Since】`和`【ETag、If-None-Match】`这两对Header来管理的

#### Last-Modified，If-Modified-Since

`Last-Modified` 表示本地文件最后修改日期，浏览器会在request header加上`If-Modified-Since`（上次返回的`Last-Modified`的值），询问服务器在该日期后资源是否有更新，有更新的话就会将新的资源发送回来

但是如果在本地打开缓存文件，就会造成 Last-Modified 被修改，所以在 HTTP / 1.1 出现了 ETag

#### ETag、If-None-Match

`Etag`就像一个指纹，资源变化都会导致ETag变化，跟最后修改时间没有关系，`ETag`可以保证每一个资源是唯一的

`If-None-Match`的header会将上次返回的`Etag`发送给服务器，询问该资源的`Etag`是否有更新，有变动就会发送新的资源回来

`ETag`的优先级比`Last-Modified`更高

具体为什么要用`ETag`，主要出于下面几种情况考虑：

- 一些文件也许会周期性的更改，但是他的内容并不改变(仅仅改变的修改时间)，这个时候我们并不希望客户端认为这个文件被修改了，而重新GET；
- 某些文件修改非常频繁，比如在秒以下的时间内进行修改，(比方说1s内修改了N次)，If-Modified-Since能检查到的粒度是s级的，这种修改无法判断(或者说UNIX记录MTIME只能精确到秒)；
- 某些服务器不能精确的得到文件的最后修改时间。



### 状态码区别

- `200`：强缓Expires/Cache-Control存失效时，返回新的资源文件

- `200(from cache)`: 强缓Expires/Cache-Control两者都存在，未过期，Cache-Control优先Expires时，浏览器从本地获取资源成功

- `304(Not Modified )`：协商缓存Last-modified/Etag没有过期时，服务端返回状态码304

  现在的`200(from cache)`已经变成了`from disk cache(磁盘缓存)`和`from memory cache(内存缓存)`两种
  打开chrome控制台看一下网络请求就知道了




分布式系统里多台机器间文件的Last-Modified必须保持一致，以免负载均衡到不同机器导致比对失败；

分布式系统尽量关闭掉ETag(每台机器生成的ETag都会不一样）；



> https://github.com/amandakelake/blog/issues/41

[浅谈Web缓存 | AlloyTeam](http://www.alloyteam.com/2016/03/discussion-on-web-caching/) [浏览器缓存知识小结及应用 - 流云诸葛 - 博客园](http://www.cnblogs.com/lyzg/p/5125934.html) [HTTP 缓存  |  Web  |  Google Developers](https://developers.google.cn/web/fundamentals/performance/optimizing-content-efficiency/http-caching?hl=zh-cn) [大公司里怎样开发和部署前端代码？ - 知乎](https://www.zhihu.com/question/20790576) [HTTP强缓存和协商缓存 - JavaScript学习笔记 - SegmentFault 思否](https://segmentfault.com/a/1190000008956069)

