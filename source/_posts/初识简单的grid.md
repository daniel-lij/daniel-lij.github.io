---
title: 初识简单的grid
date: 2018-04-19 9:11:16
tags:
- CSS
categories: [CSS,grid布局]
---

> * 文中代码测试网站 &nbsp;&nbsp;[[文中代码测试网站地址]](https://codepen.io/pen/)

## grid布局概述

####  网格是一组相交的水平线和垂直线，它定义了网格的列和行。我们可以将网格元素放置在与这些行和列相关的位置上

> * 固定的位置和弹性的轨道的大小:你可以使用固定的轨道尺寸创建网格，比如使用像素单位。你也可以使用比如百分比或者专门为此目的创建的新单位 fr来创建有弹性尺寸的网格
> * 元素位置:你可以使用行号、行名或者标定一个网格区域来精确定位元素。网格同时还使用一种算法来控制未给出明确网格位置的元素
> * 创建额外的轨道来包含元素:可以使用网格布局定义一个显式网格，但是根据规范它会处理你加在网格外面的内容，当必要时它会自动增加行和列，它会尽可能多的容纳所有的列
> * 对齐控制:网格包含对齐特点，以便我们可以控制一旦放置到网格区域中的物体对齐，以及整个网格如何对齐。
> * 控制重叠内容:多个元素可以放置在网格单元格中，或者区域可以部分地彼此重叠。然后可以CSS中的z-index属性来控制重叠区域显示的优先级

## 简单的grid布局

#### 我们通过在元素上声明 *** display：grid *** 或 ***  display：inline-grid *** 来创建一个网格容器。一旦我们这样做，这个元素的所有直系子元素将成为网格元素

```html
<!-- HTML -->
<div class="wrapper">
   <div>One</div>
   <div>Two</div>
   <div>Three</div>
   <div>Four</div>
   <div>Five</div>
</div>
```

```css
/* CSS */
* {box-sizing: border-box;}

.wrapper {
    /* 重点是这一句代码 */
    display: grid;
}

.wrapper {
    border: 2px solid #f76707;
    border-radius: 5px;
    background-color: #fff4e6;
}

.wrapper > div {
    border: 2px solid #ffa94d;
    border-radius: 5px;
    background-color: #ffd8a8;
    padding: 1em;
    color: #d9480f;
}
```

##网格轨道

####在grid中我们通过 grid-template-columns 和 grid-template-rows 属性来定义网格中的行和列\
####先固定宽度 

```html
<!-- HTML -->
<div class="wrapper">
   <div>One</div>
   <div>Two</div>
   <div>Three</div>
   <div>Four</div>
   <div>Five</div>
</div>
```

```css
/* CSS */
* {box-sizing: border-box;}

.wrapper {
    /* 重点是这一句代码 */
    display: grid;
    /* 固定宽度可以写多个属性值,你可以写一个属性值也可以像我一样写4个,总之写多少个一行中显示多少个 */ 
    grid-template-columns: 200px 200px 200px 200px;
}

.wrapper {
    border: 2px solid #f76707;
    border-radius: 5px;
    background-color: #fff4e6;
}

.wrapper > div {
    border: 2px solid #ffa94d;
    border-radius: 5px;
    background-color: #ffd8a8;
    padding: 1em;
    color: #d9480f;
}
```

####  网格还引入了一个另外的长度单位来帮助我们创建灵活的网格轨道。

```html
<!-- HTML -->
<div class="wrapper">
   <div>一</div>
		<div>二</div>
		<div>三</div>
		<div>四</div>
		<div>五</div>
		<div>六</div>
		<div>七</div>
		<div>八</div>
		<div>九</div>
		<div>十</div>
		<div>十一</div>
		<div>十二</div>
		<div>十三</div>
		<div>十四</div>
		<div>十五</div>
		<div>十六</div>
		<div>一</div>
		<div>二</div>
		<div>三</div>
		<div>四</div>
		<div>五</div>
		<div>六</div>
		<div>七</div>
		<div>八</div>
		<div>九</div>
		<div>十</div>
		<div>十一</div>
		<div>十二</div>
		<div>十三</div>
		<div>十四</div>
		<div>十五</div>
		<div>十六</div>
</div>
```

```css
/* CSS */
* {box-sizing: border-box;}

.wrapper {
    /* 重点是这一句代码 */
    display: grid;
    /* 使用fr也可以写多个属性值,你可以写一个属性值也可以像我一样写4个,总之写多少个一行中显示多少个,而且是等比例进行均分 */ 
    grid-template-columns: 1fr 2fr 1fr 1fr;
    <!-- 有着多轨道的大型网格可使用 repeat() 标记来重复部分或整个轨道列表,我写的意思是一行均分4个-->
    grid-template-columns: repeat(4, 1fr);
    <!-- 有着多轨道的大型网格可使用 repeat() 网格将有共计10个轨道，为1个1fr轨道后面跟着1个2fr轨道，该模式重复5次-->
    grid-template-columns: repeat(4, 1fr, 2fr);
    <!-- 网格中每一行的高度 第一行高度25% 第二行高度100px 第三行高度200px 其他是auto-->
    grid-template-rows: 25% 100px 200px auto;
    <!-- grid-auto-rows的属性值是能够影响grid-template-rows中的auto的-->
    grid-auto-rows: 200px;
}

.wrapper {
    border: 2px solid #f76707;
    border-radius: 5px;
    background-color: #fff4e6;
}

.wrapper > div {
    border: 2px solid #ffa94d;
    border-radius: 5px;
    background-color: #ffd8a8;
    padding: 1em;
    color: #d9480f;
}
```

####  网格高度的最大最小值

```html
<!-- HTML -->
<div class="wrapper">
    <div>One</div>
    <div>Two
        <p>I have some more content in.</p>
        <p>This makes me taller than 100 pixels.</p>
    </div>
   <div>Three</div>
   <div>Four</div>
   <div>Five</div>
</div>
```

```css
/* CSS */
* {box-sizing: border-box;}

.wrapper {
    /* 重点是这一句代码 */
    display: grid;
    grid-template-columns: repeat(4, 1fr, 2fr);
    grid-auto-rows: minmax(200px,auto);
}

.wrapper {
    border: 2px solid #f76707;
    border-radius: 5px;
    background-color: #fff4e6;
}

.wrapper > div {
    border: 2px solid #ffa94d;
    border-radius: 5px;
    background-color: #ffd8a8;
    padding: 1em;
    color: #d9480f;
}
```

#### 当我们定义网格时，我们定义的是网格轨道，而不是网格线。Grid 会为我们创建编号的网格线来让我们来定位每一个网格元素,三列两行的网格中，就拥有四条纵向的网格线,三条横向的网格线

![](/images/文章图片/flex-start.png)

```html
<!-- HTML -->
<div class="wrapper">
    <div class="box1">One</div>
    <div class="box2">Two</div>
    <div class="box3">Three</div>
    <div class="box4">Four</div>
    <div class="box5">Five</div>
</div>
```

```css
/* CSS */
* {box-sizing: border-box;}
.wrapper{
    border: 2px solid #f76707;
    border-radius: 5px;
    background-color: #fff4e6;
}
.wrapper div{
    border: 2px solid #ffa94d;
    border-radius: 5px;
    background-color: #ffd8a8;
    padding: 1em;
    color: #d9480f;
}
<!-- 以下代码需要多次尝试 反复试验才能很好的运用 -->
.wrapper { 
    display: grid; 
    grid-template-columns: repeat(3, 1fr); 
    grid-auto-rows: 100px; 
} 
.box1 { 
    grid-column-start: 1; 
    grid-column-end: 3; 
    grid-row-start: 1; 
    grid-row-end: 3; 
} 
.box2 { 
    grid-column-start: 3; 
    grid-column-end: 4;
    grid-row-start: 1; 
    grid-row-end: 3; 
}
```


### 未完待续