# 浅拷贝

拷贝第一层的**基本类型值**，以及第一层的**引用类型地址**。

* Object.assign()
* 展开语法 `Spread`

`Object.assign()` 方法用于将所有可枚举属性的值从一个或多个源对象复制到目标对象。它将返回目标对象。

```js
let b = {...a};
let c = Object.assign({}, a);
```

* Array.prototype.slice()

`slice()` 方法返回一个新的数组对象，这一对象是一个由 `begin`和 `end`（不包括`end`）决定的原数组的**浅拷贝**。原始数组不会被改变。

concat

## 浅拷贝简单实现

```
function cloneShallow(source) {
    var target = {};
    for (var key in source) {
        if (Object.prototype.hasOwnProperty.call(source, key)) {
            target[key] = source[key];
        }
    }
    return target;
}
```
值为null时的判断
## 简单深拷贝
类型判断
递归
循环引用
symbol

todos




## Object.assign

主要是将所有**可枚举属性**的值从一个或多个源对象复制到目标对象，同时返回目标对象。

Object.assign(target, ...sources)

`String` 类型和 `Symbol` 类型的属性都会被拷贝，而且不会跳过那些值为 `null` 或 `undefined` 的源对象。

### `Object.assign` 模拟实现

实现一个 `Object.assign` 大致思路如下：

1、判断原生 `Object` 是否支持该函数，如果不存在的话创建一个函数 `assign`，并使用 `Object.defineProperty` 将该函数绑定到 `Object` 上。

2、判断参数是否正确（目标对象不能为空，我们可以直接设置{}传递进去,但必须设置值）。

3、使用 `Object()` 转成对象，并保存为 to，最后返回这个对象 to。

4、使用 `for..in` 循环遍历出所有可枚举的自有属性。并复制给新的目标对象（使用 `hasOwnProperty` 获取自有属性，即非原型链上的属性）。

```js
if (typeof Object.assign2 != 'function') {
  // Attention 1
  Object.defineProperty(Object, "assign2", {
    value: function (target) {
      'use strict';
      if (target == null) { // Attention 2
      //if (target === undefined || target === null) {
        throw new TypeError('Cannot convert undefined or null to object');
      }

      // Attention 3
      var to = Object(target);
        
      for (var index = 1; index < arguments.length; index++) {
        var nextSource = arguments[index];

        if (nextSource != null) {  // Attention 2
          // Attention 4
          for (var nextKey in nextSource) {
            if (Object.prototype.hasOwnProperty.call(nextSource, nextKey)) {
              to[nextKey] = nextSource[nextKey];
            }
          }
        }
      }
      return to;
    },
    writable: true,
    configurable: true
  });
}
```

### 注意1：可枚举性
原生情况下挂载在 `Object` 上的属性是不可枚举的，但是直接在 `Object` 上挂载属性 `a` 之后是可枚举的，所以这里必须使用 `Object.defineProperty`，并设置 `enumerable: false` 以及 `writable: true, configurable: true`。

使用 2 种方法查看 `Object.assign` 是否可枚举，使用 `Object.getOwnPropertyDescriptor` 或者 `Object.propertyIsEnumerable`。其中`propertyIsEnumerable(..)` 会检查给定的属性名是否直接存在于对象中（而不是在原型链上）并且满足 `enumerable: true`。



### 注意4：存在性

`in` 操作符和 `hasOwnProperty` 方法，区别如下（你不知道的JS上卷 P119）：

1、`in` 操作符会检查属性是否在对象及其 `[[Prototype]]` 原型链中。

2、`hasOwnProperty(..)` 只会检查属性是否在 `myObject` 对象中，不会检查 `[[Prototype]]` 原型链。



`Object.assign` 方法肯定不会拷贝原型链上的属性，所以模拟实现时需要用 `hasOwnProperty(..)` 判断处理下，但是直接使用 `myObject.hasOwnProperty(..)` 是有问题的，因为有的对象可能没有连接到 `Object.prototype` 上（比如通过 `Object.create(null)` 来创建），这种情况下，使用 `myObject.hasOwnProperty(..)` 就会失败。

# 深拷贝

深拷贝会拷贝所有的属性，并拷贝属性指向的动态分配的内存。当对象和它所引用的对象一起拷贝时即发生深拷贝。深拷贝相比于浅拷贝速度较慢并且花销较大。拷贝前后两个对象互不影响。

* `JSON.parse(JSON.stringify(object))`

  * 会忽略 `undefined`
  * 会忽略 `symbol
  * 不能序列化函数
  * 不能解决循环引用的对象
  * 不能正确处理`new Date()`
  * 不能处理正则

```js
function isObject(obj) {
	return typeof obj === 'object' && obj != null;
}
```

循环检测，设置一个数组或者哈希表存储已拷贝过的对象，当检测到当前对象已存在于哈希表中时，取出该值并返回即可。

```js
function cloneDeep3(source, hash = new WeakMap()) {

    if (!isObject(source)) return source; 
    if (hash.has(source)) return hash.get(source); // 新增代码，查哈希表
      
    var target = Array.isArray(source) ? [] : {};
    hash.set(source, target); // 新增代码，哈希表设值
    
    for(var key in source) {
        if (Object.prototype.hasOwnProperty.call(source, key)) {
            if (isObject(source[key])) {
                target[key] = cloneDeep3(source[key], hash); // 新增代码，传入哈希表
            } else {
                target[key] = source[key];
            }
        }
    }
    return target;
}
```

ES5下

```js
function cloneDeep3(source, uniqueList) {

    if (!isObject(source)) return source; 
    if (!uniqueList) uniqueList = []; // 新增代码，初始化数组
      
    var target = Array.isArray(source) ? [] : {};
    
    // ============= 新增代码
    // 数据已经存在，返回保存的数据
    var uniqueData = find(uniqueList, source);
    if (uniqueData) {
        return uniqueData.target;
    };
        
    // 数据不存在，保存源数据，以及对应的引用
    uniqueList.push({
        source: source,
        target: target
    });
    // =============

    for(var key in source) {
        if (Object.prototype.hasOwnProperty.call(source, key)) {
            if (isObject(source[key])) {
                target[key] = cloneDeep3(source[key], uniqueList); // 新增代码，传入数组
            } else {
                target[key] = source[key];
            }
        }
    }
    return target;
}

