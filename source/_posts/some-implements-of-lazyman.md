---
title: lazyman的两种实现方式
date: 2017-02-12 08:58:03
description: 最近在网上看到有部分人在面试（比如[1月面试记](https://gold.xitu.io/post/587dab348d6d810058d87a0a)）的时候被问到了LazyMan，而且网友也有不同的编码，于是乎我也写了两个demo以供参考。
categories: 
  - 前端
  - javascript
tags:
  - 前端
  - javascript
  - lazyman
  - promise
---

> 最近在网上看到有部分人在面试（比如`1月面试记`）的时候被问到了LazyMan，而且网友也有不同的编码，于是乎我这里用两种方式实现了lazyman，以供参考，如有更好的方法，欢迎留言。

## 队列

这种方式，每次都是往queue里添加执行函数，next函数用来决定什么时候调用下一个函数。

```
var LazyMan = function LazyMan (name) {
    if (!this) return new LazyMan(name);
    this.queue = [() => {
        console.log(`hello ${name}`);
        this.next();
    }];

    setTimeout(() => {
        this.next();
    });
};

LazyMan.prototype.next = function next() {
    if (this.queue.length) {
        this.queue.shift()();
    }
}

LazyMan.prototype.eat = function eat(food) {
    this.queue.push(() => {
        console.log(`eat ${food}`)
        this.next();
    });
    return this;
};

LazyMan.prototype.sleep = function sleep(sec) {
    this.queue.push(() => {
        setTimeout(() => {
            console.log(`wake up after  ${sec}s`);
            this.next();
        }, sec * 1000);
    });
    return this;
};

LazyMan.prototype.sleepFirst = function sleepFirst(sec) {
    this.queue.unshift(() => {
        setTimeout(() => {
            console.log(`wake up after ${sec}s`);
            this.next();
        }, sec * 1000);
    });
    return this;
};
LazyMan('glm').eat('apple').sleep(2).eat('banana').sleepFirst(3).eat('peach');
```

这里有一点需要点一下，就是我的queue已经就绪了，该怎么执行呢，这里用到了`setTimeout(function () {}, 0);`，在下一次事件循环就开始执行，因为所有向队列添加函数都是在同一次事件循环里。

## Promise

```
var LazyMan = function LazyMan(name) {
    if (!this) return new LazyMan(name);
    this.firstPromise = this.promise = new Promise((fullfill, reject) => {
        console.log(`hello ${name}`);
        fullfill();
    });
};

LazyMan.prototype.eat = function eat(food) {
    this.promise = this.promise.then(() => {
        console.log(`eat ${food}`);
    });
    return this;
};

LazyMan.prototype.sleep = function sleep(sec) {
    this.promise = this.promise.then(() => {
        return new Promise((fullfill, reject) => {
            setTimeout(() => {
                console.log(`wake up after ${sec}s`);
                fullfill();
            }, sec * 1000);
        });
    });
    return this;
};

LazyMan.prototype.sleepFirst = function sleepFirst(sec) {
    // NOTHING
}

LazyMan('glm').eat('apple').sleep(2).eat('banana').eat('peach');
```

这种方式我还没想好如何实现sleepFirst，如果有知道的朋友，欢迎留言。