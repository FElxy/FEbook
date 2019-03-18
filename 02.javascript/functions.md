# 常用函数

index



## **once**

```
once
function once(fn, ctx) {
let flag = true
return function() {
if(flag){
flag = false
fn.apply(ctx || this, arguments)
return 
}
return undefined
}
}
```



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



## 数组去重

```
unique

  var arr = ['f', '0', '3', 'f', 'd', '2', 'd', 'd']
  function unique(arr) {
      var newarr = [] //构建newarr 数组
      var obj = {}  //构建一个对象obj
      for (var i= 0; i< arr.length; i++) {
          if (!obj[arr[i]]) { //如果元素跟数组对比
              newarr.push(arr[i])
              obj[arr[i]] = 1 // 元素内容作为对象属性
          }
      }
      return newarr 
  }
  console.log(unique(arr)) //["f", "0", "3", "d", "2"]

  var arr = ['f', '0', '3', 'f', 'd', '2', 'd', 'd']
  var newarr = [] //定义一个新的数组
  for (var i= 0; i< arr.length; i++) {   //遍历arr 每一项
      if(newarr.indexOf(arr[i]) == -1) {  //判断newarr数组是否存在arr项 
          newar.push(arr[i])  //newarr里面不存在就push
      }
  }
  console.log(newarr)  //["f", "0", "3", "d", "2"]

Array.from(new Set([2,3,4,2,3,4]))
Array.from(new Set([1,1,2,'2',3])) 
 [...new Set([1,1,2,'2',3])]
```



## flat

```
flatten
const flattenDeep = (arr) => Array.isArray(arr)
  ? arr.reduce( (a, b) => [...a, ...flattenDeep(b)] , [])
  : [arr]

flattenDeep([1, [[2], [3, [4]], 5]])
```



## jsonp

```js
// jsonp
function jsonp(url, jsonpCallback, success) {
  let script = document.createElement("script");
  script.src = url;
  script.async = true;
  script.type = "text/javascript";
  window[jsonpCallback] = function(data) {
    success & success(data);
  };
  document.body.appendChild(script);
}
jsonp(
  "http://xxx",
  "callback",
  function(value) {
    console.log(value);
  }
);
```



## ajax

let xhr = new XMLHttpRequest()

xhr.onreadystatechange = function () {

if (xhr.readyState === 4 && xhr.status === 200) {



}

}

xhr.open('get', 'url', true)

xhr.send()

## delegate

```

		function delegate(parents, selector, eventName, fn) {
			function bindEvent(event) {
				const e = event || window.event
				const currentTarget = e.currentTarget
				let target = e.target || e.srcElement
				while(target !== currentTarget) {
					let t = target
					if (target.matches(selector)) {
						fn.apply(t, arguments)
					}
					target = target.parentNode
				}
			}
			Array.from(document.querySelectorAll(parents)).forEach((item) => {
				item.addEventListener(eventName, bindEvent, false)
			})
		}

if (!Element.prototype.matches) {
    Element.prototype.matches = 
        Element.prototype.matchesSelector || 
        Element.prototype.mozMatchesSelector ||
        Element.prototype.msMatchesSelector || 
        Element.prototype.oMatchesSelector || 
        Element.prototype.webkitMatchesSelector ||
        function(s) {
            var matches = (this.document || this.ownerDocument).querySelectorAll(s),
                i = matches.length;
            while (--i >= 0 && matches.item(i) !== this) {}
            return i > -1;            
        };
}
```

## parseParam

```

function parseParam(str) {
if (typeof str !== 'string') {return}
let paramStr = str.split('?')[1]
if(!params) {return}
let obj = {}
let arr = decodeURIComponent(paramStr).split('&')
arr.forEach(item => {
let t = item.split('=')
let key = t[0]
let val = t[1] || true
if (obj[key] === 'undefined) {
obj[key] = val
} else {
let newVal = Array.isArray(obj[key]) ? obj[key] : [obj[key]]
newVal.push(val)
obj[key] = newVal
}
})
return obj
}
```

## ajax

```js
手写ajax
onreadystatechange
readyState
0: 请求未初始化
1: 服务器连接已建立
2: 请求已接收
3: 请求处理中
4: 请求已完成，且响应已就绪
status
200
404

xhr.open(Method,  URL, async, user, pwd)
method:  "GET", "POST", "PUT", "DELETE"


const xhr = new XMLHttpRequest()

xhr.onreadystartechange = function() {
if (xhr.readyState === 4) {
if (status === 200) {
console.log(xhr.response)
} else {
console.log(xhr.status)
}
}
}
xhr.open('GET', `url`)
xhr.send(null)

```





---

## 判断数组的方法

### Object.prototype.toString.call()



**在JS中，只有 0，-0，NaN，""，null，undefined 这六个值转布尔值时，结果为 false**