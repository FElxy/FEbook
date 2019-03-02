# 事件

## 事件机制

事件触发有三个阶段

- document 往事件触发处传播，遇到注册的捕获事件会触发
- 传播到事件触发处时触发注册的事件
- 从事件触发处往 document 传播，遇到注册的冒泡事件会触发

事件触发一般来说会按照上面的顺序进行，但是也有特例，如果给一个目标节点同时注册冒泡和捕获事件，事件触发会按照注册的顺序执行。

## 注册事件

使用 addEventListener 注册事件，该函数的第三个参数可以是布尔值，也可以是对象。

对于布尔值 useCapture 参数来说，该参数默认值为 false 。useCapture 决定了注册的事件是捕获事件还是冒泡事件。对于对象参数来说，可以使用以下几个属性

- capture，布尔值，和 useCapture 作用一样
- once，布尔值，值为 true 表示该回调只会调用一次，调用后会移除监听
- passive，布尔值，表示永远不会调用 preventDefault

stopPropagation 是用来阻止事件冒泡的，其实该函数也可以阻止捕获事件。

stopImmediatePropagation 同样也能实现阻止事件，但是还能阻止该事件目标执行别的注册事件。

## 事件循环

Event Loop

（1）所有同步任务都在主线程上执行，形成一个[执行栈](http://www.ruanyifeng.com/blog/2013/11/stack.html)（execution context stack）。

（2）主线程之外，还存在一个"任务队列"（task queue）。只要异步任务有了运行结果，就在"任务队列"之中放置一个事件。

（3）一旦"执行栈"中的所有同步任务执行完毕，系统就会读取"任务队列"，看看里面有哪些事件。那些对应的异步任务，于是结束等待状态，进入执行栈，开始执行。

（4）主线程不断重复上面的第三步。



JS 是门非阻塞单线程语言

JS 在执行的过程中会产生执行环境，这些执行环境会被顺序的加入到执行栈中。如果遇到异步的代码，会被挂起并加入到 Task（有多种 task） 队列中。一旦执行栈为空，Event Loop 就会从 Task 队列中拿出需要执行的代码并放入执行栈中执行。

任务源可以分为 微任务（microtask） 和 宏任务（macrotask）

微任务包括 process.nextTick ，promise ，Object.observe ，MutationObserver

宏任务包括 script ， setTimeout ，setInterval ，setImmediate ，I/O ，UI rendering

1. 执行同步代码，这属于宏任务
2. 执行栈为空，查询是否有微任务需要执行
3. 执行所有微任务
4. 必要的话渲染 UI
5. 然后开始下一轮 Event loop，执行宏任务中的异步代码

MutationObserver是HTML5新增的属性，用于监听DOM修改事件，能够监听到节点的属性、文本内容、子节点等的改动，是一个功能强大的利器



## Node 中的 Event loop

- timers：执行setTimeout() 和 setInterval()中到期的callback。
- I/O callbacks：上一轮循环中有少数的I/Ocallback会被延迟到这一轮的这一阶段执行
- idle, prepare：仅内部使用
- poll：最为重要的阶段，执行I/O callback，在适当的条件下会阻塞在这个阶段
- check：执行setImmediate的callback
- close callbacks：执行close事件的callback，例如socket.on("close",func)

timer

I/O

I/O 阶段会执行除了 close 事件，定时器和 setImmediate 的回调

idle, prepare

idle, prepare 阶段内部实现

poll



poll 阶段很重要，这一阶段中，系统会做两件事情

1. 执行到点的定时器
2. 执行 poll 队列中的事件

并且当 poll 中没有定时器的情况下，会发现以下两件事情

- 如果 poll 队列不为空，会遍历回调队列并同步执行，直到队列为空或者系统限制
- 如果 poll 队列为空，会有两件事发生

- - 如果有 setImmediate 需要执行，poll 阶段会停止并且进入到 check 阶段执行 setImmediate
  - 如果没有 setImmediate 需要执行，会等待回调被加入到队列中并立即执行回调

如果有别的定时器需要被执行，会回到 timer 阶段执行回调。

check

check 阶段执行 setImmediate

close callbacks

close callbacks 阶段执行 close 事件



