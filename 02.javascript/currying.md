# 柯里化

概念：柯里化又叫部分求值，是把接受多个参数的函数变换成接受一个单一参数（最初函数的第一个参数）的函数，并且返回接受余下的参数而且返回结果的新函数的技术。

作用：延迟计算、参数复用、动态生成函数



## 延迟计算

```js
// curring 函数
function currying(func) {
    const args = [];
    return function result(...rest) {
        if (rest.length === 0)
            return func(...args);
        args.push(...rest);
        return result;
    }
}

const add = (...args) => args.reduce((a, b) => a + b);

const sum = currying(add);

sum(1,2)(3);
sum(4);
sum(); // 10
```

sum(1)(2)(3) = 6

考点一个是`递归`，一个是`求值`，也就是`valueOf`和`toString`

```js
// method 1
function add(num) {
    var sum = 0;
    sum += num;
    var tempFun = function(nnum) {
        if (arguments.length === 0) {
            return sum;
        } else {
            sum += nnum;
            return tempFun;
        }
    }
    tempFun.valueOf = function() {
        return sum;
    }
    tempFun.toString = function() {
        return sum + '';
    }
    return tempFun;
}

// method 2
function add(num) {
    var args = [];

    function addNum() {
        if (arguments.length === 0) {
            return calulate;
        } else {
            Array.prototype.push.apply(args, Array.prototype.splice.call(arguments, 0));
            return addNum;
        }
    }

    function calulate() {
        var result = args.reduce((previousValue, currentValue) => {
            return previousValue + currentValue;
        }, 0);
        args = [];
        return result;
    }

    addNum.valueOf = function() {
        return calulate();
    }
    addNum.toString = function() {
        return calulate() + '';
    }
    return addNum;
}


// add(args) = sum(1, 2, 3, 4)
function add(args) {
    return sum.apply(this, args);
}
```

## 参数复用

```
const addEvent = (function () {
    if (window.addEventListener) {
        return (elem, type, fn, capture) => {
            elem.addEventListener(type, (e) => fn.call(elem, e), capture);
        };
    } else {
        return (elem, type, fn, capture) => {
            elem.attachEvent('on' + type, (e) => fn.call(elem, e);
        };
    }
})();
```

