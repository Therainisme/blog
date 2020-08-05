---
layout: default
title: Webpack | 安装、快速开始、管理资源、开发
description: 快速入门到入土
---

# 安装

安装最新版本或者是指定版本

```shell
npm install --save-dev webpack
npm install --save-dev webpack@<version>
```

安装CLI

```shell
npm install --save-dev webpack-cli
```

全局安装

```shell
npm install --global webpack
```

# 快速开始

新建一个文件夹，进入到里边，初始化npm，在里面安装webpack

```
npm init -y
npm install webpack webpack-cli --save-dev
```

该项目目录

```
  webpack-demo
  |- package.json
  |- /dist
    |- index.html
  |- /src
    |- index.js
```

通过npm安装我们需要的插件之后，可以通过`import $ from 'jquery';`在js文件中引入，然后可以通过webpack来打包

### 使用配置文件

在项目根目录新建一个webpack.config.js

```js
const path = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'index.js',
    path: path.resolve(__dirname, 'dist')
  }
};
```

Node 8.2+ 版本提供的 npx 命令来打包

`npx webpack --config webpack.config.js`

如果命令太长，可以在`package.json`文件中新建一个脚本

```js
{
  "name": "MagicWoodFrontEnd",
  "version": "1.0.0",
  "description": "",
  "private": "true",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
+   "build": "webpack"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "css-loader": "^4.2.0",
    "style-loader": "^1.2.1",
    "webpack": "^4.44.1",
    "webpack-cli": "^3.3.12"
  },
  "dependencies": {
    "lodash": "^4.17.19"
  }
}
```

然后就可以快乐的使用`npm run build`命令来替代之前的`npx`命令

# 管理资源

### 加载CSS文件

通过npm安装`style-loader`和`css-loader`

```shell
npm install --save-dev style-loader css-loader
```

向webpack.config,js添加

```js
  const path = require('path');

  module.exports = {
    entry: './src/index.js',
    output: {
      filename: 'index.js',
      path: path.resolve(__dirname, 'dist')
    },
+   module: {
+     rules: [
+       {
+         test: /\.css$/,
+         use: [
+           'style-loader',
+           'css-loader'
+         ]
+       }
+     ]
+   }
  };
```

> webpack 根据正则表达式，来确定应该查找哪些文件，并将其提供给指定的 loader。在这种情况下，以`.css`结尾的全部文件，都将被提供给`style-loader`和`css-loader`

可以在对应的js文件中通过import来引入某个css文件

src/style.css
```css
.hello {
  color: red;
}
```
src/index.js

```js
  import $ from 'jquery'
+ import './style.css';
```

### 加载图片

通过npm安装`file-loader`

```shell
npm install --save-dev file-loader
```

然后向webpack.config.js添加
```js
  const path = require('path');

  module.exports = {
    entry: './src/index.js',
    output: {
      filename: 'bundle.js',
      path: path.resolve(__dirname, 'dist')
    },
    module: {
      rules: [
        {
          test: /\.css$/,
          use: [
            'style-loader',
            'css-loader'
          ]
        },
+       {
+         test: /\.(png|svg|jpg|gif)$/,
+         use: [
+           'file-loader'
+         ]
+       }
      ]
    }
  };
```

两种引用方式

import

```js
import Icon from './icon.png';
```

css

```css
  .hello {
    color: red;
+   background: url('./icon.png');
  }
```

# 开发

webpack-dev-server 为你提供了一个简单的 web 服务器，并且能够实时重新加载(live reloading)。

```shell
npm install --save-dev webpack-dev-server
```

修改配置文件，告诉开发服务器(dev server)，在哪里查找文件：

```js
  module.exports = {
    ···
+   devServer: {
+     contentBase: './dist'
+   },
    ···
```

在package.json中scripts中添加一个脚本

```json
    "scripts": {
      "test": "echo \"Error: no test specified\" && exit 1",
      "watch": "webpack --watch",
+     "start": "webpack-dev-server --open",
      "build": "webpack"
    },
```

直接在命令行中`npm start`即可