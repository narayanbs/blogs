---
title: "React configuration with Webpack and Babel"
date: 2022-12-21T13:12:57+05:30
publishdate: 2022-12-21
lastmod: 2022-12-21
tags: []
categories: [javascript, react]
---

Create project folder and initialize it

```bash
$ mkdir webpackbabelproj && cd webpackbabelproj
$ npm init
$ npm install --save-dev eslint-config-prettier eslint-plugin-prettier
$ npm install --save-dev eslint-plugin-react
```

Add react libraries

```bash
$ npm install react react-dom
```

Update eslint_d and prettier configurations

```bash
$ touch .eslintrc.json (Add contents)
$ touch .prettierrc (Add contents)
```

Add Webpack and Babel libraries

```bash
$ npm install webpack webpack-cli webpack-dev-server --save-dev
$ npm install html-webpack-plugin --save-dev
$ npm install @babel/core babel-loader --save-dev
$ npm install @babel/preset-env @babel/preset-react --save-dev
```

Add webpack.config.js

```bash
$ touch webpack.config.js
```

Add the following contents

```bash
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: path.join(__dirname, 'src', 'index.js'),
  output: { path: path.resolve(__dirname, 'dist') },
  performance: {
    hints: false
  },
  mode: 'development',
  module: {
    rules: [
      {
        test: /\.?js$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['@babel/preset-env', '@babel/preset-react']
          }
        }
      }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: path.join(__dirname, 'src', 'index.html')
    })
  ]
};
```

Update package.json with dev and build scripts

```bash
"scripts": {
    "dev": "webpack serve",
    "build": "webpack",
  },
```

Now run the dev server or build the output in dist folder

```bash
$ npm run dev
  or
$ npm run build
```
