---
title: 2017面试记
tags:
  - 前端
  - 面试
---

## 蚂蚁金服电面

### Vue与React

面试官: 用过Vue、React这些框架吗？
我：用过，Vue写过项目，原来学过一段时间ReactNative。
面试官：你能说说Vue和React的区别吗？
我：React给我的印象就是语法想对复杂，学习成本高。
面试官：还有别的区别吗？
。。。（心想虚拟DOM，不行，这是共同点）
我：额。。。因为react我也没写过大项目，其他的区别就不知道了。

更多内容可以参考[这里](http://www.tuicool.com/articles/2QJjeeQ)，这篇文章从两者的用法上来分析区别，最好是再文章顶部有个总结，不然让人感觉有点冗长。

[这个](http://vuejs.org/v2/guide/comparison.html#Performance-Profiles)是尤雨溪的分析：

```
共同点：
  1. 虚拟dom；
  2. 响应式、可组合的组件；
  3. 维护的焦点是核心库，同时关注像路由和全局状态管理的协同库；
不同点：
  1. vue的虚拟dom更轻量（它参考的是snabbdom）；
  2. 当组件的state改变时，react就会渲染该组件的整个子树，必须用shouldComponentUpdate去自行控制；
     在vue的渲染中，组件的依赖是自动跟踪的，因此系统可以精确的知道谁需要重新渲染；
  3. react有自己的jsx，一切皆js；vue是使用的模版；
```

### 双向绑定

面试官：你知道双向绑定是如何实现的吗？
我：（小紧张说了很多很乱，这里我组织一下语言说说）当数据赋值的时候，在该数据的setter方法中去改变UI，当UI有改变的时候，调其类似onchange的方法，在onchange中改变数据。
面试官：你说的这是如何使用，你有没有想过像Vue和React这些是如何实现的？
我：Vue的我了解，Vue中涉及到三个类Dep、watcher、observer，blabla。。。
面试官：嗯，可以，还是可以说上来的。

```
vue的双向绑定其实就是setter＋onchange；而react的双向绑定是基于state的
```

### diff算法

面试官：你知道diff算法和虚拟dom吗？
我：我了解虚拟dom和diff算法，但是没有深入研究过。
面试官：嗯，没关系。

**react的diff算法**:[点我](http://www.infoq.com/cn/articles/react-dom-diff?from=timeline&isappinstalled=0)
**vue的diff算法**:[点我](https://github.com/youngwind/blog/issues/91)

### hybrid

面试官：有没有做过混合开发？
我：没有。

### 与native交互

面试官：你平常做pc端多还是移动端多？
我：都差不多吧，移动端稍微多一点。
面试官：有没有做过与native的交互？
我：做过，与移动开发合作过。

面试官：你对es6了解吗？
我：很熟悉，经常用到es6的语法。

### express vs koa

面试官：你常用的框架是什么？
我：您是说express和koa吗？
面试官：对对，你能否说说两者的区别
我：koa使用比express简单，主要是用法上，其他区别就不太清楚了，毕竟也没做过深入研究。

可以参考[这里](https://cnodejs.org/topic/55815f28395a0c1812f18257)
```
我来总结一下我目前的理解：
1. koa剔除了express的路由；
2. koa的中间件有三种书写形式：Common、generator、async/await；
3. 目前express生态比较好，koa大势所趋。
```

### 箭头函数

面试官：你知道箭头函数和function函数的区别吗？
我：箭头函数可以自动绑定当前作用域。
面试官：还有其他区别吗？
我：其他想不起来了，主要区别就是这一点。

[更全面的答案](http://stackoverflow.com/questions/32535110/what-are-the-differences-if-any-between-es6-arrow-functions-and-functions-boun)
```
1. Arrow functions cannot be constructed;
2. Arrow functions do have lexical arguments, new.target and super as well (not only lexical this);
3. Arrow functions don't actually bind a this value;
4. Arrow functions cannot be generator functions (though they can return generators).
```

### generator与promise

面试官：好的，那你说说generator和promise的区别吧。
我：generator的出现我感觉就是为了自动执行多个异步函数；promise是为了解决异步函数回调地狱，把倒金字塔形的回调，转化成线性执行；
面试官：额。。。回头你可以去看看generator的使用方法。
我：我看过generator的使用方法，generator可以使得函数暂停执行，通过调next可以继续执行。
面试官：嗯，嗯，是，有这么点意思了。
（我说的可能太主观了，都是我自己的理解，应该学会说说官方说法，或者更简单清晰的表达）
（其实这个问题，说说两者是什么，有什么特点就ok了，是我想多了，哈哈）

面试官：下面我问几个基础的js问题吧。
我：好的。

### 跨域

我：跨域的主要方法是jsonp，利用img或者script标签的src属性，因为src属性是可以跨域的。window.domain也可以实现，突然这么问我，我一时想不起来其他的了。
面试官：嗯，没事，可以。

主要有这[几种](http://www.cnblogs.com/2050/p/3191744.html)
```
1. jsonp;
2. window.postMessage;
3. window.domain;
4. window.name;
```

### 类的创建与继承

面试官：在没有class之前，ES5之前是如何实现类的创建和继承的？
我：我在原来看过js高级教程3，blabla。。。
面试官：嗯，不错，那说说如何实现跨域吧。

### cookie与localstorage

面试官：你说说cookie与localstorage的区别。
我：从数据量上说cookie的存储量要小，localstorage没有限制；cookie每次访问服务器的时候都会被http请求携带，localstrage是存储在本地的，主要就是这两点吧。
面试官：嗯，好的，先聊到这里，我们是先电面，了解一下情况，接下来我们的主管会筛选比较好的来邀请面试。
我：好的。

细节可以参考[这里](http://stackoverflow.com/questions/3220660/local-storage-vs-cookies)
```
1. cookie总是被同源http请求携带，WebStorage不会；
2. cookie可以设置过期时间和path，SessionStorage只在本次会话有效；
3. cookie的大小是4k，WebStorage没这个限制；
```

### 总结

这是今年的第一个电面，紧张，表达不清，不过该说的都说了，需要注意的是，如果有人问你xx和xx的区别，首先要说这两个东西是什么，以及有什么特点，因为面试官想知道的是你是否知道这两个是什么，而不是你有多么会用自己的话描述，往往自己的话描述的不够权威。
