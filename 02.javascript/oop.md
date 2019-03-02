# JavaScript面向对象

JavaScript是不是一门严格意义上的面向对象的语言，它并没有提供类的方法。它是使用**原型继承而不是类继承达到面向对象的效果。**我们先理解一下面向对象的基本概念，后续章节详细的解释下面向对象在JavaScript中的实现。

## 什么是面向对象

面向对象语言的三大特性:

- 封装(Encapsulation): 把相关的信息（无论数据或方法）存储在对象中的能力
- 继承(Inheritance): 由另一个类（或多个类）得来类的属性和方法的能力
- 多态(Polymorphism): 编写能以多种方法运行的函数或方法的能力

其他的一些内容:

- 类(Class)： 定义了一件事物的抽象特点, 用来构造对象
- 对象(Object): 类的实例化
- 属性(Property): 对象具有的数据
- 方法(Method): 也成消息，用于对象之间传递数据

## 原型继承与类继承的区别

| 基于类的继承                                              | 原型继承                                                     |
| --------------------------------------------------------- | ------------------------------------------------------------ |
| 类是不可变的。在运行时，你无法修改或者添加新的方法        | 原型是灵活的。它们可以是不可变的也可以是可变的               |
| 类可能会不支持多重继承                                    | 对象可以继承多个原型对象                                     |
| 基于类的继承比较复杂。你需要使用抽象类，接口和final类等等 | 原型继承比较简洁。你只有对象，你只需要对对象进行扩展就可以了 |