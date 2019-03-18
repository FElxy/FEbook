# CSS常用知识



## 移动端样式问题

```css
/* 禁止调出系统选项菜单 */ 
-webkit-touch-callout: none; 
```

```css
/* 禁止用户选择文本 */
user-select: none;
```

```css
/* 去掉点击的元素出现蒙层 */
div {
    -webkit-tap-highlight-color: rgba(0,0,0,0);
}
```

```css
/* 去掉输入框内阴影 */
-webkit-appearance: none;
```

```css
/* 去掉默认滚动条 */
::-webkit-scrollbar
```

## 有用的属性

### pointer-events

其实常用的是`pointer-events: none`，此时元素永远不会成为鼠标事件的target。另外还有值`auto`以及其他使用与svg的值。

之前做过一个canvas全屏动画，为了不阻挡其他元素绑定事件的触发，给canvas设置`pointer-events: none`来实现全屏的漂浮动画。

### object-fit

> object-fit 属性指定可替换元素的内容应该如何适应到其使用的高度和宽度确定的框。
>
> 可选值 fill | contain | cover | none | scale-down

大体可以通过对background-size的值来理解，比较特殊的是scale-down

scale-down：内容的尺寸与 `none` 或 `contain` 中的一个相同，取决于它们两个之间谁得到的对象尺寸会更小一些。

## 垂直居中方案

1. flex
2. display: table-cell / table  vertical-align: middle
3. position: absolute; transform: translate() / 负margin
4. line-height 单行 已知高度
5. position: absolute; left top bottom right: 0 + margin：auto
6. JS动态计算 / css 动态计算 calc
7. 多级嵌套，改变父元素位置
8. display: inline-block; vertical-align 伪元素的height 100%, vertical-aligin: middle
9. 图片base64就好 + vertical-align

## BFC

**块级格式化上下文**，是一个独立的渲染区域，让处于 BFC 内部的元素与外部的元素相互隔离，使内外元素的定位不会相互影响。

触发条件：

1. 根元素
2. 浮动元素：float不为none
3. 绝对定位元素：position为fixed、absolute
4. overflow不为visible
5. 行内块：display：inline-block
6. 表格相关：display: table, table-cell...
7. display: flex, inline-flex
8. display: grid, inline-grid

## 盒模型

box-sizing: border-box / content-box;

## 浮动原理



## 层叠上下文

文档中的层叠上下文由满足以下任意一个条件的元素形成：

- 根元素 (HTML),
- z-index 值不为 "auto"的 绝对/相对定位，
- 一个 z-index 值不为 "auto"的 flex 项目 (flex item)，即：父元素 display: flex|inline-flex，
- [opacity](https://developer.mozilla.org/zh-CN/docs/Web/CSS/opacity) 属性值小于 1 的元素（参考 [the specification for opacity](http://www.w3.org/TR/css3-color/#transparency)），
- [transform](https://developer.mozilla.org/zh-CN/docs/Web/CSS/transform) 属性值不为 "none"的元素，
- [mix-blend-mode](https://developer.mozilla.org/zh-CN/docs/Web/CSS/mix-blend-mode) 属性值不为 "normal"的元素，
- [filter](https://developer.mozilla.org/zh-CN/docs/Web/CSS/filter)值不为“none”的元素，
- [perspective](https://developer.mozilla.org/zh-CN/docs/Web/CSS/perspective)值不为“none”的元素，
- [isolation](https://developer.mozilla.org/zh-CN/docs/Web/CSS/isolation) 属性被设置为 "isolate"的元素，
- position: fixed
- 在 [will-change](https://developer.mozilla.org/zh-CN/docs/Web/CSS/will-change) 中指定了任意 CSS 属性，即便你没有直接指定这些属性的值（参考 [这篇文章](http://dev.opera.com/articles/css-will-change-property/)）
- [-webkit-overflow-scrolling](https://developer.mozilla.org/zh-CN/docs/Web/CSS/-webkit-overflow-scrolling) 属性被设置 "touch"的元素

在层叠上下文中，其子元素同样也按照上面解释的规则进行层叠。 特别值得一提的是，其子元素的 z-index 值只在父级层叠上下文中有意义。子级层叠上下文被自动视为父级层叠上下文的一个独立单元。

总结:

- 给一个 HTML 元素定位和 z-index 赋值创建一个层叠上下文，（opacity 值不为 1 的也是相同）。
- 层叠上下文可以包含在其他层叠上下文中，并且一起创建一个有层级的层叠上下文。
- 每个层叠上下文完全独立于它的兄弟元素：当处理层叠时只考虑子元素。
- 每个层叠上下文是自包含的：当元素的内容发生层叠后，整个该元素将会 在父层叠上下文中 按顺序进行层叠。

## z-index

position，display

css自适应图片

link 和 @import的区别

link属于标签，还可以定义rel，preload prefetch之类的事

@import 加载css

页面加载时，link引用的css会同时加载，import的会等到页面加载完成后再加载，闪烁问题

important，BEM

## 行内元素的margin和padding是否起作用

## CSS选择器

## 浏览器CSS匹配顺序：

浏览器CSS匹配不是从左到右进行查找，而是从右到左进行查找。比如`#divBox p span.red{color:red;}`，浏览器的查找顺序如下：先查找html中所有class='red'的span元素，找到后，再查找其父辈元素中是否有p元素，再判断p的父元素中是否有id为divBox的div元素，如果都存在则匹配上。浏览器从右到左进行查找的好处是为了尽早过滤掉一些无关的样式规则和元素。

## 内联元素间隙

标签不换行

```
<ul>
  <li>
   one</li><li>
   two</li><li>
   three</li>
</ul>
```

注释填充

```
<ul>
  <li>one</li><!--
  --><li>two</li><!--
  --><li>three</li>
</ul>
```

负边距，white-space

font-size: 0

float

flexbox