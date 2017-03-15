---
layout: post
title: CSS视觉格式化模型 
date: 2014/09/02 12:30:00
categories: 
  - 前端
  - CSS
description: CSS2.1说明的翻译，讲述了VFM（Visual Formatting Model），盒子模型，包含块，BFC，IFC，定位等
tags: 
  - front-end
  - VFM
  - css
  - css2.1
  - BFC
  - IFC
---

## Preamble

> 这里我把视觉模型谈谈，还没写完，从最重要的有价值的章节（我管它叫做MVS，如果喜欢篮球应该对MVP很了解吧）开始写的，后续还会写，不要因为里面有的只有标题就冲我扔臭鞋。

## 9.1 可视化格式化模型(Visual Formatting Model)

这章和接下来描述了可视化格式化模型（VFM）：用户代理如何在[可视媒体](https://www.w3.org/TR/CSS2/media.html)上处理[文档树](https://www.w3.org/TR/CSS2/conform.html#doctree).

在VFM中，每个在文档树中的元素参照[盒子模型](https://www.w3.org/TR/CSS2/box.html)生成0个或多个盒子。这些盒子的布局由以下几方面掌控：

* [盒子的尺寸](https://www.w3.org/TR/CSS2/box.html#box-dimensions)和[类型](https://www.w3.org/TR/CSS2/visuren.html#box-gen)
* [位置方案](https://www.w3.org/TR/CSS2/visuren.html#positioning-scheme)（普通流、float和绝对定位）
* 元素在文档树中的关系；
* 外部信息（比如viewport的尺寸、图片等实际尺寸等）

这章和接下来定义的属性适用于[continuous media](https://www.w3.org/TR/CSS2/media.html#continuous-media-group)和[paged media](https://www.w3.org/TR/CSS2/media.html#paged-media-group)。然而，margin属性当被用于paged media是有不同的意思（详情参考[page model](https://www.w3.org/TR/CSS2/page.html#page-margins)）。

VFM没有指定格式化的所有方面（比如letter－spacing的算法）。遵循用户代理有可能会对于此说明没有提到的格式化问题有不同的表现。

### 9.1.1 视窗

没啥好说的，就是你浏览页面大大的窗口。

### 9.1.2 包含块

在CSS2.1中，很多盒子位置和尺寸的计算是对于(respect to)长方形盒子（包含块）的边缘计算的。通常生成的盒子作为后代盒子的包含块。常说的“一个盒子的包含块”意思是“这个盒子存在于的包含块”，而不是它生成的。

每个盒子都对于它的包含块提供一个位置，但是它不会被它的包含块限制（confine），它有可能会溢出（overflow）。

在[下章](https://www.w3.org/TR/CSS2/visudet.html)会告诉你包含块的尺寸如何计算的[详细信息](https://www.w3.org/TR/CSS2/visudet.html#containing-block-details)。

## 9.2 控制盒子的生成

接下来的一节描述了CSS2.1中可能会生成的盒子类型。盒子类型在某种程度上（in part）会影响它在VFM中的表现。在下面描述的display属性置顶了盒子类型。

### 9.2.1 块级元素(Block-level Elements)和块盒子(Block Boxes)

块级元素是在原文档中显示格式化为block的元素（比如段落）。display属性值为'block','list-item'和'table'可以让元素称为块级元素。

块级盒子是参与到[块级上下文](https://www.w3.org/TR/CSS2/visuren.html#block-formatting)的盒子。每个块级元素都会产生一个块级盒子来包含后代盒子并生成内容，而且它也参与(involve in)了任何定位方案。一些块级元素有可能会产生除了主块级盒子外的其它盒子：如'list-item'元素。这些额外(additional)的盒子会针对主盒子被放置。

除了table盒子（后续章节会提到）和替换元素，块级盒子也是块容器盒子。

#### 9.2.1.1 匿名块盒子

```html
<div>
  Some text
  <p>More text
</div>
```
（假设div和p都是display:block）

如果在一个块容器盒子（比如上面为div生成的）内部有一个块级盒子（比如上面的p），那么我们就强制它内部只能包含块级盒子，所以为**Some text**这个文本声称了一个匿名块级盒子。

当行内盒子包含一个块级普通流内盒子时，行内盒子（和与它在同一个行盒子的行内父元素）将会被打破并环绕块级盒子（和任何连续的或者仅仅被空格分割的兄弟元素），最终此行盒子被分割成两个盒子（即使某一边是空的）。分割的两个盒子被包含在匿名盒子里。

```css
<!-- 来看个例子 -->
p { display: inline; }
span { display: block; }

```

```html
<p>
  This is anonymous text before the span.
  <span>This is the content of SPAN.</span>
  This is anonymous text after the span.
</p>

<!--
p元素包含一个匿名文本C1，块级元素，另一个匿名文本C2.
-->
```

### 9.2.2 行内级元素(Inline-level Elements)和行内盒子(Inline boxes)

行内级元素（inline-level elements）是在源文档中不会形成内容的新block的元素；这些内容是分散到多个行中（比如，段落中强调的文本，行内image等).下面这些display的值会产生行内级元素：'inline','inline-table'和'inline-block'.行内级元素会产生参与行内格式化上下文（IFC，inline formatting context）的行内级盒子(inline-level box)。

行内盒子(inline box)是行内级盒子，并且它的内容是参与它包含的IFC。display为inline的非替换元素产生一个行内盒子。不是行盒子的行内级盒子（比如行内级替换元素，行内块元素和行内table元素）被称为原子行内级盒子（atomic inline-level box）,因为他们作为一个不透明的盒子参与他们的IFC中。

#### 9.2.2.1 匿名行内盒子

## 9.3 定位方案

...

### 9.3.1 选择一个定位方案：position属性

... 

### 9.3.2 盒子偏移: top, right, bottom, left

...

## 9.4 普通流

### 9.4.1 块格式化上下文(BFC)

浮动、绝对定位元素，不是块盒子的块容器（比如inline-blocks、table-cells和table-captions)和overflow不是visible的块盒子（except when that value has been propagated to the viewport)都会为内容创建一个BFC。在BFC中，盒子是从BFC的顶部开始，竖直方向，一个一个放置的。两个兄弟盒子的竖直间距是有margin决定的。同一BFC中相邻块级盒子的竖直margin会发生[折叠](https://www.w3.org/TR/CSS2/box.html#collapsing-margins)。

在BFC中，每个盒子的左外边接触包含块的左边（对于从右到左的格式化，接触右边）。这个是正确的即使是对于float的展现（尽管一个盒子的杭盒子可能由于浮动而皱缩），除非盒子创建一个新的BFC（在这种情况下，盒子自身可能会因为浮动[变得更狭窄](https://www.w3.org/TR/CSS2/visuren.html#bfc-next-to-float)）。

### 9.4.2 行内格式化上下文(IFC)

在IFC中，盒子是从包含块的顶部水平地一个接一个放置的。水平方向的margin、border和padding是有效的。这些赫宰在竖直方向可能会有不同的方式排列：它们的顶部或底部，也可能是它们中的文本的基线。包含这些盒子的长方形区域（最终形成一条线）叫做行盒子（line box）

### 9.4.3 相对定位

## 9.5 浮动

### 9.5.1 定位浮动：float属性

...

### 9.5.2 控制浮动旁边的流：clear属性

...

## 9.6 绝对定位(Absolute Positioning)

### 9.6.1 固定定位(Fixed Positioning)

## 9.7 display、position和float的关系(重要！)

这三个属性会影响盒子生成和布局，其相互作用如下：

1. 如果**display**是**none**，那么**position**和**float**就不起作用了。在这种情况下，元素不会产生盒子；
2. 否则，如果**position**是**absolute**或者**fixed**，生成的盒子是绝对定位，**float**的计算值是**none**，
   并且**display**按照下表设置。位置将由**top**，**right**，**bottom**，**left**属性和盒子的包含块(containing
   block)决定；
3. 否则，如果**float**有个非**none**的值，盒子就会浮动并且**display**按照下表设置；
4. 否则，如果元素是根元素，**display**按照下表设置，~~除了指定的值是**list-item**(无论计算值将会是**block**或者**list-item**)
   在CSS2.1中将会使根元素的**display**为**undefined**~~；
5. 否则，剩下的**display**属性即为所设置的值。

指定值 | 计算值 
-------|------------------
inline-table | table
inline, table-row-group, table-column, table-column-group, <br/>table-header-group, table-footer-group, table-row, <br/>table-cell, table-caption, inline-block | block
others | same as specified

## 9.8 普通流、浮动和绝对定位的比较

### 9.8.1 普通流

### 9.8.2 相对定位

### 9.8.3 浮动一个盒子

### 9.8.4 绝对定位

## 9.9 分层展示(Layered Presentation)

### 9.9.1 指定栈级别：z-index属性

**z-index**

- Value: auto 或 \<integer\> 或 inherit
- initial: auto
- Applies to : **定位元素**
- Inherited: no
- Percentage: N/A
- Media: visual
- Computed value: 指定的值

对于**定位元素**，**z-index**属性指定了：

1. 盒子的栈级别在当前栈上下文中；
2. 是否盒子创建一个栈上下文。

在CSS2.1中，每个盒子都有一个三维的位置。出了在水平和垂直位置，盒子也会沿着**z轴**布局。z轴位置在盒子可见层叠时是很重要的。

渲染树绘制到canvas上的顺序是按照栈上下文的。栈上下文可以包含更多的栈上下文。A stacking context is atomic from the point of view of its parent stacking context; boxes in other stacking contexts may not come between any of its boxes.

每个盒子只属于一个栈上下文。每个在指定栈上下文的定位盒子都有一个整数的栈级别。栈级别更大的盒子总是在级别低的盒子前面。盒子也可以有负数的级别。一个栈上下文栈级别相同的盒子按照在文档中的顺序确定先后。

根元素形成根栈上下文。其他栈上下文通过任何**z-index**计算值不为**auto**的定位元素(包括相对定位)生成。栈上下文并不必须与包含块相关。

在每个栈上下文，接下来的层按照从后往前的顺序绘制：
~~1. 形成栈上下文的元素的背景和边框；~~
~~2. 负数栈级别的子栈上下文；~~
~~3. 文档流内，非行内非定位的后代；~~
~~4. 非定位浮动元素；~~
~~5. 文档流内，行内非定位后代,，包括inline table 和 inline block；~~
~~6. 栈级别是0的子栈上下文和栈级别是0的定位的后代元素；~~
~~7. 栈级别是正数的子栈上下文（值小的优先）。~~

1. 形成栈上下文元素的背景和边框；
2. z-index是负值的子栈上下文（越负越优先，意思就是越小越优先）；
3. 非inline级、非定位的流内后代元素；
4. 非定位浮动元素；
5. Inline级非定位的流内后代元素，包括inline table和inline block；
6. z-index是0的子栈上下文和栈级别是栈上下文和z-index为0的定位后代元素；
7. z-index为正数的子栈上下文（值小的优先）。

## 结语

终于可以写结尾了，重点是VFM、BFC、z-index、各种盒子，后续内容抽空再写，歇会儿。

## 参考

1. [W3C-Visual formatting model 9](https://www.w3.org/TR/CSS2/visuren.html#visual-model-intro)
