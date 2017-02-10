---
title: Section的重要性
date: 2017-02-09 06:09:16
description: 在以前的十多年里，我们通常会把web页面根据视觉分成不同的节（或者叫做块，盒子，区域等）。问题是我们从没有一款工具可以去做这种划分。往往是网页看起来是分块的，但是底层结构确是用有序的H标签（h1,h2,h3）和非语义化元素堆积起来的。 
categories: 
  - 前端
  - HTML
tags:
  - 前端
  - H5
  - HTML
  - 语义化
---

> 在以前的十多年里，我们通常会把web页面根据视觉分成不同的节（或者叫做块，盒子，区域等）。问题是我们从没有一款工具可以去做这种划分。往往是网页开起来是分块的，但是底层结构确是用编号标题（h1,h2,h3...）和非语义化元素堆积起来的。*因为我们可以用`<div>`来实现视觉上的分块，但是其表现与`<section>`不同。对于<abbr title="assistive technology ">AT</abbr>用户和数据挖掘软件并不认为div分块是文档内容的分块*

现在H5最终有了可用的section元素，但是很多人都不情愿去使用。为什么？因为我们是一个反对改变的群体，而且也因为说明文档与建议存在一些差异。事实上，该建议与section的算法比先前的实现更容易使用。

没错，section元素可以帮助你构建文档结构，这是w3c说明文档里的说到的。接下来，我会探索这些元素解决了什么问题，对于语义化web的重要性。如果你对`语义化web`的概念不熟悉，可以参考[这个视频](http://www.youtube.com/watch?gl=GB&hl=en-GB&v=OGg8A2zfWKg).

## div存在的问题

成千上万的前端工程师到目前为止还在用div把网页内容划分成一块一块的，为的是让相关内容分组。事实上div只能实现视觉上的分块，并不能把实际内容分块，看下面的例子：

![example1](/assets/images/2017/the-important-of-section-example1.png)

在这个基础布局中，在主内容的右边有一个侧边栏。为了让侧边栏与主内容更清晰的分离，我把它周围加了粗线。这时候你肯定会说：侧边栏的标题标签应该是h3。所有我设计（相邻位置，边框和减小的字体大小）都是通过css决定的。因此，当我把css移除时，就会得到这样的页面：

![example2](/assets/images/2017/the-important-of-section-exampl2.png)

移除css不仅是让页面成为响应式页面的最快方法，而且是查看html4文档（缺失section）结构是如何计算的最好方式。在这种情况下，我们所谓的侧边栏其实依然显示为文档流的一部分，并没有独立出去。

### 为什么会这样？

原因是`div`只是流内容元素。不管`div`的边框多么细，背景多么黑，它也不会独立于文档结构。移除css后，这人工（为什么说人工呢，因为是我让它看起来是个侧边栏）的侧边栏的头部`Resources`现在看来与主内容的某一部分也没什么区别。对于解析器和屏幕阅读器，也是这么认为的。

为了清楚起见，来看另一个示例：

```
<div class="parent">
   <h2>Heading</h2>
   <p>Some content...</p>
      <div class="child">
         <h2>Another heading</h2>
         <p>Some other content...</p>
      </div>
   </div>
```

`div.child`标签属于`div.parent`，我能使用css让它们看起来是这样的，但是`<div>`没有特殊含义。不仅仅因为标签名是没语义的，而且它对页面计算的结构[又名document outline](http://html5doctor.com/outlines/)也没有影响。我们使用的`div`其实是看不到的，通过看页面的展示，你是不知道哪个是div的，因此，为了得到有意义的结构，我们应该将其全部移除。这样就只剩4个元素，并且parent-child的关系其实是错觉：

```
<h2>Heading</h2>
   <p>Some content...</p>
   <h2>Another heading</h2>
   <p>Some other content...</p>
```

## 编号标题也没用

通常认为，如果把第二个`<h2>`换成`<h3>`就能解决问题。如果我们这么做，就会这样：

```
* A Heading (h2)
    * Another Heading (h3)
```

这种情况下，第二个标题(h3)实际上成了第一个标题的子标题(h2)，但是第二个标题应该作为同一主题的子heading出现吗？显然不是。单独的编号标题（h1,h2,h3...）只能表明内容的开始，但是不能表示结束，这就导致很难去告诉我们到底谁属于谁。我们必须通过选择正确的编号标题来模拟从属关系，来看另一个例子：

![example3](/assets/images/2017/the-important-of-section-exampl3.png)

在我的HTML4页面中，有h1来介绍该文档的主题，对于主内容有一个h2并且用h3来标示侧边栏(只是一个软弱无力的`div`，正如前面的例子)的开始。按照通常的约定在页面底部还有一个没有标题的`div#footer`（在HTML4中它只能是div，因为`<footer>`还不存在）。那么问题来了，这个footer是属于哪个标题的？

### footer是谁的

`footer，我们走`

![example4](/assets/images/2017/the-important-of-section-figure1.png)

大部分人从页面显示来看更同意footer是属于文档的。对于非视觉来说，其实是不一样的。因为没有新的标题标签在侧边栏h3和footer之间，所以可能会认为它俩是一个整体（看左图）。<abbr title="by the same token">出于同样的原因</abbr>，还有可能像右图那样。

![example4](/assets/images/2017/the-important-of-section-exampl4.png)

很显然，我们只能通过读页面的内容来推测文档的结构。为了补救这个问题，有人建议用一个h2标签去标示`#footer`，把footer拉回原文档流，成为文档的footer，而不是属于其他heading，这也只能算hack，其实也是不合理的。

```
   * h1 (page)
       * h2 (main)
           * h3 (sidebar)
       * h2 (footer)
```

到这里你应该知道为什么在SEO优化时，页面只能有一个h1标签，因为没有section的概念，h1就代表页面的主题，如果再有其他的h1，那蜘蛛机器人懵B了，不知道哪个才是页面的主题。有了section后，在section内部还可以使用h1，而且不会影响seo，因为section自己有自己的空间，内容是自包涵的。经过简单的分析后，接下来我们看看section是如何工作的。

## Sectioning

知道我们为了创建计算的section而对合理元素的渴求，HTML5提供了`<section>`，`<article>`，`<aside>`和`<nav>`。来猜猜下图中有多少section：

![example5](/assets/images/2017/the-important-of-section-exampl5.png)

正确答案是2个section。这里只有一个section元素，就是`aside`。因为`<footer>`和`<header>`**不是section元素**，还剩下`<body>`标签是最外层元素，它会形成一种section(更确切的叫做supersection).

下面是你应该理解的算法：

1. `<body>`是第一个section;
2. `<article>`, `<section>`, `<nav>`和`<aside>`形成subsection;
3. subsections可能包含更多的section(subsection).

来看个例子：

```
<body>
  <article>
    <aside>
      aside
    </aside>
  </article>
  <footer>
  </footer>
</body>
```

![example6](/assets/images/2017/the-important-of-section-exampl6.png)

可以看到缺少说明的标题（h1,h2..），因为现在它不重要了，结构很清晰，这个例子的document outline看起来是这样的：

```
* Document
    * Article
       * Aside
```

这意味着我们能舍弃标题(h1,h2...)的影响，用section就能让文档结构变得清晰，再来看个例子：

```
<h4>Page heading</h4>
<p>Introductory paragraph...</p>
<section>
    <h3>Section heading</h3>
    <p>some content...</p>
    <h2>Subheading</h2>
    <p>content following subheading...</p>
    <section>
        <h1>Sub-subheading</h1>
        <p>content two levels deep...</p>
    </section>
</section>
<h5>Another heading</h5>
<p>Continued content...</p>
```

这个例子中编号标题到处都是。这并不是说明所推荐得，但是它能说明H5的outline算法是如此健壮的。如果我们如果我们把开启section的标题用一个通用的标签（比如`<h>`）替换掉，事情就变得清楚了：

```
<h>Page heading</h>
<p>Introductory paragraph...</p>
<section>
    <h>Section heading</h>
    <p>some content...</p>
    <h2>Subheading</h2>
    <p>content following subheading...</p>
    <section>
        <h>Sub-subheading</h>
        <p>content two levels deep...</p>
    </section>
</section>
<h5>Another heading</h5>
<p>Continued content...</p>
```

这里你需要重点注意的是，这个例子中在计算outline中暴露的唯一错误是那些在同一section中乱序的编号标题。在前面的例子中你会看到我在`<section>`中h2是在h3面的。因为它们是乱序的，outline解释器就会将它们解释称在同一级。

## arcticle

如何来区分`<acticle>`和`<section>`呢？**注意**这里说的是section标签，不是上面提到的section概念。

`<article>`代表一个完整的或者自包含的文档（或页面，应用程序，站点）组成部分。它可以是论坛帖子，杂志或新闻文章，博客，用户提交的评论，交互的组件或者小工具，或者任何其他独立的某内容的某项条目。

当`<arcticle>`嵌套时，内部的`article`表示的文章原则上是与外层`article`相关的。比如，一篇可以评论的博客，其评论就是内层`article`，该博客就是外层`article`，看个实例：

```
<article itemscope itemtype="http://schema.org/BlogPosting">
 <header>
  <h1 itemprop="headline">The Very First Rule of Life</h1>
  <p><time itemprop="datePublished" datetime="2009-10-09">3 days ago</time></p>
  <link itemprop="url" href="?comments=0">
 </header>
 <p>If there's a microphone anywhere near you, assume it's hot and
 sending whatever you're saying to the world. Seriously.</p>
 <p>...</p>
 <footer>
  <a itemprop="discussionUrl" href="?comments=1">Show comments...</a>
 </footer>
</article>

```

而`<section>`表示的是通常意义里的章节的意思。在上下文中，section是主题内容的分组。每个section的主题是确定的，典型的section都有一个编码标题作为其子元素，来看个实例：

```
<article>
 <header>
  <h2>Apples</h2>
  <p>Tasty, delicious fruit!</p>
 </header>
 <p>The apple is the pomaceous fruit of the apple tree.</p>
 <section>
  <h3>Red Delicious</h3>
  <p>These bright red apples are the most common found in many
  supermarkets.</p>
 </section>
 <section>
  <h3>Granny Smith</h3>
  <p>These juicy, green apples make a great filling for
  apple pies.</p>
 </section>
</article>
```

## 总结

鉴于很多开发对section不了解，前几天看到相关内容，就写了这篇文章，主要还是翻译＋部分自己的理解。暂时把主要内容都呈上了，主要需要理解的就是文档的outline，其实就是文档分块，通过html标签来为内容分块，而不是通过阅读页面内容判断哪些内容是一体的，毕竟页面是需要机器去读的。有什么疑问欢迎留言。

## 参考

1. [the importance of sections](https://www.smashingmagazine.com/2013/01/the-importance-of-sections/')
2. [arcticle Element](https://www.w3.org/TR/html5/sections.html#the-article-element)