# js异步编程模式

比如，避免回调地狱，常见的ajax、事件监听，还是node中文件读取、网络编程、数据库等操作，都离不开异步编程。

## 高阶函数(泛函数)

```
step1(function(res1){
    step2(function(res2){
        step3(function(res3){
            //...
        });
    });
});
```



事件监听

```
f.on("evt", g);
function f(){
    setTimeout(function(){
        f.trigger("evt");
    })
}
```



## 发布/订阅( Pub/Sub )

```
E.subscribe("evt", g);
function f(){
    setTimeout(function () {
    　　// f的任务代码
    　　E.publish("evt");
    }, 1000);
}
```



使用这种模式的实现需要一个事件发布/监听的库。上面代码中使用node原生的`events`模块

## Promise对象

Promise/A+ 规范是对 Promise/A 规范的补充和修改，他出现的目的是为了统一异步编程中的接口，JS中的异步编程是十分普遍的事情，也出现了很多的异步库，如果不统一接口，对开发者来说也是一件十分痛苦的事情。

在Promises/A规范中，每个任务都有三种状态：默认(pending)、完成(fulfilled)、失败(rejected)。

- 默认状态可以单向转移到完成状态，这个过程叫resolve，对应的方法是deferred.resolve(promiseOrValue)；
- 默认状态还可以单向转移到失败状态，这个过程叫reject，对应的方法是deferred.reject(reason)；
- 默认状态时，还可以通过deferred.notify(update)来宣告任务执行信息，如执行进度；
- 状态的转移是一次性的，一旦任务由初始的pending转为其他状态，就会进入到下一个任务的执行过程中。



## generator

`generator`是es6中的一个新的语法。在`function`关键字后添加*即可将函数变为`generator`。

```
const gen = function* () {
    yield 1;
    yield 2;
    return 3;
}
```

执行`generator`将会返回一个遍历器对象，用于遍历`generator`内部的状态。

```
let g = gen();
g.next(); // { value: 1, done: false }
g.next(); // { value: 2, done: false }
g.next(); // { value: 3, done: true }
g.next(); // { value: undefined, done: true }

```

```
const co = reuqire('co');

const task = function* (filepath) {
   let keyword = yield readFile(filepath);
   let count = yield queryDB(keyword);
   let data = yield getData(res.length);
   console.log(data);
});

co(task, './sample.txt');

```



## async/await

function拆分的方式其实仅仅只是拆分代码块，时常会不利于后续维护；

事件发布/监听方式模糊了异步方法之间的流程关系；

 `Promise`虽然使得多个嵌套的异步调用能够通过链式的API进行操作，但是过多的`then`也增加了代码的冗余，也对阅读代码中各阶段的异步任务产生了一定干扰；

通过`generator`虽然能提供较好的语法结构，但是毕竟`generator`与`yield`的语境用在这里多少还有些不太贴切。

```
const printData = async function (filepath) {
   let keyword = await readFile(filepath);
   let count = await queryDB(keyword);
   let data = await getData(res.length);
   console.log(data);
});

printData('./sample.txt');

```



## 总结

- function拆解
- 事件发布/订阅模式
- Promise
- Generator
- async / await