---
title: webpack之es6和react.js
date: 2017-02-17 12:27:59
description: 昨天有朋友说让vsc支持react和es6太难配置了，我说直接用webpack吧，vsc仅仅用来敲代码就行了。之所以我这么说，因为我的vsc只是用来敲代码的，常用的git操作，依赖安装都是寄几在终端搞的。于是今天试了一下webpack，也踩了几脚坑，先让我擦干眼泪。
categories: 
  - 前端
  - webpack
tags:
  - 前端
  - webpack
  - es6
  - react
---

>昨天有[朋友](http://www.cnblogs.com/shannonliang/)说让vsc支持react和es6太难配置了，我说直接用webpack吧，vsc仅仅用来敲代码就行了。之所以我这么说，因为我的vsc只是用来敲代码的，常用的git操作，依赖安装都是寄几在终端搞的。于是今天试了一下webpack，也踩了几脚坑，先让我擦干眼泪。

[这里](https://github.com/CoderLim/let-us-reactjs/tree/master/hello-react)是这篇文章涉及到的源代码，如果需要运行可以直接跳到下面的最后一步<a href="#build">生成</a>。

## 如何配置

当你看到最终的是如何实现的时候，我其实是拒绝的，我宁可花长时间写的复杂点，但是很简单，绞尽脑汁也多写不了几行字。

1. 创建目录
```
cd ~/Desktop
mkdir hello-react
cd hello-react
```

2. 初始化npm
```
npm init -y
// 你也可以不用-y，那你就一步一步自己设置项目信息
```

3. 安装依赖
```json
npm install --save-dev webpack react react-dom
npm install --save-dev babel-core babel-preset-react babel-preset-es2015

// 或者直接修改`package.json`：

{
    ...,
    "devDependencies": {
        "babel-core": "^6.23.1",
        "babel-loader": "^6.3.2",
        "babel-preset-es2015": "^6.22.0",
        "babel-preset-react": "^6.23.0",
        "react": "^15.4.2",
        "react-dom": "^15.4.2",
        "webpack": "^2.2.1"
    },
    ...
}
```

4. 安装loader
```
npm install --save-dev babel-loader
```

5. 新建**.babelrc**
```json
{
    "presets": ["es2015", "react"]
}
```

6. 新建**webpack.config.js**
```js
var webpack = require('webpack');
module.exports = {
    entry: ['./app/main.js'],
    output: {
        path: __dirname + '/dist',
        filename: './bundle.js'
    },
    module: {
        loaders: [
          {
            test: /\.jsx?$/,
            loader: 'babel-loader'
          }
        ]
    }
}
```

到此配置就完成了，接下来写点代码

## app源码

1.  文件目录
```
app
  HelloComponent.jsx
  index.html
  main.js
dist
  bundle.js(自动生成的)
.babelrc
package.json
webpack.config.js
```

2. HelloComponent.jsx
```js
import React from 'react';
import ReactDOM from 'react-dom';

export default class HelloComponent extends React.Component {
    render () {
        return  (<h1>Hello beauty!</h1>);
    }
}
```

3. index.html
```html
<div id="app"></div>
<script src="../dist/bundle.js"></script>
```

4. main.js
```js
import React from 'react';
import ReactDOM from 'react-dom';
import Hello from './HelloComponent.jsx';

ReactDOM.render(<Hello/>, document.getElementById('app'));
```

## <a name="build">生成</a>

上面已经添加了源码，生成就简单了。

```
npm install (如果安装依赖不是通过package.json可以省略这步)
webpack
```

一条指令搞定，然后你就可以直接访问`index.html`了。

## 总结

这个教程算个入门引导，因为没有太难的问题，没实现webpack的各种插件、各种loader、热加载、自动刷新等等，自己去研究吧。

## 参考

[webpack][1]：webpack官方文档<br>
[Introduction to webpack with ES2015 and React][2]<br>
[How-to setup Webpack on an ES6 React Application with SASS?][3]

[1]:https://webpack.github.io/docs/usage.html "webpack官网"
[2]:http://blog.scalac.io/2016/03/03/introduction-to-webpack-with-es2015-and-react.html
[3]:https://www.jonathan-petitcolas.com/2015/05/15/howto-setup-webpack-on-es6-react-application-with-sass.html