---
layout: default
title: 引入Vue、Vuetify
description: 它很不错，但它的错大多都不会报错
---

> 声明，它很不好看

# 引入Vue

`npm install module_name -S`即`npm install module_name --save`写入dependencies

`npm install module_name -D`即`npm install module_name --save-dev`写入devDependencies

`npm install module_name -g`全局安装

### 初始化

进入工程根目录

```shell
npm init 
npm install -D webpack webpack-cli webpack-dev-server
```

webpack.config.js

```js
const path = require('path');

module.exports = {
  mode: 'development',
  entry: {
    index: './src/main.js',
  },
  output: {
    filename: '[name].bundle.js',
    path: path.resolve(__dirname, 'dist')
  },
  devServer: {
    contentBase: './dist'
  }
};
```

index.html添加

```html
<body>
  <script src="./index.bundle.js"></script>
</body>
```

### 集成Vue

`vue-loader` 先安装 `vue` `vue-loader` `vue-template-compiler`

标准的vue文件中，除了template包裹的html内容意外，还会有`<style>`包裹的css和`<script>`包裹的JS，因此还需要提供`css-loader``style-loader`用于处理样式，`babel-loader`用于处理JS。

```shell
npm install -P vue
npm install -D @babel/core @babel/preset-env vue-loader vue-template-compiler babel-loader css-loader  vue-style-loader css-loader stlye-loader
```

之后webpack.config.js增加一些

```js
const VueLoaderPlugin = require('vue-loader/lib/plugin');

module.exports = {

  plugins: [
    new VueLoaderPlugin()
  ],
  module: {
    rules: [
      {
        test: /\.vue$/,
        loader: 'vue-loader'
      },
      {
        test: /\.js$/,
        loader: 'babel-loader'
      },
      {
        test: /\.css$/,
        use: [
          'vue-style-loader',
          'stlye-loader'
          'css-loader'
        ]
      },
    ]
  }
};
```

主入口`main.js`和`App.vue`

```js
import Vue from 'vue';
import App from './App.vue';

new Vue({
  render: h => h(App),
}).$mount('#app');
```

```js
<style>
  .app {color: red}
</style>

<template>
  <div class="app">{{msg}}</div>
</template>
<script>
export default {
  data() {
    return {
      msg: 'hello'
    }
  }
}
</script>
```

`index.html`修改为

```html
<body id="app">
  <script src="./index.bundle.js"></script>
</body>
```
### 引入Vue-router

```shell
npm install -D vue-router
```

vue-router配置文件js

```js
import Vue from 'vue'
import VueRouter from 'vue-router'

import Home from 'projectSrc/views/Home.vue'
import Products from 'components/Product.vue'

Vue.use(VueRouter)

const router = new VueRouter({
    mode: 'history',
    routes: [
        ······
    ],
})

export default router
```

主入口`main.js`，import Vue-router 后

```js
new Vue({
    router,
    render: h => h(App),
}).$mount('#app');
```

# 引入Vuetify

> [直接去官网](https://vuetifyjs.com/zh-Hans/getting-started/quick-start/)

> 不过用[vue-cli](https://cli.vuejs.org/zh/guide/)脚手架来搭建更加方便快捷