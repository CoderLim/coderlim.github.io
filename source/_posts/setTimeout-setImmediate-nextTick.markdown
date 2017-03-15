---
layout: post
title: setTimeout setImmediate nextTick
date: 2016.11.25 13:10:00
categories: 
  - 前端
  - javascript 
description: 每次写文章都想在前面提提时事，今天想说的是：*******，如果你看到的是星号，那很抱歉，“福利”被和谐了，你可以更换浏览器试试。本文对setTimeout、setImmediate、process.nextTick的区别做了简单阐述，如有疑问，请留言。
tags: 
  - node
  - setImmediate
  - setTimeout
  - nextTick
---

## 前言 

每次写文章都想在前面提提~~事实~~时事，今天想说的是：*******，如果你看到的是星号，那很抱歉，“福利”被和谐了，你可以更换浏览器试试。本文对setTimeout、setImmediate、process.nextTick的区别做了简单阐述，如有疑问，请留言。

## api介绍

1. **setTimeout(callback, delay)**：经过delay时间后只执行一次callback，但是并不能保证时间点的精确性。delay的意思是，告诉callback可以被执行了，如果callback所在队列前面还有任务没执行，那它也得稍等等。

2. <div id="setImmediate">**setImmediate(callback)**：执行callback的时机是在IO事件回调之后，并且在setTimeout和setInterval创建的timer之前。</div>

3. **nextTick(callback)**: 这个比较简单，会将callback放到执行栈的底部，而setTimeout和setImmediate是添加到任务队列，不知道什么是执行栈和任务队列的参考[这里](http://www.ruanyifeng.com/blog/2014/10/event-loop.html).

## 大众疑惑

有了上面api的解释，那么问题来了，setTimeout与setImmediate的callback谁先执行呢？

来看个例子：

```js
setTimeout(function () {
  console.log('setTimeout')
})

setImmediate(function () {
  console.log('setImmediate')
})
```

实际执行结果是有时setTimeout在前，有时setImmediate在前，并不确定，但是[上面](#setImmediate)不是说setImmediate的回调会在setTimeout和setInterval前面执行吗？经我多方查证，据说是文档的漏洞。

为什么上面的执行顺序不确定呢？程序刚刚执行时libuv的事件循环还没开始，事件循环开始的时候首先会检查timer，如果timer被添加的时间点到事件循环开始的时间间隔比timer的timeout大，那么这个timer就会比任何immediate先被触发，比如：

```js
setTimeout(function () {
  console.log('setTimeout')
}, 1000)

setImmediate(function () {
  console.log('setImmediate')
})
```

这里把timer的timeout设置成1s，那么immediate就会比timer先被触发。这不是废话吗？timer延迟了1s，当然比immediate的回调函数触发的晚，哈哈。

也许nodejs的文档应该高亮显示：只有把immediate和timer放到IO回调函数中，才能保证immediate的callback先于timer的callback执行，比如：

```js
var fs = require('fs')
fs.readFile('nodetest.js', function (err, data) {
  setTimeout(function () {
    console.log('timeout')
  })
  setImmediate(function () {
    console.log('immediate')
  })
})
```

===================2017.02.04更新====================

这三种函数存放回调函数的姿势是不同的：

- setTimeout：其回调存放在红黑树中，查找效率O(lg(n));
- nextTick：其回调存放在数组中，查找效率O(1);
- setImmediate：其回调存放在链表中；

所以，对于立即执行的方法setTimeout(cb, 0)比nextTick效率低。

对于下面这种情况：

```js
process.nextTick(function () {
  console.log('nextTick');
});
setImmediate(function () {
  console.log('Immediate');
});
console.log('正常');
// 结果：nextTick、Immediate、正常
```
这是由于事件循环不同观察者（观察者可以理解为一系列回调函数，每次事件循环都会去问观察者有没有回调函数）是有优先级的，
优先级由高到低依次为：idle观察者、io观察者、check观察者。

还有一点需要说明，刚刚上面提到了`process.nextTick`的回调保存在数组中，setImmediate的回调保存在链表中，
process.nextTick在每次事件循环中会把数组中的所有回调执行，而setImmediate每次只执行一个，来看个例子：

```js
process.nextTick(function () {
  console.log('nextTick1');
});
process.nextTick(function () {
  console.log('nextTick2');
});
setImmediate(function () {
  console.log('setImmediate1');
  // 进入下次循环
  process.nextTick(function () {
    console.log('强势进入');
  });
});
setImmediate(function () {
  console.log('setImmediate2');
});
console.log('正常执行');
// 结果：nextTick1、nextTick2、setImmediate1、强势进入、setImmedate2
```

从执行结果可以看出，当第一个setImmediate执行完，并没有紧接着执行第二个，而是进入了下一次循环。这种设计是为了
保证每轮循环能够快速执行结束，防止CPU占用过多而阻塞后续IO调用的情况。

## 总结

实际上setTimeout最初出现是在浏览器端，毕竟node是后来出现的。我有一个小建议，在浏览器端推荐使用setTimeout，
而在node端使用process.nextTick、Immediate。还有一种[说法][1]提到了，setTimeout与Immediate不是同一队列，所以会出现不可预测的先后执行。
有何不顺眼的地方欢迎指正。

[1]:https://github.com/nodejs/node-v0.x-archive/issues/6034 "说到setTimeout与setImmediate不是同一队列"

## 参考

1.[setImmediate executes after setTimeout #25788](https://github.com/nodejs/node-v0.x-archive/issues/25788)<br/>
2.[NodeJS - setTimeout(fn,0) vs setImmediate(fn)](http://stackoverflow.com/questions/24117267/nodejs-settimeoutfn-0-vs-setimmediatefn)<br/>
3.[JavaScript 运行机制详解：再谈Event Loop](http://www.ruanyifeng.com/blog/2014/10/event-loop.html)
4.[Node.js v7.2.0 Documentation-timer](https://nodejs.org/api/timers.html)
