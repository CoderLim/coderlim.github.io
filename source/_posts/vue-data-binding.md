---
title: 聊聊Vue的数据绑定
tags:
  - vue
  - 数据绑定
description: >-
  《最强大脑》水哥失败了，很不可思议，神一样的存在居然败给了余奕沛，为什么呢？我就不表达我的看法了。这篇博客的题目很宽泛，因为你不知道我将会写什么内容，是vue数据绑定的用法，还是vue如何实现数据绑定，其实这里是想简单聊聊如何实现数据绑定。我第一次知道MVVM和数据绑定是在自学WPF的时候，那是一种很美妙的感觉，代码量变少了，可以将注意力放在功能实现上，而不用去考虑UI与Model的同步。这篇文章仅适合熟悉vue和es6语法的童鞋，如果不满足这两个条件，请移步其他文章。
categories:
  - 前端
  - vue
date: 2017-03-07 10:14:46
---


![黑幕](/assets/images/2017/vue-data-binding1.png)
*没图片太单调，用啥图呢，思考片刻选择了这幅图*

## 数据绑定的本质

实现数据绑定的本质就是`Setter+change事件`，前者Setter用于在数据模型变化时更新UI，后者change事件，用于在UI变化时更新数据模型，来看个大某：

[Demo1](https://github.com/CoderLim/you-dont-know-mvvm/tree/master/demo1)

```js
// 数据模型
var data = {
    text: 'Hello World'
};

// UI元素
var input = document.getElementById('input'),
    label = document.getElementById('lbl');

// Setter
Object.defineProperty(data, 'text', {
    set: function(val) {
        label.innerText = val;
        input.value = val; // This won't trigger 'change' event.
    }
});

// change事件
input.addEventListener('change', function() {
    data.text = input.value;
});
```

从上面的代码可以看出，在**data.text**的Setter方法中更改了UI元素label和input的值，这样在**data.text**被赋值时，就会同步更新UI元素；同样在**change事件**中，input的值改变会同步更新**data.text**。

## Vue数据绑定

[Demo2](https://github.com/CoderLim/you-dont-know-mvvm/tree/master/demo2)

这里只聊聊如何实现当数据变化时更新页面，至于当UI内容变化如何更新数据，其实和上面的例子是一样的。那你肯定会问，难道**数据**变化时更新页面难道和上面的例子不一样吗？当然不一样，继续看（你不要凑字数好吗？好的）。

我先来说说原理，接下来再上代码。尤大在实现数据变化更新UI时用到了动态收集依赖Dep，下面让尤大来给我们解释解释：

**我**：什么是依赖Dep？<br>
**尤大**：每个数据data的属性property都对应一个依赖Dep，Dep最终会与Watcher相互关联，当property变化时，Dep就会通知watcher执行回调函数。<br>
**我**：那当数据变化时，触发的所有操作都在watcher的回调函数中吗？<br>
**尤大**：是的，与传统的data与watcher硬绑定不同，vue在data与watcher之间插入了Dep层，是为了解耦data与watcher，可以随心所欲的修改data与watcher的关联，这种实现方式可以成为依赖的动态收集。<br>
**我**：哦，原来Dep是用来解耦的，那Dep和Watcher是如何关联的？<br>
**尤大**：上面我已经提到了data的每个属性property都对应一个Dep，当property被访问时（也就是调用它的getter方法），它的Dep就会添加到当前的watcher。<br>
**我**：当前的watcher是什么意思？<br>
**尤大**：表急，耐心听。每个watcher都有一个expOrFn，当expOrFn执行前会把该watcher保存在Dep.target上，这个Dep.target就是当前的watcher。当expOrFn执行时如果访问了某个data的属性property，这个property的Dep就会与Dep.target关联起来。<br>
**我**：Dep.target是什么？<br>
**尤大**：Dep.target是Dep的静态属性。<br>
**我**：哦，好的，全明白了。<br>
**尤大**：全明白了吗？你应该忘了问Observer了吧。<br>
**我**：哦，是的，确实忘了，那Observer是什么？<br>
**尤大**：Observer的作用就是为data的property设置setter和getter。<br>

以下是精简后的代码，我把其他细节全部去掉了，只留下真正的核心代码。

### Dep(依赖)
 
dep是用来连接data与watcher的桥梁，每个data的属性都对应一个dep。

```js
let uid = 0;
export default class Dep {
    // 存放当前的watcher
    static target;
    constructor() {
        // 保存的是Watcher实例
        this.subs = [];
        // 唯一标示
        this.id = uid++;
    }
    // 添加watcher
    addSub(sub) {
        this.subs.push(sub);
    }
    // 移除watcher
    removeSub(sub) {
        const idx = this.subs.indexOf(sub);
        this.subs.splice(idx, 1);
    }
    depend() {
        if (Dep.target) {
            Dep.target.addDep(this);
        }
    }
    // 执行所有watcher的run方法
    notify() {
        const subs = this.subs.slice();
        for (let i = 0, l = subs.length; i < l; i++) {
            subs[i].run();
        }
    }
}

// 保存当前watcher
Dep.target = null

export function pushTarget(_target) {
    Dep.target = _target;
}

export function popTarget() {
    Dep.target = null;
```

### Watcher

watcher负责收集dep，保存dep变化时(也就是data属性变化时)调的callback。

```js
export default class Watcher {
    constructor(expOrFn, cb) {
        // 当前依赖
        this.deps = [];
        this.depIds = new Set();
        // 新关联的依赖
        this.newDeps = [];
        this.newDepIds = new Set();
        // 回调函数
        this.cb = cb;
        this.getter = expOrFn; // 只考虑expOrFn是函数的情况
        this.value = this.get(); // 这里value其实没用到
    }
    /**
     * 重新收集依赖
     */
    get() {
        // 将当前watcher放到Dep.target
        pushTarget(this);
        const value = this.getter(); // 这里value其实没用到
        popTarget();
        this.cleanupDeps();
        return value;
    }
    /**
     * 添加一个依赖
     */
    addDep(dep) {
        const id = dep.id;
        if (!this.newDepIds.has(id)) {
            this.newDepIds.add(id);
            this.newDeps.push(dep);
            if (!this.depIds.has(id)) {
                dep.addSub(this);
            }
        }
    }
    /**
     * 整理新依赖和旧依赖
     */
    cleanupDeps() {
        let i = this.deps.length
        while (i--) {
            const dep = this.deps[i]
            if (!this.newDepIds.has(dep.id)) {
                dep.removeSub(this)
            }
        }
        let tmp = this.depIds;
        this.depIds = this.newDepIds;
        this.newDepIds = tmp;
        this.newDepIds.clear();
        tmp = this.deps;
        this.deps = this.newDeps;
        this.newDeps = tmp;
        this.newDeps.length = 0;
    }
    /**
     * 当依赖有变化时就会执行这里
     */
    run() {
        const value = this.get(); // value其实没用到
        this.cb();
    }
    /**
     * 将依赖添加到Dep.target
     */
    depend() {
        let i = this.deps.length;
        while (i--) {
            this.deps[i].depend();
        }
    }
}
```

### Observer

observer使得data是可观察的，为data的每个属性都添加了dep，这里你会见到期盼已久的用`Object.defineProperty`定义的setter方法，在setter方法里通过dep.notify最终通知watcher执行回调方法。

```js
export default class Observer {
    constructor(value) {
        this.value = value;
        this.walk(value);
    }

    //遍历obj的所有属性，设置其setter和getter 
    walk(obj) {
        const keys = Object.keys(obj)
        for (let i = 0; i < keys.length; i++) {
            defineReactive(obj, keys[i], obj[keys[i]])
        }
    }
}

export function defineReactive(obj, key, val) {
    // obj中每个key都对应一个dep
    const dep = new Dep();
    // 将key对应的值保存在__key里，比如obj['name'] = 'glm'，则用obj['__name']来保存'glm'。
    obj[`__${key}`] = val;
    // 定制getter和setter
    Object.defineProperty(obj, key, {
        enumerable: true,
        configurable: true,
        get: function reactiveGetter() {
            // 当Watcher调其自身的get（️注意不是getter）方法时，将Dep.target赋值为该Watcher。
            if (Dep.target) {
                // dep.depend作用是让watcher（即Dep.target）与此dep互相引用
                // 伪代码是酱的：
                // dep.depend() {
                //  target.addDep(dep) { 
                //    dep.addSub(target);
                //  }
                // }
                dep.depend();
            }
            return this[`__${key}`];
        },
        set: function reactiveSetter(newVal) {
            this.__value = newVal;
            // data的属性变了，dep通知watcher该执行回调函数了
            dep.notify()
        }
    })
}
```

到现在把关键的三个类看完了，来看看如何使用它们：

```js
let obj = {
    name: 'glm',
    sex: 'male'
};
let observer = new Observer(obj);

const watcher = new Watcher(() => {
    console.log('-读取过的属性会与watcher建立联系-');
    console.log(obj.name);
    console.log('-----------------------------');
}, () => {
    console.log('Watcher回调函数');
});

console.log('设置name');
obj.name = 'zwr';
console.log('设置sex');
obj.sex = 'female';

// 打印
-读取过的属性会与watcher建立联系-
glm
-----------------------------
设置name
-读取过的属性会与watcher建立联系-
glm
-----------------------------
Watcher回调函数
设置sex
```

从上面的例子可以看出，在设置name时，触发了watcher的回调，而在设置sex时，没有触发。

[完整代码](https://github.com/CoderLim/you-dont-know-mvvm)

## 总结

到现在已经把相关的细节说完了，虽然我的阐述不多，但是已经加上了必要的注释。vue的数据绑定看似逻辑复杂，使用了动态收集依赖的思想，它的好处就是，dep与watcher的关系不是一成不变的，在更新页面的时候，只有被访问属性的dep才会与当前watcher建立联系，只有这个属性才能出发watcher的回调函数。如有有问题欢迎留言。
