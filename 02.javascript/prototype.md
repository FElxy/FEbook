# 构造函数、原型和原型链

## 构造函数

`constructor` 返回创建实例对象时构造函数的引用。

```js
function Parent(age) {
    this.age = age;
}

var p = new Parent(50);
p.constructor === Parent; // true
p.constructor === Object; // false
```

构造函数和普通函数的区别在于，使用 `new` 生成实例的函数就是构造函数，直接调用的就是普通函数。

普通函数创建的实例constructor === Object;

`Symbol` 是基本数据类型，不能使用new，生成实例直接使用 `Symbol()` 即可。

`Symbol(123)` 实例可以获取 `constructor` 属性值。

// ƒ Symbol() { [native code] }

`Symbol.prototype.constructor` 返回创建实例原型的函数， 默认为 `Symbol` 函数。



对于**引用类型**来说 `constructor` 属性值是可以修改的，但是对于**基本类型**来说是只读的。

`null` 和 `undefined` 是没有 `constructor` 属性的。

基本类型是只读的原生构造函数（`native constructors`）创建的

## 原型

prototype

每个对象拥有一个**原型对象**，对象以其原型为模板，从原型继承方法和属性，这些属性和方法定义在对象的构造器函数的 `prototype` 属性上，而非对象实例本身。

 `__proto__`

是一个**访问器属性**（即 getter 函数和 setter 函数），通过它可以访问到对象的内部 `[[Prototype]]` (一个对象或 `null` )。

`__proto__` 是每个实例上都有的属性，`prototype` 是构造函数的属性，这两个并不一样，但 `p.__proto__ ` 和 `Parent.prototype` 指向同一个对象。

## 原型链

每个对象拥有一个原型对象，通过 `__proto__` 指针指向上一个原型 ，并从中继承方法和属性，同时原型对象也可能拥有原型，这样一层一层，最终指向 `null`。这种关系被称为**原型链 (prototype chain)**，通过原型链一个对象会拥有定义在其他对象中的属性和方法。



## instanceof原理

能在实例的 **原型对象链** 中找到该构造函数的`prototype`属性所指向的 **原型对象**，就返回`true`。即:

```js
// __proto__: 代表原型对象链
instance.[__proto__...] === instance.constructor.prototype

// return true
```

