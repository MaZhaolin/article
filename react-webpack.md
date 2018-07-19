---
title: 使用webpack构建react项目
date: 2018-07-18 21:41:25
tags: 
- react
- webpack
---



## 使用npm安装所需要的依赖

```bash
#初始化项目
npm init 
#安装react
cnpm i -S react react-dom 
#安装webpack
cnpm i -D webpack webpack-dev-server 
#使用sass
cnpm i -D node-sass sass-loader css-loader style-loader
#css加载相关
cnpm i -D postcss-loader postcss-flexbugs-fixes resolve-url-loader autoprefixer
#使用babel
cnpm i -D babel-core babel-loader babel-preset-es2015 babel-preset-react
#热更新
cnpm i -D html-webpack-plugin extract-text-webpack-plugin
```

<!--more-->

package.json

```json
{
  "scripts": {
    "start": "node config/dev-server.js",
    "build": "webpack ./src/index.js --config ./config/webpack.config.js"
  },
  "devDependencies": {
    "autoprefixer": "^9.0.0",
    "babel-core": "^6.26.3",
    "babel-loader": "^7.1.5",
    "babel-preset-es2015": "^6.24.1",
    "babel-preset-react": "^6.24.1",
    "css-loader": "^1.0.0",
    "extract-text-webpack-plugin": "^4.0.0-beta.0",
    "html-webpack-plugin": "^3.2.0",
    "node-sass": "^4.9.2",
    "postcss-flexbugs-fixes": "^3.3.1",
    "postcss-loader": "^2.1.6",
    "resolve-url-loader": "^2.3.0",
    "sass-loader": "^7.0.3",
    "style-loader": "^0.21.0",
    "webpack": "^4.16.1",
    "webpack-cli": "^3.0.8",
    "webpack-dev-server": "^3.1.4"
  },
  "dependencies": {
    "react": "^16.4.1",
    "react-dom": "^16.4.1"
  }
}
```

## 项目目录

dev-server.js

```javascript
'use strict'

const WebpackDevServer = require('webpack-dev-server');
const config = require('./webpack.config');
const webpack = require('webpack');
const path = require('path');
const compiler = webpack(config);

config.mode = 'development'
config.entry.app.unshift("webpack-dev-server/client?http://localhost:3000/");
config.plugins.pop()

const server = new WebpackDevServer(compiler, {
    contentBase: path.resolve(__dirname, '../dist'), //默认会以根文件夹提供本地服务器，这里指定文件夹
    historyApiFallback: true, //在开发单页应用时非常有用，它依赖于HTML5 history API，如果设置为true，所有的跳转将指向index.html
    port: 3000, //如果省略，默认8080
    publicPath: "/"
});
server.listen(3000, 'localhost', function (err) {
    if (err) throw err
})
```



webpack.config.js

```javascript
const path = require('path');
const webpack = require('webpack');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const ExtractTextPlugin = require('extract-text-webpack-plugin')
const autoprefixer = require('autoprefixer')
function resolve (dir) {
  return path.join(__dirname, '..', dir)
}
module.exports = {
  mode: 'production',
  entry: {
    app: [
      path.resolve(__dirname, '../src/index.js'),
    ]
  }, 
  output: {
    path: path.resolve(__dirname, '../dist'), // 输出的路径
    filename: 'app.[hash].js' // 打包后文件
  },
  resolve: {
    alias: {
      '@': resolve('src'),
    }
  },
  module: {
    rules: [{
      test: /\.(js|jsx)$/,
      loader: 'babel-loader',
      query: {
        presets: ['es2015']
      },
      exclude: /node_modules/
    },
    {
      test: /\.scss$/,
      use: ExtractTextPlugin.extract({
        fallback: "style-loader",
        use: [{
            loader: require.resolve('css-loader'),
            options: {
              importLoaders: 1,
              minimize: true,
              sourceMap: true,
            },
          },
          {
            loader: require.resolve('postcss-loader'),
            options: {
              ident: 'postcss', // https://webpack.js.org/guides/migrating/#complex-options
              plugins: () => [
                require('postcss-flexbugs-fixes'),
                autoprefixer({
                  browsers: [
                    '>1%',
                    'last 4 versions',
                    'Firefox ESR',
                    'not ie < 9', // React doesn't support IE8 anyway
                  ],
                  flexbox: 'no-2009',
                }),
              ],
            },
          },
          "resolve-url-loader",
          "sass-loader?sourceMap"
        ]
      })
    },{
      exclude: [/\.js$/, /\.html$/, /\.json$/,/\.scss$/],
      loader: require.resolve('file-loader'),
      options: {
        name: '[name].[hash:8].[ext]',
      },
    }]
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: path.resolve(__dirname, '../index.html'),
      inject: true
    }),
    new webpack.HotModuleReplacementPlugin(),
    new ExtractTextPlugin("app.[hash].css"),
    new webpack.LoaderOptionsPlugin({
      minimize: true,
      debug: false,
      options: {
        context: __dirname
      }
    })
  ]
}
```

