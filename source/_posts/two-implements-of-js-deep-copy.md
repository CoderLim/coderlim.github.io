---
title: 两种javascript深拷贝的方法
date: 2015-08-12 17:21:16
description: 两种javascript深拷贝的方法
categories:
  - 前端
  - javascript
tags:
  - 前端
  - javascript
  - deepCopy
---

## 递归

```js
function deepCopy(obj) {
    if (!obj || typeof obj !== 'object') return obj;

    var target = isPlainObject(obj) ? {} : [],
        property, val;

    for (var key in obj) {
        val = obj[key];
        // 防止循环引用
        if (val === obj) continue;
        if (Array.isArray(val) || isPlainObject(val)) {
            target[key] = deepCopy(val);
        } else {
            target[key] = val;
        }
    }
    return target;
}
```

## 非递归

```js
function deepCopy(obj) {
    if (!obj || typeof obj !== 'object') return obj;

    var target = isPlainObject(obj) ? {} : [],
        auxlity = [
            [target, obj]
        ];

    while (auxlity.length) {
        var item = auxlity.shift(),
            to = item[0],
            from = item[1];
        for (var key in from) {
            var prop = from[key];
            // 防止循环引用
            if (from === prop) continue;
            if (Array.isArray(prop)) {
                to[key] = [];
                auxlity.push([to[key], prop]);
            } else if (isPlainObject(prop)) {
                to[key] = {};
                auxlity.push([to[key], prop]);
            } else {
                to[key] = prop;
            }
        }
    }
    return target;
}
```
