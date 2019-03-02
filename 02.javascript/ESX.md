# ESX

## grammar

### 块作用域声明

之前只能使用function建立块级作用域，现在可以用{...}建立块级作用域

过早引用let声明的变量会报引用错误，是因为产生了临时性死区

const用于创建常量，

常量不是对这个值本身的限制，而是对赋值的那个变量的限制。换句话说，这个值并没有
因为 const 被锁定或者不可变，只是赋值本身不可变。如果这个值是复杂值，比如对象或
者数组，其内容仍然是可以修改的。

将一个对象或数组作为常量赋值，意味着这个值在这个常量的词法作用域结
束之前不会被垃圾回收，因为指向这个值的引用没有清除。

### 块作用域函数

```js
if (something) {
         function foo() {
             console.log( "1" );
         }
}
else {
         function foo() {
             console.log( "2" );
} }
foo(); // ??
```

在前 ES6 环境中，不管 something 的值是什么，foo() 都会打印出 "2"，因为两个函数声明都被提升到了块外，第二个总是会胜出。

而在 ES6 中，最后一行会抛出一个 ReferenceError。

### spread/rest

**展开一个数组**

```
var a = [2,3,4];
var b = [ 1, ...a, 5 ];
console.log( b ); // [1,2,3,4,5]
```

就像是 [1].concat( a, [5] )

 **收集一个数组**

```
function foo(x, y, ...z) {
	console.log( x, y, z );
}
foo( 1, 2, 3, 4, 5 );           // 1 2 [3,4,5]
```

把剩下的参数(如果有的话)收集到一起组成一个名为 z 的数组。

```js
// 按照新的ES6的行为方式实现 
function foo(...args) { 
// args已经是一个真正的数组 
// 丢弃args中第一个元素 
args.shift();
	// 把整个args作为参数传给console.log(..) 
	console.log( ...args );
}
```

老式实现方式

```js
function bar() { 
// 把arguments转换为一个真正的数组
 var args = Array.prototype.slice.call( arguments ); 
}
```

默认值表达式中的标识符引用首先匹配到形式参数作用域，然后才会搜索外层作用域

**数组解构和对象解构**

```js
var [ a, b, c ] = foo();
var { x: x, y: y, z: z } = bar();
// 如果属性名和要赋值的变量名相同
var { x, y, z } = bar(); 
var { x: bam, y: baz, z: bap } = bar();
语法模式是souce: target
console.log( bam, baz, bap );       // 4 5 6
```

```
var aa = 10, bb = 20;
var o = { x: aa, y: bb };
var { x: AA, y: BB } = o;
console.log( AA, BB ); // 10 20
```



###  对象字面量

简洁方法

```
runSomething( {
	something: function something(x,y) {
	// .. } 
}); 
```

这里的第二个 something 提供了一个超级方便的词法标识符，总是指向这个函数本身，为我们提供了一个完美的用于递归、事件绑定 / 解绑定等的引用——不会和 this 纠缠也不需要并不可靠的对象引用。

```
runSomething( {
	something(x,y) {
	// .. } 
}); 
```

等于匿名形式

```
runSomething( {
	something: function (x,y) {
	// .. } 
}); 
```

### 计算属性名

对象字面定义属性名位置的 [ .. ] 中可以放置任意合法表达式。

```
var prefix = "user_";
     var o = {
         baz: function(..){ .. },
         [ prefix + "foo" ]: function(..){ .. },
         [ prefix + "bar" ]: function(..){ .. }
         ..
};

var o = {
["f" + "oo"]() { .. } // 计算出的简洁方法 *["b" + "ar"]() { .. } // 计算出的简洁生成器
};
```

### 模板字面量

```
var greeting = `Hello ${name}!`;
```

```
插入字符串字面量的一个优点是它们可以分散在多行:
     var text =
     `Now is the time for all good men
     to come to the aid of their
     country!`;
     console.log( text );
     // Now is the time for all good men
     // to come to the aid of their
     // country!
插入字符串字面量中的换行(新行)会在字符串值中被保留
```

插入字符串字面量在它出现的词法作用域内，没有任何形式的动态作用域。

```
function foo(strings, ...values) {
         console.log( strings );
         console.log( values );
         } 
         var desc = "awesome";
      foo`Everything is ${desc}!`;
      // [ "Everything is ", "!"]
      // [ "awesome" ]

```

### 箭头函数

箭头函数总是函数表达式;并不存在箭头函数声明。我们还应清楚箭头函数是匿名函数表
达式——它们没有用于递归或者事件绑定 / 解绑定的命名引用

=> 箭头函数的主要设计目的就是以特定的方式改变 this 的行为特性

在箭头函数内部，this 绑定不是动态的，而是词法的。

箭头函数还有词法 arguments——它们没有自己的 arguments 数组，而是继承自父层

对于箭头函数的this总结如下：

1. 箭头函数不绑定this，箭头函数中的this相当于普通变量。
2. 箭头函数的this寻值行为与普通变量相同，在作用域中逐级寻找。
3. 箭头函数的this无法通过bind，call，apply来**直接**修改（可以间接修改）。
4. 改变作用域中this的指向可以改变箭头函数的this。
5. eg. `function closure(){()=>{//code }}`，在此例中，我们通过改变封包环境`closure.bind(another)()`，来改变箭头函数this的指向。



### for..of 循环

for..of 循环的值必须是一个 iterable

for..in 在数组 a 的键 / 索引上循环，而 for..of 在 a 的值上循环。

```
			var a = ["a","b","c","d","e"];
      for (var idx in a) {
          console.log( idx );
}
// 0 1 2 3 4
      for (var val of a) {
          console.log( val );
      }
      // "a" "b" "c" "d" "e"

```

JavaScript 中默认为(或提供)iterable 的标准内建值包括: 

- Arrays 
-  Strings 

- Generators(参见第 3 章) 
- Collections / TypedArrays ( 参见第 5 章) 

默认情况下平凡对象并不适用 for..of 循环。因为它们并没有默认的迭代器 

for..of 循环也可以通过 break、continue、return(如果在函数中的 话)提前终止，并抛出异常。 

### 符号

- 不能也不应该对 Symbol(..) 使用 new。它并不是一个构造器，也不会创建一个对象。 
- 传给 Symbol(..) 的参数是可选的。如果传入了的话，应该是一个为这个 symbol 的用途给出用户友好描述的字符串。
- typeof 的输出是一个新的值 ("symbol")，这是识别 symbol 的首选方法。 



















