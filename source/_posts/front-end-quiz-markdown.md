---
title: 前端测试题
date: 2017-03-16 15:48:56
description: 这些测试题是David Shariff写的，感觉挺有意思，拿来分享一下。(不要问我他是谁，因为我也布吉岛)
categories:
  - 前端
  - 测试
tags:
  - 前端
  - 测试
---

原测试链接：http://davidshariff.com/quiz/

> 这些测试题是David Shariff写的，感觉挺有意思，拿来分享一下。(不要问我他是谁，因为我也布吉岛)

## CSS

1、 CSS是大小写敏感的吗? 不是

```css
ul {
    MaRGin: 10px;
}
```

2、 `margin-top`和`margin-bottom`会对行内元素有影响吗？不会

3、 `padding-top`和`padding-bottom`会影响行内元素的大小吗？不会

4、 如果有个`<p>`的样式为`font-size:10rem`，当用户改变浏览器大小时，它的文字是响应式的吗？不是

5、 伪类`:checked`将会选择type为radio和checkbox的input，而不会选择`<option>`。错误

6、 在HTML文档中，`:root`总是指向`<html>`元素。正确

7、 `translate()`函数可以在z轴上移动元素。错误

8、 Sausage的文本是什么颜色？蓝色

```html
<ul class="shopping-list" id="awesome">
    <li><span>Milk</span></li>
    <li class="favorite" id="must-buy"><span class="highlight">Sausage</span></li>
</ul>
```

```css
ul {
    color: red;
}
li {
    color: blue;
}
```

9、 Sausage的文本是什么颜色？蓝色

```html
<ul class="shopping-list" id="awesome">
    <li><span>Milk</span></li>
    <li class="favorite" id="must-buy"><span class="highlight">Sausage</span></li>
</ul>
```

```css
ul li {
    color: red;
}
#must-buy {
    color: blue;
}
```

10、 Sausage的文本是什么颜色？蓝色

```html
<ul class="shopping-list" id="awesome">
    <li><span>Milk</span></li>
    <li class="favorite" id="must-buy"><span class="highlight">Sausage</span></li>
</ul>
```

```css
.shopping-list .favorite {
    color: red;
}
#must-buy {
    color: blue;
}
```

11、 Sausage的文本是什么颜色？蓝色

```html
<ul class="shopping-list" id="awesome">
    <li><span>Milk</span></li>
    <li class="favorite" id="must-buy"><span class="highlight">Sausage</span></li>
</ul>
```

```css
ul#awesome {
    color: red;
}
ul.shopping-list li.favorite span {
    color: blue;
}
```

12、 Sausage的文本是什么颜色？蓝色

```html
<ul class="shopping-list" id="awesome">
    <li><span>Milk</span></li>
    <li class="favorite" id="must-buy"><span class="highlight">Sausage</span></li>
</ul>
```

```css
ul#awesome #must-buy {
    color: red;
}
.favorite span {
    color: blue!important;
}
```

13、 Sausage的文本是什么颜色？蓝色

```html
<ul class="shopping-list" id="awesome">
    <li><span>Milk</span></li>
    <li class="favorite" id="must-buy"><span class="highlight">Sausage</span></li>
</ul>
```

```css
ul.shopping-list li .highlight {
    color: red;
}
ul.shopping-list li .highlight:nth-of-type(odd) {
    color: blue;
}
```

14、 Sausage的文本是什么颜色？蓝色

```html
<ul class="shopping-list" id="awesome">
    <li><span>Milk</span></li>
    <li class="favorite" id="must-buy"><span class="highlight">Sausage</span></li>
</ul>
```

```css
#awesome .favorite:not(#awesome) .highlight {
    color: red;
}
#awesome .highlight:nth-of-type(1):nth-last-of-type(1) {
    color: blue;
}
```

15、 `#example`的位置有什么变化? 所有#example后面的元素向上移动5px；

```html
<p id="example">Hello</p>
```

```css
#example {
    margin-bottom: -5px;
}
```

16、 `#example`的位置有什么变化? 将会向左移动5px；

```html
<p id="example">Hello</p>
```

```css
#example {
    margin-left: -5px;
}
```

17、 浏览器会下载未使用的样式资源吗？不会

```html
<div id="test1">
    <span id="test2"></span>
</div>
```

```css
#i-am-useless {
    background-image: url('mypic.jpg');
}
```

18、 在页面加载时，`mypic.jpg`会被浏览器下载吗？会

```html
<div id="test1">
    <span id="test2"></span>
</div>
```

```css
#test2 {
    display: none;
    background-image: url('mypic.jpg');
}
```

19、 在页面加载时，`mypic.jpg`会被浏览器下载吗？不会

```html
<div id="test1">
    <span id="test2"></span>
</div>
```

```css
#test1 {
    display: none;
}
#test2 {
    background-image: url('mypic.jpg');
    visibility: hidden;
}
```

20、 `only`选择器有什么作用？阻止老浏览器解析这个选择器的其它部分

```css
@media only screen and (max-width: 1024px) {
    margin: 0;
}
```

21、 `overflow: hidden` 会创建一个新的BFC吗? 会

```html
<div>
    <p>I am floated</p>
    <p>So am I</p>
</div>
```

```css
div {
    overflow: hidden;
}
p {
    float: left;
}
```

