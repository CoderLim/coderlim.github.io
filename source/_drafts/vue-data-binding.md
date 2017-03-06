---
title: vue数据绑定
description: 这个题目很宽泛，因为你不知道我将会写什么内容，是vue数据绑定的用法，还是vue如何实现数据绑定，其实这里会聊聊如何实现数据绑定。我第一次知道MVVM和数据绑定是在自学WPF的时候，那是一种很美妙的感觉，代码量变少了，可以将注意力放在功能实现上，而不用去考虑UI与Model的同步。
tags:
  - vue
  - 数据绑定
categories: 
  - 前端
  - vue 
---

>这个题目很宽泛，因为你不知道我将会写什么内容，是vue数据绑定的用法，还是vue如何实现数据绑定，其实这里会聊聊如何实现数据绑定。我第一次知道MVVM和数据绑定是在自学WPF的时候，那是一种很美妙的感觉，代码量变少了，可以将注意力放在功能实现上，而不用去考虑UI与Model的同步，同时MVVM解决了MVC(MassiveVC)问题。

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

我先来索索原理，接下来再上代码。尤大在实现数据变化更新UI时用到了动态收集依赖Dep（什么是依赖，一会儿会解释），在更新页面时就会重新收集一次依赖，什么样的算依赖呢，就是在更新UI的Watcher（下面称为UI-Watcher）回调函数执行中，被调用了getter方法的数据，就会与UI-Watcher相互依赖，当该数据调setter方法时，就会触发UI-Watcher的回调去更新UI。

### Dep

```js
let uid = 0;

export default class Dep {
    static target;

    constructor() {
        this.subs = [];
        this.id = uid++;
    }

    addSub(sub) {
        this.subs.push(sub);
    }

    removeSub(sub) {
        const idx = this.subs.indexOf(sub);
        this.subs.splice(idx, 1);
    }

    depend() {
        if (Dep.target) {
            Dep.target.addDep(this);
        }
    }

    notify() {
        const subs = this.subs.slice();
        for (let i = 0, l = subs.length; i < l; i++) {
            subs[i].run();
        }
    }
}

Dep.target = null

export function pushTarget(_target) {
    Dep.target = _target;
}

export function popTarget() {
    Dep.target = null;

```

### Watcher

```js
export default class Watcher {

    constructor(expOrFn, cb) {
        // 当前依赖
        this.deps = [];
        // 新关联的依赖
        this.newDeps = [];
        this.depIds = new Set();
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
     * Depend on all deps collected by this watcher.
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

```js
export default class Observer {
    constructor(value) {
        this.value = value;
        this.dep = new Dep();
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
            // 当Watcher调其自身的get时，将Dep.target赋值为该Watcher。
            // 
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
            // 
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
```

从上面的例子可以看出，在设置name时，触发了watcher的回调，而在设置sex时，没有触发。

## 总结

一直在探索怎样的表达方式更容易让人接受，希望以一种轻松诙谐的方式写博客，但是写着着就严肃了，毕竟时间有限，就不贫嘴了，我是个正经人（呵呵，这是我听过最搞笑的一句话）。
