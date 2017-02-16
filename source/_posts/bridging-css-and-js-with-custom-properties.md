---
title: 使用自定义属性桥接css和javascript(翻译)
date: 2017-02-16 09:46:10
categories: 
  - 前端
  - CSS
  - 翻译
tags: 
  - css 
  - custom-properties 
  - js 
  - abstraction
---

>CSS自定义属性除了简单的保存和获取CSS值机制外，还有很多有趣的用处。在这篇文章里，我们将会探索自定是属性的最佳实践。

## 浏览器支持度

![image](/assets/images/2017/bridging-css-and-js-with-custom-property-figure1.png)

从图中可以看出浏览器的支持度还是挺高的。

## 用class分离css和js

大多数情况下，为了分离关注点和简化两者的交互，你想让js和CSS保持高度分离。最简单的经常被工程师实践的示例是使用定义良好的CSS类，在js端为了触发事件驱动的视觉变化，只需要添加或移除相关的css类，比如：

```css
.button {
  position: relative;
  transform: scale(1);
}
 
.button.js-toggled {
  transform: scale(1.5);
}
```

```js
const button = document.querySelector('.button');
button.addEventListener('click', () => {
  button.classList.toggle('js-toggled');
});
```

这种方式是让css来控制所有的样式，而js只需要触发状态的变化。你可以通过css简单的转换到不同的视觉展示，而不需要改变js代码。同样，你可以是用js改变触发状态改变的条件，而不需要改变css文件。

*
注意：考虑到简单css类和为js交互而定义的类之间的不同，最佳的命名约定是，为js定义的css类最好用`js-`前缀，如上面代码那样。
*

## 在css和js之间传递数据

添加移除类对于布尔状态来说，工作的很好，但是有时你需要更复杂的数据传递。举个例子，如果你要对用户输入（比如单击或触摸）做出反应，同时为了显示一些什么鬼需要考虑输入的坐标位置。

比如，我们有个容器，并且我想展示用户在其中最后点击位置。如果我们有个辅助元素在这个容器中，我们可以移动它通过这样：

```css
.container {
  position: relative;
}
.container > .auxElement {
  position: absolute;
}
```

```js
document.querySelector('.container').addEventListener('click', evt => {
  const aux = document.querySelector('.container > .auxElement');
  aux.style.transform = `translate(${evt.clientX}px, ${evt.clientY}px)`;
});
```

上面的例子是可以运行的，但是在css和js之间没有任何抽象。不仅仅是我们在js中知道辅助元素的存在（理想状态是，我们不应该知道），而且我们甚至是直接操作css属性。

直到现在，没有更好的方法解决这个问题，但是使用自定义属性，我们可以简单的找回丢失的抽象层。

```css
.container {
  position: relative;
  --clickX: 0;
  --clickY: 0;
}
 
.container > .auxElement {
  position: absolute;
  transform: translate(var(--clickX, 0), var(--clickY, 0));
}
```

```js
const container = document.querySelector('.container');
container.addEventListener('click', evt => {
  container.style.setProperty('--clickX', `${evt.clientX}px`);
  container.style.setProperty('--clickY', `${evt.clientY}px`);
});
```

## 一个值改变多个样式

一个来自逻辑层（js）的事件不一定可以在css层面转化成单个变化；它可以影响整个范围。一个最好的相关的例子是主题，像主题颜色就能影响相当数量的元素。

来看个音乐播放器app的例子。如果你想不使用自定义属性让用户界面为当前播放的专辑展现不同的颜色时，你需要维护被这个颜色影响的元素列表。你可以用js这么实现：

```js
const thingsToUpdate = new Map([
  ['playButton', 'background-color'],
  ['title': 'color'],
  ['progress': 'background-color']
])};
 
for (let [id, property] of thingsToUpdate) {
  document.getElementById(id).style.setProperty(property, newColor);
}
```

或者用html：

```html
<span class="title js-update-color">Song title</span>
<button class="play-button js-update-background">Play</button>
<div class="progress-track js-update-background"></div>

const colorList = document.querySelectorAll('.js-update-color');
for (let el of colorList) {
  el.style.setProperty('color', newColor);
}
 
const backgroundList =
    document.querySelectorAll('.js-update-background');
for (let el of backgroundList) {
  el.style.setProperty('background-color', newColor);
}
```

无论哪种方式都会让维护更难，因为并行的受影响的元素和属性需要保持同步更新。

另一种选择就是向该页面注入一个新的样式表，用来覆盖默认颜色。这种方式可能相对来说会好点儿（虽然有点像hack），但是它让眼需要覆盖一定数量的样式并且需要保持一定的模版，这些都是需要维护的，比如：

```css
.play-button {
  background-color: ${newColor} !important;
}
.title {
  color: ${newColor} !important;
}
.progress-track {
  background-color: ${newColor} !important;
}
```

用自定义属性就会变的很简单；只需要决定dom树最高层你需要修改的元素，并且让级联做剩下的事：

```css
.player {
  --theme-color: red;
}
 
.play-button {
  background-color: var(--theme-color);
}
.title {
  color: var(--theme-color);
}
.progress-track {
  background-color: var(--theme-color);
}
```

```js
document.querySelector('.player').style.setProperty(
    '--theme-color', newColor);
```

这种方法不需要你的js脚本知道会影响哪个元素，并且不需要你为了样式的变化而去维护一系列模版。作为奖励（为什么叫作为奖励，不知道），这种方法比前几种要简单很多。

## 自定义属性的重要性

将你的交互限制在css类和自定义属性能让你定义在css和js之间定义严格的接口。这就意味着任何运行时的变化都会被限制在一组定义良好的为交互而存在的实体中。这么做可以让你减小bug出现的范围和非预期的行为，并且能够让分别测试样式和行为变的更简单。

因为你可以分别修改、维护样式和逻辑，所以维护就变的更容易了。

## 参考

1. [Bridging CSS and JS with Custom Properties](https://sgom.es/posts/2017-02-10-bridging-css-and-js-with-custom-properties/?utm_source=frontendfocus&utm_medium=email)<br/>