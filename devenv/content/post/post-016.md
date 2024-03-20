---
title: "Javascript Project Setup (Basic, with Webpack and with babel)"
date: 2022-05-03T08:42:41+05:30
publishdate: 2022-05-03
lastmod: 2022-05-03

tags: [setup]
categories: [setup]
---
##### Basic Configuration
```
$ mkdir my-project
$ cd my-project

$ npm init -y

$ mkdir src
$ touch src/index.js
$ echo "console.log('Hello Project.')" >> src/index.js
$ node src/index.js
```
Automate this by entering the following in package.json
```
{
  ...
  "scripts": {
    "start": "node src/index.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  ...
}
```
Now we can use `npm start` 

##### webpack setup
```
$ mkdir dist
$ cd dist
$ vim index.html
<!DOCTYPE html>
<html>
  <head>
    <title>Hello Webpack</title>
  </head>
  <body>
    <div>
      <h1>Hello Webpack</h1>
    </div>
    <script src="./bundle.js"></script>
  </body>
</html>
$ vim src/index.js and add console.log('Hello Webpack Proj');
```
We now have the following folder structure
```
- dist/
-- index.html
- src/
-- index.js
- package.json
```
Now install the dependencies
```
$ npm install --save-dev webpack webpack-dev-server webpack-cli
```
Now the folder structure will be
```
- dist/
-- index.html
- src/
-- index.js
- node_modules/
- package.json
```
Modify package.json 
```
{
  ...
  "scripts": {
    "start": "webpack serve --mode development",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  ...
}
```
Now we can use `npm start` and we will have webpack as the local web server to serve files in development mode. Navigage to dist/html to see its output.

Now we will see how to bundle source code files from src/ to dist/ folder.
Modify package json as shown below
```
{
  ...
  "scripts": {
    "start": "webpack serve --config ./webpack.config.js --mode development",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  ...
}
```
The script defines that you want to use the Webpack Dev Server with a configuration file called webpack.config.js. Let’s create the required webpack.config.js file in your project's root directory:
```
$ touch webpack.config.js
```
Now our folder structure will be
```
- dist
-- index.html
- node_modules
- src
-- index.js
- package.json
- webpack.config.js
```
Now finish the setup by providing the following configuration in webpack.config.js.
```
module.exports = {
  // 1
  entry: './src/index.js',
  // 2
  output: {
    path: '/dist',
    filename: 'bundle.js'
  },
  // 3
  devServer: {
    static: './dist'
  }
};
```
The Webpack configuration file gives the following instructions:

* (1) Use the src/index.js file as entry point to bundle it. If the src/index.js file imports other JavaScript files, bundle them as well.
* (2) The bundled source code files shall result in a bundle.js file in the /dist folder.
* (3) The /dist folder will be used to serve our application to the browser.

In order to be more correct about these paths across operating systems, we should resolve them properly

```
const path = require('path');

module.exports = {
  entry: path.resolve(__dirname, './src/index.js'),
  output: {
    path: path.resolve(__dirname, './dist'),
    filename: 'bundle.js',
  },
  devServer: {
    static: path.resolve(__dirname, './dist'),
  },
};
```
Now start the application with npm start and open the application in the browser.

##### Webpack with babel
Install the dependencies
```
$ npm install --save-dev @babel/core @babel/preset-env
```
Now install the webpack babel loader
```
$ npm install --save-dev babel-loader 
```
Now include the babel preset in package.json
```
{
  ...
  "babel": {
    "presets": [
      "@babel/preset-env"
    ]
  },
  ...
}
```
Now modify webpack.config.js to include babel
```
const path = require('path');

module.exports = {
  entry: path.resolve(__dirname, './src/index.js'),
  module: {
    rules: [
      {
        test: /\.(js)$/,
        exclude: /node_modules/,
        use: ['babel-loader']
      }
    ]
  },
  resolve: {
    extensions: ['*', '.js']
  },
  output: {
    path: path.resolve(__dirname, './dist'),
    filename: 'bundle.js',
  },
  devServer: {
    static: path.resolve(__dirname, './dist'),
  },
};
```
Now start the application again.  Nothing should have changed except for that you can use upcoming ECMAScript features for JavaScript now.

