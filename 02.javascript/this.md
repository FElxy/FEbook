# this
## 默认绑定 全局this
**非严格模式**
在浏览器中this指向window对象。
创建变量没有使用var或者let声明的，是在操作全局this。

在node中，this是全局命名空间。通过global来访问。
**严格模式**
 不能将全局对象用于默认绑定，this会绑定到`undefined`
 'a' of undefined
## 函数和方法里的this
函数正常被调用（不使用new）时，里面的this指向的是全局作用域。
但是，使用了“use strict”，此时的this是undefined。

```
<script type="text/javascript">
    foo = "bar";

    function testThis() {
      "use strict";
      this.foo = "foo";
    }

    console.log(this.foo); //logs "bar"
    testThis();  //Uncaught TypeError: Cannot set property 'foo' of undefined 
</script>
```
当使用了new关键字时，产生了一个新的实例，此时的this指向了一个新的上下文，不再指向全局的this。

## 原型中的this new
函数创建以后以一个函数对象的形式存在着，函数对象自动获得prototype的属性。
通过new创建了多个实例共享一个原型。
使用`new`来调用函数，或者说发生构造函数调用时，会自动执行下面的操作。

* 1、创建（或者说构造）一个新对象。
* 2、这个新对象会被执行`[[Prototype]]`连接。
* 3、这个新对象会绑定到函数调用的`this`。
* 4、如果函数没有返回其他对象，那么`new`表达式中的函数调用会自动返回这个新对象。

**手写一个new实现**

```js
function create() {
	// 创建一个空的对象
    var obj = new Object(),
	// 获得构造函数，arguments中去除第一个参数
    Con = [].shift.call(arguments);
	// 链接到原型，obj 可以访问到构造函数原型中的属性
    obj.__proto__ = Con.prototype;
	// 绑定 this 实现继承，obj 可以访问到构造函数中的属性
    var ret = Con.apply(obj, arguments);
	// 优先返回构造函数返回的对象
	return ret instanceof Object ? ret : obj;
};
```
**手写一个new实现**

```js
function create() {
	// 创建一个空的对象
    var obj = new Object(),
	// 获得构造函数，arguments中去除第一个参数
    Con = [].shift.call(arguments);
	// 链接到原型，obj 可以访问到构造函数原型中的属性
    obj.__proto__ = Con.prototype;
	// 绑定 this 实现继承，obj 可以访问到构造函数中的属性
    var ret = Con.apply(obj, arguments);
	// 优先返回构造函数返回的对象
	return ret instanceof Object ? ret : obj;
};
```
**代码原理解析**：

* 1、用`new Object() `的方式新建了一个对象`obj`
* 2、取出第一个参数，就是我们要传入的构造函数。此外因为 shift 会修改原数组，所以 `arguments `会被去除第一个参数
* 3、将 `obj `的原型指向构造函数，这样`obj`就可以访问到构造函数原型中的属性
* 4、使用` apply`，改变构造函数` this` 的指向到新建的对象，这样 `obj `就可以访问到构造函数中的属性
* 5、返回 `obj`

JS中创建一个空对象最简单的方法是**`Object.create(null)`**，这个和`{}`很像，但是并不会创建`Object.prototype`这个委托，所以比`{}`更空。


## 使用bind，call，apply改变this

如果使用Object create可以不用调用构造函数，
Object.create(Thing.prototype);

## 对象中的this
 可以在对象中的任何方法中使用this来访问该对象的属性
 
## DOM事件回调中的this
在DOM事件的处理函数中，this.指向的是被绑定该事件的DOM元素。
可以使用bind人为改变执行上下文
## HTML中的this
HTML标签集中写JS，此时this只想的是该HTML元素。

## eval中的this
eval安全隐患
```
function Thing () {
}
Thing.prototype.foo = "bar";
Thing.prototype.logFoo = function () {
    eval("console.log(this.foo)"); //logs "bar"
}

var thing = new Thing();
thing.logFoo();
```
## 使用with时的this
使用with可以将this人为添加到当前执行环境中而不需要显示地引用this。
this会产生歧义
## 自带的方法可以传递this
forEach
JS许多内置函数提供了一个可选参数，被称之为“上下文”（context），其作用和`bind(..)`一样，确保回调函数使用指定的this。
## 箭头函数this
箭头函数，箭头函数无法使用上述四条规则，而是根据外层（函数或者全局）作用域（**词法作用域**）来决定this。



> https://leohxj.gitbooks.io/front-end-database/content/javascript-basic/all-this.html

