---
title: vue项目脚手架
date: 2018-06-01 16:42:02
categories: 前端
tags:
  - vue
---
#### 运行环境安装

- 安装node.js

```
# 1. 安装nvm管理Node
$curl -L -o- [http://build.sankuai.com/nvm/install](http://build.sankuai.com/nvm/install) | bash
＃ 或者
$curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.32.1/install.sh | bash

# 2. 安装node
$nvm install v8.5.0

# 3. 更换镜像源
$npm --registry=https://registry.npm.taobao.org
```

- 安装 vue-cli VUE的脚手架工具
```
npm install -g vue-cli
```

- 安装webpack
```
npm install webpack -g
```

#### vue-cli构建项目脚手架
```
$vue init webpack vue-learn
$npm install
$npm run dev
```
**测试**
浏览器输入http://localhost:8080即可看到vue页面

#### 指定项目eslint规约
参考:http://eslint.cn/docs/rules/
```
$vim .eslintrc.js

// add your custom rules here
rules: {
  'indent':[0, 'tab'],
  'no-tabs': 0,
  'no-mixed-spaces-and-tabs': 0,
  "semi": [0,';'],
  // 要求箭头函数的参数使用圆括号
  'arrow-parens': 0,
  // 强制箭头函数的箭头前后使用一致的空格
  "arrow-spacing": 0,
  // allow async-await
  'generator-star-spacing': 0,
  // allow debugger during development
  'no-debugger': process.env.NODE_ENV === 'production' ? 2 : 0,
  'comma-dangle':0,
  // 空行最多不超过三行
  "no-multiple-empty-lines": [0, {"max": 3}],
  // 变量声明就要使用
  "no-unused-vars": 2,
  // 调用变量前必须声明
  "no-use-before-define": 2,
  // 禁止使用行尾空格
  "no-trailing-spaces": 2,
  // 圆括号內使用一致的空格
  "space-in-parens": 0,
  "one-var": 0,
  "key-spacing": 0,
  'no-unused-vars': 0,
  'no-multi-spaces': 0
}
```

#### 项目文件结构
主要对src下的代码文件作展开
```
├── App.vue      // APP入口文件
├── api          // 接口调用工具文件夹
├── assets       // 静态资源
│   └── logo.png
├── components   // 组建文件夹
├── main.js      // 项目配置文件
├── router       // 路由文件
│   └── index.js
├── store        // vuex文件夹
└── utils        // 项目工具函数库
```

#### 路由规划
以实现一个在线答题的小应用
页面规划
1. home 主页面
2. item 试题页
3. score 得分页
路由配置如下:
```
routes: [
    {
      path: '/',
      name: 'home',
      component: r => require.ensure([], () => r(require('../pages/home')), 'home')
    },
    {
      path: '/item',
      component: r => require.ensure([], () => r(require('../pages/item')), 'item')
    },
    {
      path: '/score',
      component: r => require.ensure([], () => r(require('../pages/score')), 'score')
    },
    {
      path: '/404',
      component: r => require.ensure([], () => r(require('../pages/404')), '404')
    },
    {
      path: '*',
      redirect: '/404'
    }
  ]
```

#### 添加page，让项目运行起来
根据路由的组件加载路径，添加页面，如：
page.vue
```
<template>
  <div>home page</div>
</template>
```

依次添各个页面,将项目运行起来，在地址栏中输入路由访问各个页面

#### 移动端适配
接下来要解决的一个问题就是移动端适配问题，这个项目运行在移动端
移动端适配代码
```
( function (doc, win) {
  var docEl = doc.documentElement
  var resizeEvt = 'orientationchange' in window ? 'orientationchange' : 'resize'

  var recalc = function () {
    var clientWidth = docEl.clientWidth
    if (!clientWidth) return
    docEl.style.fontSize = 20 * (clientWidth / 320) + 'px'
  }

  if (!doc.addEventListener) return

  win.addEventListener(resizeEvt, recalc, false)
  doc.addEventListener('DOMContentLoaded', recalc, false)
})( document, window)
```