22、 `screen`关键字被应用于设备的物理屏幕还是浏览器的视窗？浏览器的视窗

```css
@media only screen and (max-width: 1024px) {
    margin: 0;
}
```

## html

1、 `<keygen>`是H5有效的标签吗？是

2、 `<bdo>`标签会改变文本的方向吗？会

3、 下面的html是有效的吗？是

```html
<figure>
	<img src="myimage.jpg" alt="My image">
	<figcaption>
		<p>This is my self portrait.</p>
	</figcaption>
</figure>
```

4、 什么情况下你会用`<small>`？2

    1. 当你在h1后面创建子标题时；
    2. 当你在footer中创建copyright信息时；
    3. 这两种情况都可以；

5、 当页面中包含多个`<h1>`标签时，会对SEO有负面影响吗？不会

6、 如果你有一个搜索结果页，并且想高亮搜索的条目，你会使用什么html标签？2

  1. `<strong>`
  2. `<mark>`
  3. `<em>`
  4. `<highlight>`

7、 `scoped`的作用是什么？2

```html
<article>
    <h1>Hello World</h1>
    <style scoped>
        p {
            color: #FF0;
        }
    </style>
    <p>This is my text</p>
</article>
 
<article>
    <h1>This is awesome</h1>
    <p>I am some other text</p>
</article>
```

  1. 对后续同父元素应用样式；
  2. 对scoped的父元素的所有子元素应用样式；
  3. 只对IE浏览器应用样式

8、 HTML5支持块级超级链接吗? 支持

```html
<article>
    <a href="#">
        <h1>Hello</h1>
        <p>I am some text</p>
    </a>
</article>
```

9、 第一次加载时，这html会触发http请求吗？会

```html
<img src="mypic.jpg" style="visibility: hidden" alt="My picture">
```

10、 第一次加载时，这html会触发http请求吗？会

```html
<div style="display: none;">
    <img src="mypic.jpg" alt="My photo">
</div>
```

11、 在`Hello World`弹出前，`main1.css`必须下载并解析完吗？是的

```html
<head>
    <link href="main1.css" rel="stylesheet">
    <script>
        alert('Hello World');
    </script>
</head>
```

12、 在`main2.css`被获取前，`main1.css`必须下载并解析完吗？不用

```html
<head>
    <link href="main1.css" rel="stylesheet">
    <link href="main2.css" rel="stylesheet">
</head>
```

13、 `Paragraph 1`在页面上被渲染之前，`main2.css`必须下载并解析完吗？是的

```html
<head>
    <link href="main1.css" rel="stylesheet">
</head>
<body>
    <p>Paragraph 1</p>
    <p>Paragraph 2</p>
    <link href="main2.css" rel="stylesheet">
</body>
```

## javascript

1、 下面的语句结果是什么？"1234"

```js
"1" + 2 + "3" + 4;
```

2、下面的语句结果是什么？"91"

```js
4 + 3 + 2 + "1"
```

3、 弹出什么？ 

```js
var foo = 1;
function bar() {
	foo = 10;
	return;
	function foo() {}
}
bar();
alert(foo);
```

4、 弹出什么？ function
What is alerted? 2

```js
function bar() {
    return foo;
    foo = 10;
    function foo() {}
    var foo = 11;
}
alert(typeof bar());
```

5、 弹出什么？3,1

```js
var x = 3;

var foo = {
    x: 2,
    baz: {
        x: 1,
        bar: function() {
            return this.x;
        }
    }
}

var go = foo.baz.bar;

alert(go());
alert(foo.baz.bar());
```

6、 弹出什么？3,1

```js
var x = 4,
    obj = {
        x: 3,
        bar: function() {
            var x = 2;
            setTimeout(function() {
                var x = 1;
                alert(this.x);
            }, 1000);
        }
    };
obj.bar();
```

7、 弹出什么？2

```js
x = 1;
function bar() {
    this.x = 2;
    return x;
}
var foo = new bar();
alert(foo.x);
```

8、 弹出什么？3

```js
function foo(a) {
    alert(arguments.length);
}
foo(1, 2, 3);
```

9、 弹出什么？undefined

```js
var foo = function bar() {}; 
alert(typeof bar);
```

10、 弹出什么？2

```js
var arr = [];
arr[0]  = 'a';
arr[1]  = 'b';
arr.foo = 'c';
alert(arr.length);
```

11、 弹出什么？2

```js
function foo(a) {
    arguments[0] = 2;
    alert(a);
}
foo(1);
```

12、 弹出什么？number

```js
function foo(){}
delete foo.length;
alert(typeof foo.length);
```

13、 结果是什么？2

```js
"1" - - "1"
```

14、 结果是什么？"f,o,o"

```js
[] + [] + 'foo'.split('');
```

15、 控制台会打印什么？undefined

```js
var x = 0;
function foo() {
    x++;
    this.x = x;
    return foo;
}
var bar = new new foo;
console.log(bar.x);
```

16、 结果是什么？false

```js
10 > 9 > 8 === true;
```

17、 弹出什么？undefined

```js
function foo(a, b) {
    arguments[1] = 2;
    alert(b);
}
foo(1);
```

## 总结

这些小测试有易有难，如果你有疑问就请留言交流吧。