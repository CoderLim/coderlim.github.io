---
layout: post
title: animation-timing-function 之 steps()
date: 2016.11.09 20:30:00
categories: 
  - 前端
  - CSS
description: 这篇博客是面向于对css动画有一定了解的童鞋，主要是区分step-start和step-end.
tags: 
  - 前端
  - CSS
  - step-start
  - step-end
  - timing-function
---

## 随便聊聊

最近三星在研究炸弹，不仅手机能爆炸，洗衣机也会爆炸，尼玛，产品设计师难不成是研究炸弹的；NBA开赛了，可惜湖人没了科比的身影，在我还没成为球迷之前他已经不在了，很湿伤心，还是听听音乐吧。说到听歌，给大家推荐一首神曲《我的滑板鞋》，看到这歌名，我想你们内心是崩溃的，听到`原唱`你们肯定骂了不止一百遍逗比碧池，心细的同学可能注意到我提到了原唱，没错原唱是不堪入耳，但是经过华晨宇的改编已经脱胎换骨了，“一步两步，一步两步”，steps, step-start, step-end。

## 示例

CSS:

```css
    .cube {
      height: 150px;
      width: 200px;
    }
    .start {
      border: 20px solid red;
      animation: anima 4s infinite step-start;
    }
    .end {
      border: 20px solid green;
      animation: anima 4s infinite step-end;
    }

    @keyframes anima {
      25% {
        border-top-color: transparent;
      }
      50% {
        border-right-color: transparent;
      }
      75% {
        border-bottom-color: transparent;
      }

```

本来想把剂夫图发上来，但是录制的时间点总是不对，防止误导就不贴图了。

## steps

steps(number_of_step, direction)定义了一个step函数,将输出分成等距的步。还是用我擅长的方言说吧，比如steps(2,start)(先忽略*start*)，意思是把时间分成2等分，每等个时间段持续一种不变的状态，没有中间的过渡，可以从下面两个图看出来。

- **number_of_step**：正数，代表组成stepping函数的等距步的多少，即步数；
- **direction**：表示stepping函数是[左持续还是右持续][1]；
  - **start**：表示左持续函数，因此当动画开始时，第一步就会发生，什么意思呢？也就是动画刚开始的一刹那，第一步就结束了，你还没来得急看到就没了；
  - **end**：表示右持续，因此当动画结束时，最后一步就会发生，这又TTMM什么意思呢？意思是动画结束的一刹那是最后一步的开始，也是最后一步的结束。

什么是左持续和右持续呢？请看下面坐标里的大头针，针尖方向的左就是左持续，相反就是右持续，^~^!

[狠狠的点击这里][2]，这是个steps(4)的示例。

steps\(2,start\):
![steps(2,start)](/assets/images/2016/steps(2,start.png)

steps\(4,end\):
![steps(4,end)](/assets/images/2016/steps(4,end.png)

## step-start

![steps-start](/assets/images/2016/step-start.png)

这个关键字与steps(1, start)是一样的。使用这个时间函数，动画立即跳到最终的状态，呆在那个位置直到动画结束。

## step-end

![step-end](/assets/images/2016/step-end.png)

这个与steps(1,end)等同。在用这个函数时，动画呆在初始状态直到结束。阿西吧吧，什么意思？一会儿通过实例讲解。

有一点需要注意，对于帧动画，每一帧都会应用时间函数，也就是某一帧的开头就是时间函数的开头，相应的该帧的结尾就是时间函数的结尾。接下来用我的姿势来告诉你这两个是怎么表现的，还是拿最上面的示例来说话，来看看这段4s动画的实现：

```css
    @keyframes anima {
      25% {
        border-top-color: transparent;
      }
      50% {
        border-right-color: transparent;
      }
      75% {
        border-bottom-color: transparent;
      }
    }

```

~~虽然step-XXX都是一步到位（numbers_of_step＝1），也就是只有一个大头针，但是有四帧动画，实际上主要还是用到了方向这个参数。~~

~~从数学归纳法的角度来看看规律：~~

~~step-start：~~<br>
~~1、 1s，动画的最终状态是25%，50%，75%样式叠加，所以只显示左边框；~~<br>
~~2、 2s是50%，75%样式的叠加，所以只隐藏了右和下边框；~~<br>
~~3、 3s是只有75%的样式，所以只隐藏了下边框；~~<br>
~~4、 4s是这几种样式都不存在的状态，所以四边都显示了。~~<br>

~~step-end：也许这种更符合我们的直觉~~<br>
~~1、 1s，动画的最初状态是四边都显示；~~<br>
~~2、 2s，只有25%的样式，也就是只隐藏顶部；~~<br>
~~3、 3s是25%和50%样式的叠加，所以隐藏了顶部和右边；~~<br>
~~4、 4s是25%，50%，75%样式的叠加，所以只有左边显示。~~<br>

~~是不是不太形象生动，那好，我来从方向的角度来解释：~~<br>

~~step-start是一根大头针从帧动画的100%直接插入到0%：~~<br>
~~1、 1s，帧动画0%，在针尖的位置，所以从针尖到针冒的样式就是当前的样式，正如数学归纳法说的是25%，50%，75%样式的叠加；~~<br>
~~2、 2s，帧动画25%，在距离针尖1/4的位置，从此位置到针冒的样式就是50%，75%叠加；~~<br>
~~...~~<br>
~~依此类推吧，不多废话了，同理step-end是大头针从帧动画的0%直捣黄龙到100%，样式的叠加也是某个位置到针冒样式的叠加，慢慢体会吧。~~<br>

上面啰啰嗦嗦写了这么多，只能算是找规律，但是还是要从原理上找原因。因为时间函数是应用在帧动画的每一帧的，**step-start**在动画开始的时候，每一帧的起始状态都是其最终的状态，对于上面的栗子就是上右下三边都是透明的，当第一次**step-start**结束，因为是左持续的，所以上边变成不透明，第二次右边变成不透明，以此类推。

同理，**step-end**动画开始的时候，每帧显示的状态都是它们的最初状态，也就是都是不透明的。对于**step-end**第一次结束时，上边变成透明的，一直持续到动画结束（因为是右持续的），以此类推。

## 结语

初学该时间函数，着实头疼，只怪自己看文档不仔细，现在基本上已经能正确的理解了，有不正确的地方，欢迎指正。

## 参考

1.[single-transition-timing-function](https://developer.mozilla.org/en-US/docs/Web/CSS/single-transition-timing-function)<br/>
2.[animation-timing-function][3]<br/>



[1]: http://en.wikipedia.org/wiki/Left-continuous#Directional_and_semi-continuity "left- or right-continuous"
[2]: https://jsfiddle.net/CoderLim/e6hd40c8/ "steps(4)示例"
[3]: https://developer.mozilla.org/en-US/docs/Web/CSS/animation-timing-function "css3 animation"
