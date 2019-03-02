# 常用函数

index





## bind

要点：

实现：

```javascript
if (!Function.prototype.bind) {
  Function.prototype.bind = function (ctx) {
      if (typeof this !== 'function') {
      return
    }
  
    let self = this
    let args = Array.prototype.slice.call(arguments, 1)
    return function () {
      return self.apply(ctx, args.concat(Array.prototype.slice.call(arguments))) //这里的arguments是执行绑定函数时的实参
    }
  }
}
```

```
// es6
Function.prototype.bind = function (context, ...args) {
    return (...rest) => this.call(context, ...args, ...rest);
};

// es5
Function.prototype.bind = function(oThis) {
    var args = Array.prototype.slice.call(arguments, 1);
    var fToBind = this;
    var F = function() {};
    var fBound = function() {
        return ftoBind.apply(this instanceof F ? this : oThis || this,
            args.concat(Array.prototype.slice.call(arguments)));
    }
    F.prototype = fToBind.prototype;
    fBound.prototype = new F();
    return fBound;
}

```



## throttle

应用：间隔一定时间触发回调来控制函数调用频率

- DOM 元素的拖拽功能实现（mousemove）
- 射击游戏的 mousedown/keydown 事件（单位时间只能发射一颗子弹）
- 计算鼠标移动的距离（mousemove）
- Canvas 模拟画板功能（mousemove）
- 搜索联想（keyup）
- 监听滚动事件判断是否到页面底部自动加载更多：给 scroll 加了 debounce 后，只有用户停止滚动后，才会判断是否到了页面底部；如果是 throttle 的话，只要页面滚动就会间隔一段时间判断一次

要点：

实现：

```javascript
function throttle(fn = () => {}, delay = 0) {
    let _timer = null
    let last = +new Date()
    return function() {
        let _this = this,
            args = arguments,
            cur = +new Date()
        clearTimeout(_timer)
        if (cur - last > delay) {
            fn.apply(_this, arguments)
            last = cur
        } else {
            _timer = setTimeout(function() {
                fn.apply(_this, arguments)
            }, delay)
        }
    }
}
```

## debounce

应用场景：

连续的事件响应我们只需要执行一次回调

- 每次 resize/scroll 触发统计事件
- 文本输入的验证（连续输入文字后发送 AJAX 请求进行验证，验证一次就好）

## promise



## deepclone



var Type = (function() {

​                var type = {};

​                var typeArr = ['String', 'Object', 'Number', 'Array','Undefined', 'Function', 'Null', 'Symbol'];

​                for (var i = 0; i < typeArr.length; i++) {

​                    (function(name) {

​                        type['Is' + name] = function(obj) {

​                            return Object.prototype.toString.call(obj) == '[object ' + name + ']';

​                        }

​                    })(typeArr[i]);

​                }

​                return type;

})();

var a = null;  (!a && typeof a === 'object');//true

```
     if (!Number.isNaN) {
         Number.isNaN = function isNaN(x) {
             return x !== x;
         };
     }
```


 NaN 是整个语言中唯一和 

自身不相等的值。因此，NaN 是使得 x != x 为真的唯一值。 





---

