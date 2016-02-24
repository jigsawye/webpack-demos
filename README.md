翻譯自[ruanyf/webpack-demos](https://github.com/ruanyf/webpack-demos)。

此 repo 包含了一些 Webpack 簡單的 demo。

這些 demo 故意使用簡單且乾淨的方式來撰寫。你會發現可以不費吹灰之力來學習這些有力的工具。

## 如何使用

首先，全域安裝 [Webpack](https://www.npmjs.com/package/webpack) 及 [webpack-dev-server](https://www.npmjs.com/package/webpack-dev-server)。

```bash
$ npm i -g webpack webpack-dev-server
```

接著 clone 此 repo 並安裝 dependencies。

```bash
$ git clone git@github.com:jigsawye/webpack-demos.git
$ cd webpack-demos
$ npm install
```

現在可以在 repo 的 demo* 目錄中執行原始檔案。

```bash
$ cd demo01
$ webpack-dev-server
```

在你的瀏覽器打開 http://127.0.0.1:8080。

## 前言：什麼是 Webpack

Webpack 是一個如同 Grunt 與 Gulp 的前端建構（Build）系統。

可以像 Browserify 一般將它作為模組打包器（module bundler），但包含其他[更多功能](http://webpack.github.io/docs/what-is-webpack.html)。

```bash
$ browserify main.js > bundle.js
# 會同等於
$ webpack main.js bundle.js
```

設定檔為 `webpack.config.js`。

```javascript
// webpack.config.js
module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  }
};
```

有了 `webpack.config.js` 之後，你可以不帶任何參數呼叫 Webpack。

```bash
$ webpack
```

一些你應該知道的命令列（command-line）選項。

- `webpack` – 在開發（development）環境 build 一次
- `webpack -p` – 在上線（production）環境 build 一次（壓縮）
- `webpack --watch` – 監控並自動 build
- `webpack -d` – 包含 source maps
- `webpack --colors` – 讓它更美觀

若要產生準備上線的應用程式，你可以如下撰寫 package.json 檔案的 `scripts` 區塊。

```javascript
// package.json
{
  // ...
  "scripts": {
    "dev": "webpack-dev-server --devtool eval --progress --colors",
    "deploy": "NODE_ENV=production webpack -p"
  },
  // ...
}
```

## 目錄

1. [進入點檔案](#demo01-entry-file-source)
1. [多個進入點檔案](#demo02-multiple-entry-files-source)
1. [Babel-loader](#demo03-babel-loader-source)
1. [CSS-loader](#demo04-css-loader-source)
1. [Image loader](#demo05-image-loader-source)
1. [CSS Module](#demo06-css-module-source)
1. [UglifyJs Plugin](#demo07-uglifyjs-plugin-source)
1. [HTML Webpack Plugin 與 Open Browser Webpack Plugin](#demo08-html-webpack-plugin-and-open-browser-webpack-plugin-source)
1. [環境標記（flags）](#demo09-environment-flags-source)
1. [程式碼分割](#demo10-code-splitting-source)
1. [使用 bundle-loader 進行程式碼分割](#demo11-code-splitting-with-bundle-loader-source)
1. [通用 chunk](#demo12-common-chunk-source)
1. [第三方 chunk](#demo13-vendor-chunk-source)
1. [暴露（expose）全域變數](#demo14-exposing-global-variables-source)
1. [Hot Module Replacement](#demo15-hot-module-replacement-source)
1. [React router](#demo16-react-router-source)

<a name="demo01-entry-file-source">
## Demo01：進入點檔案（[source](https://github.com/jigsawye/webpack-demos/tree/master/demo01)）

進入點檔案會被 Webpack 讀取並 build 成 bundle.js。

例如，`main.js` 是個進入點檔案。

```javascript
// main.js
document.write('<h1>Hello World</h1>');
```

index.html

```html
<html>
  <body>
    <script type="text/javascript" src="bundle.js"></script>
  </body>
</html>
```

Webpack 會遵循 `webpack.config.js` 來 build `bundle.js`。

```javascript
// webpack.config.js
module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  }
};
```

執行伺服器，瀏覽 http://127.0.0.1:8080。

```bash
$ webpack-dev-server
```

<a name="demo02-multiple-entry-files-source">
## Demo02：多個進入點檔案（[source](https://github.com/jigsawye/webpack-demos/tree/master/demo02)）

多個進入點檔案是可行的。這在多頁的 app 相當的有用。

```javascript
// main1.js
document.write('<h1>Hello World</h1>');

// main2.js
document.write('<h2>Hello Webpack</h2>');
```

index.html

```html
<html>
  <body>
    <script src="bundle1.js"></script>
    <script src="bundle2.js"></script>
  </body>
</html>
```

webpack.config.js

```javascript
module.exports = {
  entry: {
    bundle1: './main1.js',
    bundle2: './main2.js'
  },
  output: {
    filename: '[name].js'
  }
};
```

<a name="demo03-babel-loader-source">
## Demo03：Babel-loader（[source](https://github.com/jigsawye/webpack-demos/tree/master/demo03)）

Loaders 是轉換 app 資源檔案的預處理器（[更多資訊](http://webpack.github.io/docs/using-loaders.html)）。例如，[Babel-loader](https://www.npmjs.com/package/babel-loader)可以轉換 JSX/ES6 檔案至 JS 檔案。官方文件有 [loaders](http://webpack.github.io/docs/list-of-loaders.html) 的完整列表。

`main.jsx` 是個 JSX 檔案。

```javascript
const React = require('react');
const ReactDOM = require('react-dom');

ReactDOM.render(
  <h1>Hello, world!</h1>,
  document.querySelector('#wrapper')
);
```

index.html

```html
<html>
  <body>
    <div id="wrapper"></div>
    <script src="bundle.js"></script>
  </body>
</html>
```

webpack.config.js

```javascript
module.exports = {
  entry: './main.jsx',
  output: {
    filename: 'bundle.js'
  },
  module: {
    loaders:[
      {
        test: /\.js[x]?$/,
        exclude: /node_modules/,
        loader: 'babel-loader?presets[]=es2015&presets[]=react'
      },
    ]
  }
};
```

在 `webpack.config.js` 中，`module.loaders` 部份被使用於指定 loaders。上方的程式片段使用了 `babel-loader`，它還同時需要 [babel-preset-es2015](https://www.npmjs.com/package/babel-preset-es2015) 與 [babel-preset-react](https://www.npmjs.com/package/babel-preset-react) 來轉譯 ES6 及 React。你也可以使用其他方式來設定 babel 的查詢（query）選項。

```javascript
module: {
  loaders: [
    {
      test: /\.jsx?$/,
      exclude: /node_modules/,
      loader: 'babel',
      query: {
        presets: ['es2015', 'react']
      }
    }
  ]
}
```

<a name="demo04-css-loader-source">
## Demo04：CSS-loader（[source](https://github.com/jigsawye/webpack-demos/tree/master/demo04)）

Webpack 讓你可以 在 JS 檔案中 require CSS，並使用 CSS-loader 來預處理 CSS 檔案。

main.js

```javascript
require('./app.css');
```

app.css

```css
body {
  background-color: blue;
}
```

index.html

```html
<html>
  <head>
    <script type="text/javascript" src="bundle.js"></script>
  </head>
  <body>
    <h1>Hello World</h1>
  </body>
</html>
```

webpack.config.js

```javascript
module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  },
  module: {
    loaders:[
      { test: /\.css$/, loader: 'style-loader!css-loader' },
    ]
  }
};
```

請注意，你需要使用兩種 loaders 來轉譯 CSS 檔案。首先使用 [CSS-loader](https://www.npmjs.com/package/css-loader) 來讀取 CSS 檔案，接著使用 [Style-loader](https://www.npmjs.com/package/style-loader) 寫入 Style 標籤至 HTML 頁面。不同的 loaders 使用驚嘆號（!）來連接。

在執行伺服器之後，`index.html` 會擁有行內樣式（inline style）。

```html
<head>
  <script type="text/javascript" src="bundle.js"></script>
  <style type="text/css">
    body {
      background-color: blue;
    }
  </style>
</head>
```

<a name="demo05-image-loader-source">
## Demo05：Image loader（[source](https://github.com/jigsawye/webpack-demos/tree/master/demo05)）

Webpack 也可以在 JS 檔案中 require 圖片。

main.js

```javascript
var img1 = document.createElement("img");
img1.src = require("./small.png");
document.body.appendChild(img1);

var img2 = document.createElement("img");
img2.src = require("./big.png");
document.body.appendChild(img2);
```

index.html

```html
<html>
  <body>
    <script type="text/javascript" src="bundle.js"></script>
  </body>
</html>
```

webpack.config.js

```javascript
module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  },
  module: {
    loaders:[
      { test: /\.(png|jpg)$/, loader: 'url-loader?limit=8192' }
    ]
  }
};
```

[url-loader](https://www.npmjs.com/package/url-loader) 會轉譯圖片檔案。如果圖片大小小於 8192 bytes，圖片就會被轉譯成 Data URL；否則它會被轉譯成普通的 URL。如你所見，問號（?）被用於傳遞參數至 loaders。

在執行伺服器之後，`small.png` 及 `big.png` 會變成下方的 URLs。

```html
<img src="data:image/png;base64,iVBOR...uQmCC">
<img src="4853ca667a2b8b8844eb2693ac1b2578.png">
```

<a name="demo06-css-module-source">
## Demo06：CSS Module（[source](https://github.com/jigsawye/webpack-demos/tree/master/demo06)）

`css-loader?modules`（查詢參數的 modules）會開啟 [CSS Modules](https://github.com/css-modules/css-modules) 功能。

這代表你 module 的 CSS 預設會是區域範圍（local scoped）的 CSS。你可以在選擇器及／或規則使用 `:global(...)` 切換成關閉。（[更多資訊](https://css-modules.github.io/webpack-demo/)）

index.html

```html
<html>
<body>
  <h1 class="h1">Hello World</h1>
  <h2 class="h2">Hello Webpack</h2>
  <div id="example"></div>
  <script src="./bundle.js"></script>
</body>
</html>
```

app.css

```css
.h1 {
  color:red;
}

:global(.h2) {
  color: blue;
}
```

main.jsx

```javascript
var React = require('react');
var ReactDOM = require('react-dom');
var style = require('./app.css');

ReactDOM.render(
  <div>
    <h1 className={style.h1}>Hello World</h1>
    <h2 className="h2">Hello Webpack</h2>
  </div>,
  document.getElementById('example')
);
```

webpack.config.js

```javascript
module.exports = {
  entry: './main.jsx',
  output: {
    filename: 'bundle.js'
  },
  module: {
    loaders:[
      {
        test: /\.js[x]?$/,
        exclude: /node_modules/,
        loader: 'babel-loader',
        query: {
          presets: ['es2015', 'react']
        }
      },
      {
        test: /\.css$/,
        loader: 'style-loader!css-loader?modules'
      }
    ]
  }
};
```

執行伺服器。

```bash
$ webpack-dev-server
```

瀏覽 http://127.0.0.1:8080 ，你會看到只有第二個 `h1` 是紅色，因為它的 CSS 為區域範圍，而兩個 `h2` 都是藍色，因為它的 CSS 是全域範圍（global scoped）。

<a name="demo07-uglifyjs-plugin-source">
## Demo07：UglifyJs Plugin（[source](https://github.com/jigsawye/webpack-demos/tree/master/demo07)）

Webpack 擁有 plugin 系統來擴增它的功能。例如，[UglifyJs Plugin](http://webpack.github.io/docs/list-of-plugins.html#uglifyjsplugin)會壓縮輸出的（`bundle.js`） JS 程式碼。

main.js

```javascript
var longVariableName = 'Hello';
longVariableName += ' World';
document.write('<h1>' + longVariableName + '</h1>');
```

index.html

```html
<html>
<body>
  <script src="bundle.js"></script>
</body>
</html>
```

webpack.config.js

```javascript
var webpack = require('webpack');
var uglifyJsPlugin = webpack.optimize.UglifyJsPlugin;
module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  },
  plugins: [
    new uglifyJsPlugin({
      compress: {
        warnings: false
      }
    })
  ]
};
```

在執行伺服器之後，`main.js` 會被壓縮成如下。

```javascript
var o="Hello";o+=" World",document.write("<h1>"+o+"</h1>")
```

<a name="demo08-html-webpack-plugin-and-open-browser-webpack-plugin-source">
## Demo08：HTML Webpack Plugin 及 Open Browser Webpack Plugin（[source](https://github.com/jigsawye/webpack-demos/tree/master/demo08)）

此 demo 為你展示如何載入第三方 plugins。

[html-webpack-plugin](https://github.com/ampedandwired/html-webpack-plugin) 會為你建立 `index.html`，而 [open-browser-webpack-plugin](https://github.com/baldore/open-browser-webpack-plugin) 會在 Webpack 載入後打開一個新的瀏覽器分頁。

main.js

```javascript
document.write('<h1>Hello World</h1>');
```

webpack.config.js

```javascript
var HtmlwebpackPlugin = require('html-webpack-plugin');
var OpenBrowserPlugin = require('open-browser-webpack-plugin');

module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  },
  plugins: [
    new HtmlwebpackPlugin({
      title: 'Webpack-demos'
    }),
    new OpenBrowserPlugin({
      url: 'http://localhost:8080'
    })
  ]
};
```

執行 `webpack-dev-server`。

```bash
$ webpack-dev-server
```

現在你不必手動撰寫 `index.html`，也不必自己打開瀏覽器。 Webpack 會為你做這些事。

<a name="demo09-environment-flags-source">
## Demo09: 環境標記（flags）（[source](https://github.com/jigsawye/webpack-demos/tree/master/demo09)）

你可以透過環境標記，讓某些程式碼只在開發環境時啟用。

main.js

```javascript
document.write('<h1>Hello World</h1>');

if (__DEV__) {
  document.write(new Date());
}
```

index.html

```html
<html>
<body>
  <script src="bundle.js"></script>
</body>
</html>
```

webpack.config.js

```javascript
var webpack = require('webpack');

var devFlagPlugin = new webpack.DefinePlugin({
  __DEV__: JSON.stringify(JSON.parse(process.env.DEBUG || 'false'))
});

module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  },
  plugins: [devFlagPlugin]
};
```

現在傳遞環境變數至 webpack。

```bash
# Linux & Mac
$ env DEBUG=true webpack-dev-server

# Windows
$ DEBUG=true webpack-dev-server
```

<a name="demo10-code-splitting-source">
## Demo10：程式碼分割（[source](https://github.com/jigsawye/webpack-demos/tree/master/demo10)）

對於大型的 web app 來說將所有程式碼放置於同個檔案是很沒效率的，Webpack 讓你可以將它們分割成幾個 chunk 。特別在某些程式碼區塊只需要在某些情況下 required，這些 chunk 可以按需求載入。

首先，使用 `require.ensure` 來定義分割點。（[官方文件](http://webpack.github.io/docs/code-splitting.html)）

```javascript
// main.js
require.ensure(['./a'], function(require) {
  var content = require('./a');
  document.open();
  document.write('<h1>' + content + '</h1>');
  document.close();
});
```

`require.ensure` 告知 Webpack `./a.js` 必須從 `bundle.js` 分離並 build 至單一的 chunk 檔案。

```javascript
// a.js
module.exports = 'Hello World';
```

現在 Webpack 會處理依賴、輸出檔案及執行時的相關事務。你不必放置任何冗餘的東西至 `index.html` 及 `webpack.config.js`。

```html
<html>
  <body>
    <script src="bundle.js"></script>
  </body>
</html>
```

webpack.config.js

```javascript
module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  }
};
```

執行伺服器。

```bash
$ webpack-dev-server
```

表面上，你不會感覺到任何差異。不過，Webpack 實際上 builds `main.js` 及 `a.js` 至不同的 chunks（`bundle.js` 及 `1.bundle.js`），並在有需求的時候從 `bundle.js` 載入 `1.bundle.js`。

<a name="demo11-code-splitting-with-bundle-loader-source">
## Demo11：使用 bundle-loader 進行程式碼分割（[source](https://github.com/jigsawye/webpack-demos/tree/master/demo11)）

另一個程式碼分割的方式是使用 [bundle-loader](https://www.npmjs.com/package/bundle-loader)。

```javascript
// main.js

// 現在 a.js 被請求，他會被 bundled 至另一個檔案
var load = require('bundle-loader!./a.js');

// 若要等待 a.js 直到他可用（並取得導出）
// 你需要為進行它非同步等待。
load(function(file) {
  document.open();
  document.write('<h1>' + file + '</h1>');
  document.close();
});
```

`require('bundle-loader!./a.js')` 告知 Webpack 從另一個 chunk 載入 `a.js`。

現在 Webpack 會 build `main.js` 至 `bundle.js`，`a.js` 至 `1.bundle.js`。

<a name="demo12-common-chunk-source">
## Demo12：通用 chunk（[source](https://github.com/jigsawye/webpack-demos/tree/master/demo12)）

當多個 scripts 擁有通用的 chunks，你可以使用 CommonsChunkPlugin 取出通用的部分至一個分離的檔案。

```javascript
// main1.jsx
var React = require('react');
var ReactDOM = require('react-dom');

ReactDOM.render(
  <h1>Hello World</h1>,
  document.getElementById('a')
);

// main2.jsx
var React = require('react');
var ReactDOM = require('react-dom');

ReactDOM.render(
  <h2>Hello Webpack</h2>,
  document.getElementById('b')
);
```

index.html

```html
<html>
  <body>
    <div id="a"></div>
    <div id="b"></div>
    <script src="init.js"></script>
    <script src="bundle1.js"></script>
    <script src="bundle2.js"></script>
  </body>
</html>
```

webpack.config.js

```javascript
var CommonsChunkPlugin = require("webpack/lib/optimize/CommonsChunkPlugin");
module.exports = {
  entry: {
    bundle1: './main1.jsx',
    bundle2: './main2.jsx'
  },
  output: {
    filename: '[name].js'
  },
  module: {
    loaders:[
      {
        test: /\.js[x]?$/,
        exclude: /node_modules/,
        loader: 'babel-loader',
        query: {
          presets: ['es2015', 'react']
        }
      },
    ]
  },
  plugins: [
    new CommonsChunkPlugin('init.js')
  ]
}
```

<a name="demo13-vendor-chunk-source">
## Demo13：第三方 chunk（[source](https://github.com/jigsawye/webpack-demos/tree/master/demo13)）

你也可以使用 CommonsChunkPlugin 從 script 取出第三方函式庫至分離的檔案。

main.js

```javascript
var $ = require('jquery');
$('h1').text('Hello World');
```

index.html

```html
<html>
  <body>
    <h1></h1>
    <script src="vendor.js"></script>
    <script src="bundle.js"></script>
  </body>
</html>
```

webpack.config.js

```javascript
var webpack = require('webpack');

module.exports = {
  entry: {
    app: './main.js',
    vendor: ['jquery'],
  },
  output: {
    filename: 'bundle.js'
  },
  plugins: [
    new webpack.optimize.CommonsChunkPlugin(/* chunkName= */'vendor', /* filename= */'vendor.js')
  ]
};
```

如果你想讓一個 module 在每個 module 作為可用的變數，像是讓 $ 與 jQuery 在每個 module 都可以使用，而不必撰寫 `require("jquery")`。你需要使用 `ProvidePlugin`（[官方文件](http://webpack.github.io/docs/shimming-modules.html)）。

```javascript
// main.js
$('h1').text('Hello World');


// webpack.config.js
var webpack = require('webpack');

module.exports = {
  entry: {
    app: './main.js'
  },
  output: {
    filename: 'bundle.js'
  },
  plugins: [
    new webpack.ProvidePlugin({
      $: "jquery",
      jQuery: "jquery",
      "window.jQuery": "jquery"
    })
  ]
};
```

<a name="demo14-exposing-global-variables-source">
## Demo14：暴露（expose）全域變數 ([source](https://github.com/jigsawye/webpack-demos/tree/master/demo14))

如果你想使用一些全域變數，且不想讓他們包含在 Webpack bundle 中，你可以啟用 `webpack.config.js` 中的 `externals` 區塊（[官方文件](http://webpack.github.io/docs/library-and-externals.html)）。

例如，我們有個 `data.js`。

```javascript
var data = 'Hello World';
```

我們可以暴露 `data` 作為全域變數。

```javascript
// webpack.config.js
module.exports = {
  entry: './main.jsx',
  output: {
    filename: 'bundle.js'
  },
  module: {
    loaders:[
      {
        test: /\.js[x]?$/,
        exclude: /node_modules/,
        loader: 'babel-loader',
        query: {
          presets: ['es2015', 'react']
        }
      },
    ]
  },
  externals: {
    // require('data') 在全域變數資料中是外部且可用的
    'data': 'data'
  }
};
```

現在，你在 script 中 require `data` 作為 module 變數。但是它實際上是全域變數。

```javascript
// main.jsx
var data = require('data');
var React = require('react');
var ReactDOM = require('react-dom');

ReactDOM.render(
  <h1>{data}</h1>,
  document.body
);
```

<a name="demo15-hot-module-replacement-source">
## Demo15：Hot Module Replacement（[source](https://github.com/jigsawye/webpack-demos/tree/master/demo15)）

[Hot Module Replacement](https://github.com/webpack/docs/wiki/hot-module-replacement-with-webpack)（HMR）在應用程式執行時進行交換、增加，或移除 modules，但**頁面不需重新載入**。

你有[兩種方式](http://webpack.github.io/docs/webpack-dev-server.html#hot-module-replacement)在 webpack-dev-server 啟用 Hot Module Replacement。

(1) 在 command line 指定 `--hot` 及 `--inline`

```bash
$ webpack-dev-server --hot --inline
```

這些選項的意義：

- `--hot`: 增加 HotModuleReplacementPlugin 並切換 server 至 hot 模式。
- `--inline`: 嵌入 webpack-dev-server runtime 至 bundle。
- `--hot --inline`: 還增加了 webpack/hot/dev-server 進入點。

(2) 修改 `webpack.config.js`。

- 增加 `new webpack.HotModuleReplacementPlugin()` 至 `plugins` 區塊
- 增加 `webpack/hot/dev-server` 及 `webpack-dev-server/client?http://localhost:8080` 至 `entry` 區塊

`webpack.config.js` 看起來會像下方那樣。

```javascript
var webpack = require('webpack');
var path = require('path');

module.exports = {
  entry: [
    'webpack/hot/dev-server',
    'webpack-dev-server/client?http://localhost:8080',
    './index.js'
  ],
  output: {
    filename: 'bundle.js',
    publicPath: '/static/'
  },
  plugins: [
    new webpack.HotModuleReplacementPlugin()
  ],
  module: {
    loaders: [{
      test: /\.jsx?$/,
      exclude: /node_modules/,
      loaders: ['babel-loader'],
      query: {
        presets: ['es2015', 'react']
      },
      include: path.join(__dirname, '.')
    }]
  }
};
```

現在執行 dev 伺服器。

```bash
$ webpack-dev-server
```

瀏覽 http://localhost:8080，你應該在瀏覽器中看見「Hello World」。

別關閉伺服器。開啟一個新的終端機編輯 `App.js`，並將「Hello World」修改成「Hello Webpack」。儲存並看看瀏覽器發生什麼事。

App.js

```javascript
import React, { Component } from 'react';

export default class App extends Component {
  render() {
    return (
      <h1>Hello World</h1>
    );
  }
}
```

index.js

```javascript
import React from 'react';
import ReactDOM = require('react-dom');
import App from './App';

ReactDOM.render(<App />, document.getElementById('root'));
```

index.html

```html
<html>
  <body>
    <div id='root'></div>
    <script src="/static/bundle.js"></script>
  </body>
</html>
```

<a name="demo16-react-router-source">
## Demo16：React router（[source](https://github.com/jigsawye/webpack-demos/tree/master/demo16)）

此 demo 使用 webpack 來 build [React-router](https://github.com/rackt/react-router/blob/0.13.x/docs/guides/overview.md) 的官方範例。

讓我們想像一個小型 app，它擁有 dashboard、inbox 及 calendar。

```
+---------------------------------------------------------+
| +---------+ +-------+ +--------+                        |
| |Dashboard| | Inbox | |Calendar|      Logged in as Jane |
| +---------+ +-------+ +--------+                        |
+---------------------------------------------------------+
|                                                         |
|                        Dashboard                        |
|                                                         |
|                                                         |
|   +---------------------+    +----------------------+   |
|   |                     |    |                      |   |
|   | +              +    |    +--------->            |   |
|   | |              |    |    |                      |   |
|   | |   +          |    |    +------------->        |   |
|   | |   |    +     |    |    |                      |   |
|   | |   |    |     |    |    |                      |   |
|   +-+---+----+-----+----+    +----------------------+   |
|                                                         |
+---------------------------------------------------------+
```

```bash
$ webpack-dev-server --history-api-fallback
```

## 相關連結

- [Webpack docs](http://webpack.github.io/docs/)
- [webpack-howto](https://github.com/petehunt/webpack-howto), by Pete Hunt
- [Diving into Webpack](https://web-design-weekly.com/2014/09/24/diving-webpack/), by Web Design Weekly
- [Webpack and React is awesome](http://www.christianalfoni.com/articles/2014_12_13_Webpack-and-react-is-awesome), by Christian Alfoni
- [Browserify vs Webpack](https://medium.com/@housecor/browserify-vs-webpack-b3d7ca08a0a9), by Cory House
- [React Webpack cookbook](https://christianalfoni.github.io/react-webpack-cookbook/index.html), by Christian Alfoni

## License

MIT