// 新增方法，用于查找
function find(arr, item) {
    for(var i = 0; i < arr.length; i++) {
        if (arr[i].source === item) {
            return arr[i];
        }
    }
    return null;
}
```

拷贝symbol

`Symbol` 在 `ES6` 下才有，我们需要一些方法来检测出 `Symble` 类型。

方法一：`Object.getOwnPropertySymbols(...)`

方法二：`Reflect.ownKeys(...)`

**对于方法一**可以查找一个给定对象的符号属性时返回一个 `?symbol` 类型的数组。注意，每个初始化的对象都是没有自己的 `symbol` 属性的，因此这个数组可能为空，除非你已经在对象上设置了 `symbol` 属性。

```js
var obj = {};
var a = Symbol("a"); // 创建新的symbol类型
var b = Symbol.for("b"); // 从全局的symbol注册?表设置和取得symbol

obj[a] = "localSymbol";
obj[b] = "globalSymbol";

var objectSymbols = Object.getOwnPropertySymbols(obj);

console.log(objectSymbols.length); // 2
console.log(objectSymbols)         // [Symbol(a), Symbol(b)]
console.log(objectSymbols[0])      // Symbol(a)
```



**对于方法二**返回一个由目标对象**自身**的属性键组成的数组。它的返回值等同于`Object.getOwnPropertyNames(target).concat(Object.getOwnPropertySymbols(target))`。

```js
Reflect.ownKeys({z: 3, y: 2, x: 1}); // [ "z", "y", "x" ]
Reflect.ownKeys([]); // ["length"]

var sym = Symbol.for("comet");
var sym2 = Symbol.for("meteor");
var obj = {[sym]: 0, "str": 0, "773": 0, "0": 0,
           [sym2]: 0, "-1": 0, "8": 0, "second str": 0};
Reflect.ownKeys(obj);
// [ "0", "8", "773", "str", "-1", "second str", Symbol(comet), Symbol(meteor) ]
// 注意顺序
// Indexes in numeric order, 
// strings in insertion order, 
// symbols in insertion order
```

思路就是先查找有没有 `Symbol` 属性，如果查找到则先遍历处理 `Symbol` 情况，然后再处理正常情况

```js
    // ============= 新增代码
    let symKeys = Object.getOwnPropertySymbols(source); // 查找
    if (symKeys.length) { // 查找成功	
        symKeys.forEach(symKey => {
            if (isObject(source[symKey])) {
                target[symKey] = cloneDeep4(source[symKey], hash); 
            } else {
                target[symKey] = source[symKey];
            }    
        });
    }
    // =============
```

```js
function cloneDeep4(source, hash = new WeakMap()) {

    if (!isObject(source)) return source; 
    if (hash.has(source)) return hash.get(source); 
      
    let target = Array.isArray(source) ? [] : {};
    hash.set(source, target);
    
  	Reflect.ownKeys(source).forEach(key => { // 改动
        if (isObject(source[key])) {
            target[key] = cloneDeep4(source[key], hash); 
        } else {
            target[key] = source[key];
        }  
  	});
    return target;
}
```

使用了 `Reflect.ownKeys()` 获取所有的键值，同时包括 `Symbol`，对 source 遍历赋值即可。

```js
function cloneDeep4(source, hash = new WeakMap()) {

    if (!isObject(source)) return source; 
    if (hash.has(source)) return hash.get(source); 
      
    let target = Array.isArray(source) ? [...source] : { ...source }; // 改动 1
    hash.set(source, target);
    
  	Reflect.ownKeys(target).forEach(key => { // 改动 2
        if (isObject(source[key])) {
            target[key] = cloneDeep4(source[key], hash); 
        } else {
            target[key] = source[key];
        }  
  	});
    return target;
}
```

在改动 1 中，返回一个新数组或者新对象，获取到源对象之后就可以如改动 2 所示传入 target 遍历赋值即可。

`Reflect.ownKeys()` 这种方式的问题在于不能深拷贝原型链上的数据，因为返回的是目标对象**自身**的属性键组成的数组。如果想深拷贝原型链上的数据怎么办，那用 `for..in` 就可以了。

### 解决递归爆栈

```js
function cloneDeep5(x) {
    const root = {};

    // 栈
    const loopList = [
        {
            parent: root,
            key: undefined,
            data: x,
        }
    ];

    while(loopList.length) {
        // 深度优先
        const node = loopList.pop();
        const parent = node.parent;
        const key = node.key;
        const data = node.data;

        // 初始化赋值目标，key为undefined则拷贝到父元素，否则拷贝到子元素
        let res = parent;
        if (typeof key !== 'undefined') {
            res = parent[key] = {};
        }

        for(let k in data) {
            if (data.hasOwnProperty(k)) {
                if (typeof data[k] === 'object') {
                    // 下一次循环
                    loopList.push({
                        parent: res,
                        key: k,
                        data: data[k],
                    });
                } else {
                    res[k] = data[k];
                }
            }
        }
    }

    return root;
}
```

[深拷贝的终极探索（99%的人都不知道）](深拷贝的终极探索（99%的人都不知道）)

