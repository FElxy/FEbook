# Grid布局

Grid布局是基于网格的二维布局系统

目前(2019.01.19)支持程度如下

![image-20190119232846132](/Users/rain/Library/Application Support/typora-user-images/image-20190119232846132.png)



## 术语概念

### Grid Container

需要应用 `display: grid` 的元素，grid item的直接父元素

### Grid Item

Grid Container的直接子元素

```html
<div class="container">
  <div class="item item-1"></div>
  <div class="item item-2"></div>
  <div class="item item-3"></div>
</div>
```

### Grid Line

网格结构的分界线，垂直(column grid lines)和水平(row grid lines)的分界线

### Grid Track

网格轨道，两条相邻网格线之间的空间

### Grid Cell

网格单元格，相邻行列网格线划分出来的格子

### Grid Area

网格区域，多个网格单元格构成的区域



## 属性目录

### 容器属性

#### display

grid | inline-grid；

grid块级，inline-grid内联

#### grid-template-columns / grid-template-rows

定义列/行

值：
– `<track-size>`： 可以是长度值，百分比，或者等份网格容器中可用空间（使用 `fr` 单位）
– `<line-name>`：你可以选择的任意名称，需要使用[]将名称包含起来

![Grid with user named lines](https://css-tricks.com/wp-content/uploads/2018/11/template-column-rows-02.svg)

定义包含多个重复值，则可以使用 `repeat()` 表示法来简化定义

```
.container {
  grid-template-columns: repeat(3, 20px [col-start]);
}
// 等同于
.container {
  grid-template-columns: 20px [col-start] 20px [col-start] 20px [col-start];
}
```

使用fr均分

```
.container {
  grid-template-columns: 1fr 1fr 1fr;
}
.container {
  grid-template-columns: 1fr 50px 1fr 1fr;
}
```

#### grid-template-areas

引用grid-area指定的网格名称来定义网格模板，相当于是一个可视化配置

- `<grid-area-name>`：由网格项的 grid-area 指定的网格区域名称
- `.`（点号） ：代表一个空的网格单元
- `none`：不定义网格区域

```css
.item-a {
  grid-area: header;
}
.item-b {
  grid-area: main;
}
.item-c {
  grid-area: sidebar;
}
.item-d {
  grid-area: footer;
}

.container {
  grid-template-columns: 50px 50px 50px 50px;
  grid-template-rows: auto;
  grid-template-areas: 
    "header header header header"
    "main main . sidebar"
    "footer footer footer footer";
}
```

这里网格线会自动命名，如header-start, header-end, 一条网格线可能会有多个名字

#### grid-template

用来定义grid-template-rows，grid-template-columns，grid-template-areas的简写属性

```
.container {
  grid-template:
    [row1-start] "header header header" 25px [row1-end]
    [row2-start] "footer footer footer" 25px [row2-end]
    / auto 50px auto;
}

.container {
  grid-template-rows: [row1-start] 25px [row1-end row2-start] 25px [row2-end];
  grid-template-columns: auto 50px auto;
  grid-template-areas: 
    "header header header" 
    "footer footer footer";
}
```

grid-template不会重置隐式网格属性grid-auto-rows, grid-auto-columns, grid-auto-flow

#### grid-column-gap / grid-row-gap | column-gap / row-gap

网格线的大小，设置列、行之间的间距

网格外边缘不会有间距

#### grid-gap

grid-column-gap / grid-row-gap简写

```
grid-gap: 15px 10px；
```

#### justify-items / align-items

网格项的对齐方式

start | end | center | stretch 填满单元格的宽度（默认值）

网格项可以使用justify-self单独设置

#### place-items

`align-items` 和 `justify-items` 的简写形式

第一个值设置 `align-items` 属性，第二个值设置 `justify-items` 属性。如果省略第二个值，则将第一个值同时分配给这两个属性

#### justify-content / align-content

总的网格大小小于container的时候

`start | end | center | stretch | space-around | space-between | space-evenly;`

`space-around`: 在每个网格项之间放置一个均匀的空间，左右两端放置一半的空间

`space-evenly`：在每个网格项目之间放置一个均匀的空间，左右两端放置一个均匀的空间

#### place-content

place-content是align-content justify-content的简写

#### grid-auto-columns / grid-auto-rows

指定任何自动生成的网格轨道(grid tracks)（又名隐式网格轨道）的大小。

#### grid-auto-flow

一些没有明确放置在网格上的网格项，**自动放置算法** 会自动放置这些网格项。该属性控制自动布局算法如何工作。

- `row`：告诉自动布局算法依次填充每行，根据需要添加新行 （默认）
- `column`：告诉自动布局算法依次填入每列，根据需要添加新列
- `dense`：告诉自动布局算法在稍后出现较小的网格项时，尝试填充网格中较早的空缺

#### grid

 `grid-template-rows`, `grid-template-columns`, `grid-template-areas`,`grid-auto-rows`, `grid-auto-colunms`,`grid-auto-flow`

- `none`：将所有子属性设置为其初始值。
- `<grid-template>`：与[`grid-template`](https://www.css88.com/archives/8510#prop-grid-template) 简写的工作方式相同。
- `<grid-template-rows> / [ auto-flow && dense? ] <grid-auto-columns>? `：将[`grid-template-rows`](https://www.css88.com/archives/8510#prop-grid-template-columns-rows) 设置为指定的值。 如果 `auto-flow` 关键字位于斜杠的右侧，则会将 [`grid-auto-flow`](https://www.css88.com/archives/8510#prop-grid-auto-flow) 设置为 `column`。 如果另外指定了 `dense` 关键字，则自动放置算法使用 “dense” 算法。 如果省略 [`grid-auto-columns`](https://www.css88.com/archives/8510#prop-grid-auto-columns-rows) ，则将其设置为 `auto`。
- `[ auto-flow && dense? ] <grid-auto-rows>? / <grid-template-columns>`：将 [`grid-template-columns`](https://www.css88.com/archives/8510#prop-grid-template-columns-rows) 设置为指定值。 如果 `auto-flow` 关键字位于斜杠的左侧，则会将[`grid-auto-flow`](https://www.css88.com/archives/8510#prop-grid-auto-flow) 设置为 `row` 。 如果另外指定了 `dense` 关键字，则自动放置算法使用 “dense” 打包算法。 如果省略 [`grid-auto-rows`](https://www.css88.com/archives/8510#prop-grid-auto-columns-rows) ，则将其设置为 `auto`。

```css
.container {
  grid: 100px 300px / 3fr 1fr;
}
.container {
  grid-template-rows: 100px 300px;
  grid-template-columns: 3fr 1fr;
}
.container {
  grid: auto-flow dense 100px / 1fr 2fr;
}
.container {
  grid-auto-flow: row dense;
  grid-auto-rows: 100px;
  grid-template-columns: 1fr 2fr;
}
```



### 网格项属性

`float`，`display: inline-block`，`display: table-cell`，`vertical-align` 和 `column-*` 属性对网格项无效。

#### grid-column-start / grid-column-end / grid-row-start / grid-row-end

引用特定网格线(grid lines) 来确定 网格项(grid item) 在网格内的位置。

#### grid-column / grid-row

简写形式<start-line> / <end-line>

#### grid-area

为网格项提供一个名称，也可以作为<row-start> / <column-start> / <row-end> / <column-end>的简写

#### justify-self / align-self

start | end | center | stretch;

#### place-self

justify-self / align-self的简写

### Animation

5个属性可应用动画

- `grid-gap`， `grid-row-gap`，`grid-column-gap` 作为长度，百分比或 calc。
- `grid-template-columns`，`grid-template-rows` 作为长度，百分比或 calc 的简单列表，只要列表中长度、百分比或calc组件的值不同即可。



> [A Complete Guide to Grid](https://css-tricks.com/snippets/css/complete-guide-grid/)
>
> [CSS Grid 布局完全指南(图解 Grid 详细教程)](https://www.css88.com/archives/8510)