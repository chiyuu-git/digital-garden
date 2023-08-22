---
{"dg-publish":true,"permalink":"/programming/font-end/framework/engineering/webpack/"}
---



# 项目构建

## 什么是项目构建

编译项目中的 js, sass, less

合并 js/css 等资源文件

压缩 js/css/html 等资源文件

JS 语法的检查

## 构建工具

webpack

+ http://webpack.github.io/ (英文官网)
+ http://guowenfh.github.io/2016/03/24/vue-webpack-01-base/(系列教程)
+ https://github.com/nimojs/webpack-book(webpack 入门指南)
+ http://www.jianshu.com/p/bb48898eded5(教程)

rollup

snowpack https://zhuanlan.zhihu.com/p/149351900

vite https://zhuanlan.zhihu.com/p/150083887

写一个自己的 vite snowpack 可不可？

# Webpack 基础

https://github.com/gwuhaolin/dive-into-webpack/

## 基本概念

webpack 本质上是一个打包工具，它会根据代码的内容解析模块依赖，帮助我们把多个模块的代码打包

webpack 会把我们项目中使用到的多个代码模块（可以是不同文件类型），打包构建成项目运行仅需要的几个静态文件

在 Webpack 看来, 前端的所有资源文件 (js/json/css/img/less/...) 都会作为模块处理，除了 html

它将根据模块的依赖关系进行静态分析，生成对应的静态资源

内置支持 commonJS、ES6、AMD 三种模块化规范

### 配置文件 (默认)

webpack.config.js : 是一个 node 模块，返回一个 json 格式的配置信息对象

入口 输出 loader plugin 等相关配置全都是在 webpack.config.js 内设置

### 顶级属性

entry：

ouput：

rules：loader

pulgin：

resolve：文件路径

### 入口

入口可以使用 `entry` 字段来进行配置，`webpack` 支持配置多个入口来进行构建

```js
module.exports = {
  entry: './src/index.js' 
}
```

```js
// 上述配置等同于
module.exports = {
  entry: {
    main: './src/index.js'
  }
}
```

```js
// 或者配置多个入口
module.exports = {
  entry: {
    foo: './src/page-foo.js',
    bar: './src/page-bar.js', 
    // ...
  }
}
```

```js
// 使用数组来对多个文件进行打包
module.exports = {
  entry: {
    main: [
      './src/foo.js',
      './src/bar.js'
    ]
  }
}...
```

### 输出

构建结果的文件名、路径等都是可以配置的，使用 `output` 字段

```js
module.exports = {
  // ...
  output: {
    // 设置输出的文件的位置
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js',
  },
}
```

```js
// 或者多个入口生成不同文件
module.exports = {
  entry: {
    foo: './src/foo.js',
    bar: './src/bar.js',
  },
  output: {
    filename: '[name].js',
    path: __dirname + '/dist',
  },
}
```

```js
// 路径中使用 hash，每次构建时会有一个不同 hash 值，避免发布新版本时线上使用浏览器缓存
module.exports = {
  // ...
  output: {
    filename: '[name].js',
    path: __dirname + '/dist/[hash]',
  },
}...
```

我们一开始直接使用 `webpack` 构建时，默认创建的输出内容就是 `./dist/main.js`

## Loader

### 概述

Webpack 本身只能加载 JS/JSON 模块，如果要加载其他类型的文件 (模块)，就需要使用对应的 loader 进行转换/加载

Loader 本身也是运行在 node.js 环境中的 JavaScript 模块

它本身是一个函数，接受源文件作为参数，返回转换的结果

loader 一般以 xxx-loader 的方式命名，xxx 代表了这个 loader 要做的转换功能，比如 css-loader

可以把 `loader` 理解为是一个转换器，负责把某种文件格式的内容转换成 webpack 可以支持打包的 **模块**

当我们需要使用不同的 `loader` 来解析处理不同类型的文件时，我们可以在 `module.rules` 字段下来配置相关的规则，例如使用 `Babel` 来处理 `.js` 文件

```js
module: {
  // ...
  rules: [
    {
      test: /\.jsx?/, // 匹配文件路径的正则表达式，通常我们都是匹配文件类型后缀
      include: [
        path.resolve(__dirname, 'src') // 指定哪些路径下的文件需要经过 loader 处理
      ],
      use: 'babel-loader', // 指定使用的 loader
    },
  ],
}...
```

### Loader 匹配规则

当我们需要配置 `loader` 时，都是在 `module.rules` 中添加新的配置项，在该字段中，每一项被视为一条匹配使用 `loader` 的规则

```js
module.exports = {
  // ...
  module: {
    rules: [ 
      {
        test: /\.jsx?/, // 条件
        include: [ 
          path.resolve(__dirname, 'src'),
        ], // 条件
        use: 'babel-loader', // 规则应用结果
      }, // 一个 object 即一条规则
      // ...
    ],
  },
}...
```

`loader` 的匹配规则中有两个最关键的因素：一个是匹配条件，一个是匹配规则后的应用

**注意**：有时部分 npm 包有自己的 css 文件，如果 css-loader 路径没有包含 node_modules 则无法解析相应的 css 文件，因此在遇到这部分 npm 包的时候，需要额外添加路径

### 规则条件配置

大多数情况下，配置 `loader` 的匹配条件时，只要使用 `test` 字段就好了，很多时候都只需要匹配文件后缀名来决定使用什么 `loader`，但也不排除在某些特殊场景下，我们需要配置比较复杂的匹配条件。webpack 的规则提供了多种配置形式…

```js
{ test: ... } 匹配特定条件
{ include: ... } 匹配特定路径
{ exclude: ... }排除特定路径
{ and: [...] }必须匹配数组中所有条件
{ or: [...] }匹配数组中任意一个条件
{ not: [...] } 排除匹配数组中所有条件…
```

上述的所谓条件的值可以是：

- 字符串：必须以提供的字符串开始，所以是字符串的话，这里我们需要提供 **绝对路径**
- 正则表达式：调用正则的 `test` 方法来判断匹配
- 函数：`(path) => boolean`，返回 `true` 表示匹配
- 数组：至少包含一个条件的数组
- 对象：匹配所有属性值的条件…

```js
rules: [
  {
    test: /\.jsx?/, // 正则
    include: [
      path.resolve(__dirname, 'src'), // 字符串，注意是绝对路径
    ], // 数组
    // ...
  },
  {
    test: {
      js: /\.js/,
      jsx: /\.jsx/,
    }, // 对象，不建议使用
    not: [
      (value) => { /* ... */ return true; }, // 函数，通常需要高度自定义时才会使用
    ],
  },
],...
```

### 使用 Loader 配置

`module.rules` 的匹配规则最重要的还是用于配置 `loader`，我们可以使用 `use` 字段

```js
rules: [
  {
    test: /\.less/,
    use: [
      'style-loader', // 直接使用字符串表示 loader
      {
        loader: 'css-loader',
        options: {
          importLoaders: 1
        },
      }, // 用对象表示 loader，可以传递 loader 配置等
      {
        loader: 'less-loader',
        options: {
          noIeCompat: true
        }, // 传递 loader 配置
      },
    ],
  },
],...
```

`use` 字段可以是一个数组，也可以是一个字符串或者表示 `loader` 的对象。如果只需要一个 `loader`，也可以这样：`use: { loader: 'babel-loader'`, `options: { ... } }`

### Loader 应用顺序

对于上面的 `less` 规则配置，一个 `style.less` 文件会途径 `less-loader`、`css-loader`、`style-loader` 处理，成为一个可以打包的模块。

`loader` 的应用顺序在配置多个 `loader` 一起工作时很重要，通常会使用在 CSS 配置上，除了 `style-loader` 和 `css-loader`，你可能还要配置 `less-loader` 然后再加个 `postcss` 的 `autoprefixer` 等。

上述从后到前的顺序是在同一个 `rule` 中进行的，那如果多个 `rule` 匹配了同一个模块文件，`loader` 的应用顺序又是怎样的呢？看一份这样的配置…

```js
rules: [
  {
    test: /\.js$/,
    exclude: /node_modules/,
    loader: "eslint-loader",
  },
  {
    test: /\.js$/,
    exclude: /node_modules/,
    loader: "babel-loader",
  },
],...
```

这样无法法保证 `eslint-loader` 在 `babel-loader` 应用前执行。`webpack` 在 `rules` 中提供了一个 `enforce` 的字段来配置当前 `rule` 的 `loader` 类型，没配置的话是普通类型，我们可以配置 `pre` 或 `post`，分别对应前置类型或后置类型的 `loader`…

所有的 `loader` **按照前置** -> **行** 内 -> **普通** -> **后置** 的顺序执行。所以当我们要确保 `eslint-loader` 在 `babel-loader` 之前执行时，可以如下添加 `enforce` 配置

```js
rules: [
  {
    enforce: 'pre', // 指定为前置类型
    test: /\.js$/,
    exclude: /node_modules/,
    loader: "eslint-loader",
  },
]...
```

当项目文件类型和应用的 `loader` 不是特别复杂的时候，通常建议把要应用的同一类型 `loader` 都写在同一个匹配规则中，这样更好维护和控制

## Plugin

### 概述

模块代码转换的工作由 `loader` 来处理，除此之外的其他任何工作都可以交由 `plugin` 来完成。

通过添加我们需要的 `plugin`，可以满足更多构建中特殊的需求。例如，要使用压缩 `JS` 代码的 `uglifyjs-webpack-plugin` 插件，只需在配置中通过 `plugins` 字段添加新的 `plugin` 即可

```js
const UglifyPlugin = require('uglifyjs-webpack-plugin')

module.exports = {
  plugins: [
    new UglifyPlugin()
  ],
}
```

`plugin` 理论上可以干涉 `webpack` 整个构建流程，可以在流程的每一个步骤中定制自己的构建需求

更多的插件可以在这里查找：[plugins in awesome-webpack](https://github.com/webpack-contrib/awesome-webpack#webpack-plugins)

### DefinePlugin

`DefinePlugin` 是 `webpack` 内置的插件，可以使用 `webpack.DefinePlugin` 直接获取

这个插件用于创建一些在编译时可以配置的全局常量，这些常量的值我们可以在 `webpack` 的配置中去指定，例如

```js
module.exports = {
  // ...
  plugins: [
    new webpack.DefinePlugin({
      PRODUCTION: JSON.stringify(true), // const PRODUCTION = true
      VERSION: JSON.stringify('5fa3b9'), // const VERSION = '5fa3b9'
      BROWSER_SUPPORTS_HTML5: true, // const BROWSER_SUPPORTS_HTML5 = 'true'
      TWO: '1+1', // const TWO = 1 + 1,
      CONSTANTS: {
        APP_VERSION: JSON.stringify('1.1.2') // const CONSTANTS = { APP_VERSION: '1.1.2' }
      }
    }),
  ],
}...
```

有了上面的配置，就可以在应用代码文件中，访问配置好的变量了，如：

```js
console.log("Running App version " + VERSION);

if(!BROWSER_SUPPORTS_HTML5) require("html5shiv");
```

上面配置的注释已经简单说明了这些配置的效果，这里再简述一下整个配置规则：

- 如果配置的值是字符串，那么整个字符串会被当成代码片段来执行，其结果作为最终变量的值，如上面的 `"1+1"`，最后的结果是 `2`
- 如果配置的值不是字符串，也不是一个对象字面量，那么该值会被转为一个字符串，如 `true`，最后的结果是 `'true'`
- 如果配置的是一个对象字面量，那么该对象的所有 `key` 会以同样的方式去定义

这样我们就可以理解为什么要使用 `JSON.stringify()` 了，因为 `JSON.stringify(true)` 的结果是 `'true'`，`JSON.stringify("5fa3b9")` 的结果是 `"5fa3b9"`。

社区中关于 `DefinePlugin` 使用得最多的方式是定义环境变量，例如 `PRODUCTION = true` 或者 `__DEV__ = true` 等。部分类库在开发环境时依赖这样的环境变量来给予开发者更多的开发调试反馈，例如 react 等。

建议使用 `process.env.NODE_ENV`: … 的方式来定义 `process.env.NODE_ENV`，而不是使用 `process: { env: { NODE_ENV: ... } }` 的方式，因为这样会覆盖掉 `process` 这个对象，可能会对其他代码造成影响…

### Copy-webpack-plugin

我们一般会把开发的所有源码和资源文件放在 `src/` 目录下，构建的时候产出一个 `build/` 目录，通常会直接拿 `build` 中的所有文件来发布。有些文件没经过 `webpack` 处理，但是我们希望它们也能出现在 `build` 目录下，这时就可以使用 `CopyWebpackPlugin` 来处理了…

```js
const CopyWebpackPlugin = require('copy-webpack-plugin')

module.exports = {
  // ...
  plugins: [
    new CopyWebpackPlugin([
      { from: 'src/file.txt', to: 'build/file.txt', }, // 顾名思义，from 配置来源，to 配置目标路径
      { from: 'src/*.ico', to: 'build/*.ico' }, // 配置项可以使用 glob
      // 可以配置很多项复制规则
    ]),
  ],
}...
```

https://juejin.im/post/59a3ec53f265da2490350edb

### Extract-text-webpack-plugin

我们用它来把依赖的 `CSS` 分离出来成为单独的文件。这里再看一下使用 `extract-text-webpack-plugin` 的配置

```js
const ExtractTextPlugin = require('extract-text-webpack-plugin')

module.exports = {
  // ...
  module: {
    rules: [
      {
        test: /\.css$/,
        // 因为这个插件需要干涉模块转换的内容，所以需要使用它对应的 loader
        use: ExtractTextPlugin.extract({ 
          fallback: 'style-loader',
          use: 'css-loader',
        }), 
      },
    ],
  },
  plugins: [
    // 引入插件，配置文件名，这里同样可以使用 [hash]
    new ExtractTextPlugin('index.css'),
  ],
}...
```

在上述的配置中，我们使用了 `index.css` 作为单独分离出来的文件名，但有的时候构建入口不止一个，`extract-text-webpack-plugin` 会为每一个入口创建单独分离的文件，因此最好这样配置

```js
// 这样确保在使用多个构建入口时，生成不同名称的文件
plugins: [
  new ExtractTextPlugin('[name].css'),
],
```

## 代码模块路径

webpack 中有一个很关键的模块 `enhanced-resolve` 就是处理依赖模块路径的解析的，这个模块可以说是 Node.js 那一套模块路径解析的增强版本，有很多可以自定义的解析配置

在 webpack 配置中，和模块路径解析相关的配置都在 `resolve` 字段下

```js
module.exports = {
  resolve: {
    // ...
  }
}
```

**一般步骤**

解析路径后，解析器将路径指向文件或者文件夹（目录）

  - 如果是文件，直接加载，根据 resolve.extensions 配置补充后缀名
  - 如果是文件夹，查找里面是否有 package.json 文件
    - 如果有，默认按照里面的 main 字段的文件名查找文件 （可以通过 `resolve.mainFields` 配置更改）
    - 如果没有，默认查找 index.js 文件（可以通过 `resolve.mainFiles` 配置更改）

### resolve.alias

alias，顾名思义，是指路径的别名。简单点说，就是用一个简单的别名来替换一个常用的或者复杂的文件路径。

原理：先替换，后解析。在引入模块时，先将模块路径中匹配 alias 中的 key 替换成对应的 value，再做查找

> 1. 替换掉的路径可以是相对路径，也可以是绝对路径。

假设我们有个 `utils` 模块极其常用，经常编写相对路径很麻烦，希望可以直接 `import 'utils'` 来引用，那么我们可以配置某个模块的别名，如

```js
alias: {
  utils: path.resolve(__dirname, 'src/utils') // 这里使用 path.resolve 和 __dirname 来获取绝对路径
}
```

上述的配置是模糊匹配，意味着只要模块路径中携带了 utils 就可以被替换掉，如：

```js
import 'utils/query.js' // 等同于 import '[项目绝对路径]/src/utils/query.js'
```

如果需要进行精确匹配可以使用 `$`：

```js
alias: {
  utils$: path.resolve(__dirname, 'src/utils') // 只会匹配 import 'utils'
}
```

### resolve.extensions

```js
extensions: ['.wasm', '.mjs', '.js', '.json', '.jsx'],
// 这里的顺序代表匹配后缀的优先级，例如对于 index.js 和 index.jsx，会优先选择 index.js
```

这个配置的作用是和文件后缀名有关的,这个配置可以定义在进行模块路径解析时，webpack 会尝试帮你补全那些后缀名来进行查找

### resolve.modules

查找声明依赖名的模块，默认搜索 node_modules 目录。一般我们不修改这个配置。

```js
resolve: {
  modules: ['node_modules']
},
```

### resolve.mainFields

在引用模块时，指明使用 package.json 中哪个字段指定的文件，默认是“main”

```js
resolve: {
  // 配置 target === "web" 或者 target === "webworker" 时 mainFields 默认值是：
  mainFields: ['browser', 'module', 'main'],

  // target 的值为其他时，mainFields 默认值为：
  mainFields: ["module", "main"],
}
```

因为通常情况下，模块的 package 都不会声明 browser 或 module 字段，所以便是使用 main 了。

### resolve.mainFiles

在目录中没有 package.json 时，指明使用该目录中哪个文件，默认是 index.js

```js
resolve: {
  mainFiles: ['index'], // 可以添加其他默认使用的文件名
}
```

./design/Cascader/ 解析为文件夹，有没有办法不加最后一个斜杠呢？

./design/Cascader 解析为文件

### 打包前后的路径

我们的 path.reslove 获取的是绝对路径，但是打包之后其实都放在一起去了

如果用 copyplugin 抽离出来，那又是怎么引用的呢？

## Webpack-dev-server

`webpack-dev-server` 是 `webpack` 官方提供的一个工具，可以基于当前的 `webpack` 构建配置快速启动一个静态服务。当 `mode` 为 `development` 时，会具备 `hot reload` 的功能，即当源码文件变化时，会即时更新当前页面，以便你看到最新的效果…

### 基础使用

`webpack-dev-server` 是一个 `npm package`，安装后在已经有 `webpack` 配置文件的项目目录下直接启动就可以

```
npm install webpack-dev-server -g
webpack-dev-server --mode development
```

`webpack-dev-server` 默认使用 `8080` 端口

package.json

```js
{
  // ...
  "scripts": {
    "start": "webpack-dev-server --mode development"
  }
}
```

### 配置

https://juejin.im/post/5ae9ae5e518825672f19b094#comment

在 webpack 的配置中，可以通过 `devServer` 字段来配置 `webpack-dev-server`，如端口设置、启动 `gzip` 压缩等，这里简单讲解几个常用的配置

+ `public` 字段用于指定静态服务的域名，默认是 `http://localhost:8080/` ，当你使用 `Nginx` 来做反向代理时，应该就需要使用该配置来指定 `Nginx` 配置使用的服务域名
+ `port` 字段用于指定静态服务的端口，如上，默认是 `8080`，通常情况下都不需要改动
+ `publicPath` 字段用于指定构建好的静态文件在浏览器中用什么路径去访问，默认是 `/`
  + 例如，对于一个构建好的文件 `bundle.js`，完整的访问路径是 `http://localhost:8080/bundle.js`，
  + 如果你配置了 `publicPath: 'assets/'`，那么上述 `bundle.js` 的完整访问路径就是 `http://localhost:8080/assets/bundle.js`。
  + 可以使用整个 `URL` 来作为 `publicPath` 的值，如 `publicPath: 'http://localhost:8080/assets/'`。
  + 如果你使用了 `HMR`，那么要设置 `publicPath` 就必须使用完整的 `URL`
  + 建议将 `devServer.publicPath` 和 `output.publicPath` 的值保持一致
+ `before` 和 `after` 配置用于在 `webpack-dev-server` 定义额外的中间件，如

  ```js
  before(app){
    app.get('/some/path', function(req, res) { // 当访问 /some/path 路径时，返回自定义的 json 数据
      res.json({ custom: 'response' })
    })
  }...
  
  ```

+ `before` 在 `webpack-dev-server` 静态资源中间件处理之前，可以用于拦截部分请求返回特定内容，或者实现简单的数据 `mock`。
+ `after` 在 `webpack-dev-server` 静态资源中间件处理之后，比较少用到，可以用于打印日志或者做一些额外处理…
+ `contentBase`，用于指定静态资源的位置，一般来说会把静态资源直接放在 dist 文件夹中，这样当 webpack 打包之后 index.html 可以直接引用，因此当在开发环境的时候为了维持 index.html 的相对路径不变，可以通过 `contentBase` 进行设置。

  ```js
  contentBase: './dist'
  ```

+ 这样无论是开发环境还是生产环境，index.html 引用的静态文件都只需写 `./static/` 即可

#### Proxy 字段

- `proxy` 用于配置 `webpack-dev-server` 将特定 `URL` 的请求代理到另外一台服务器上。当你有单独的后端开发服务器用于请求 API 时，这个配置相当有用
- dev-server 使用了非常强大的 [http-proxy-middleware](https://github.com/chimurai/http-proxy-middleware) 包。更多高级用法，请查阅其 [文档](https://github.com/chimurai/http-proxy-middleware#options)。

**基本使用**

- 在 `localhost:3000` 上有后端服务的话，你可以这样启用代理：
- **webpack.config.js**

  ```javascript
  module.exports = {
    //...
    devServer: {
      proxy: {
        '/api': 'http://localhost:3000'
      }
    }
  };
  ```

- 请求到 `/api/users` 现在会被代理到请求 `http://localhost:3000/api/users`。

**重写路径**

- 如果你不想始终传递 `/api` ，则需要重写路径：
- **webpack.config.js**

  ```javascript
  module.exports = {
    //...
    devServer: {
      proxy: {
        '/api': {
          target: 'http://localhost:3000',
          pathRewrite: {'^/api' : ''}
        }
      }
    }
  };
  ```

- 后端不需要再加上 ‘api’ ，但是前端请求还是要的，用作 **转发的** 标志

**代理多个路径**

- 如果你想要代理多个路径特定到同一个 target 下，你可以使用由一个或多个「具有 `context` 属性的对象」构成的数组：

  **webpack.config.js**

  ```javascript
  module.exports = {
    //...
    devServer: {
      proxy: [{
        context: ['/auth', '/api'],
        target: 'http://localhost:3000',
      }]
    }
  };
  ```

**配合 HTTPs**

- https://webpack.docschina.org/configuration/dev-server/#devserver-proxy

**代理根路径**

# Webpack 初始化

## prod.conf.js

- webpack.prod.conf.js 直接继承 webpack.base.conf.js，执行 `npm run build` 如图所示：
- *生成的 app.js 文件有 110K，对比我们的代码量来说（main.js 只有 233B，占空间 4K），这实在太大了！*
  **这时候我们需要的就是 webpack 的代码分离了：把代码分离到不同的 bundle 中，然后可以按需加载或并行加载这些文件。**
  配置 webpack.prod.conf.js 如下：

  ```js
  'use strict'
  const merge = require('webpack-merge');
  const baseWebpackConfig = require('./webpack.base.conf');
  
  const path = require('path');
  const webpack = require('webpack');
  const CleanWebpackPlugin = require('clean-webpack-plugin');
  const UglifyJSPlugin = require('uglifyjs-webpack-plugin');
  
  module.exports = merge(baseWebpackConfig, {
    // 模式
    mode: "production",
    // 调试工具
    devtool: '#source-map',
    // 输出
    output: {
      path: path.resolve(__dirname, '../dist'),
      filename: "js/[name].[chunkhash].js",
    },
    // 插件
    plugins: [
      new CleanWebpackPlugin(['dist', 'build'], {
        root: path.resolve(__dirname, '../'),
      }),
      new webpack.HashedModuleIdsPlugin(),
    ],
    // 代码分离相关
    optimization: {
      nodeEnv: 'production',
      minimizer: [new UglifyJSPlugin()],
      runtimeChunk: {
        name: 'manifest'
      },
      splitChunks: {
        minSize: 30000,
        minChunks: 1,
        maxAsyncRequests: 5,
        maxInitialRequests: 3,
        name: false,
        cacheGroups: {
          vendor: {
            test: /[\\/]node_modules[\\/]/,
            name: 'vendor',
            chunks: 'initial',
          }
        }
      }
    }
  });
  ```

- 再次编译，可以看到由原来一个 js 文件（app.js）变成了三个 js 文件（manifest.js、vendor.js 和 app.js），app.js 的大小也变成了一个合适的大小 1.31k

## 生产环境完善

## 图片加载优化

### 图片

- 打包图片出现的问题：
  - 大图无法打包到 entry.js 文件中，index.html 不在生成资源目录下。
  - 页面加载图片会在所在目录位置查找，导致页面加载图片时候大图路径无法找到
  - 解决办法：
  - 使用 publicPath : 'dist/js/' //设置为 index.html 提供资源的路径,设置完后找所有的资源都会去当前目录下找。
  - 将 index.html 放在 dist/js/也可以解决。
- 自动编译打包（指南→开发）
  - 利用 webpack 开发服务器工具: webpack-dev-server
  - 下载
    - npm install --save-dev webpack-dev-server
    - 下载完之后多出一个 webpack-dev-server 命令，也是内置一个微型服务器，自动读取 webpack 配置文件
    - 访问 localhost:8080，可以看到当前的项目
    - webpack-dev-server 默认服务于 **根目录** 下的 **index.html**
    - 根目录以配置文件为基准，所以 index.html 直接放在配置文件旁边，可以不设置 contenBase 属性，且文件名固定的
    - 热加载会自动寻找打包后的大图片，热加载生成的文件都在微型服务器的内存里，与本地文件无关
    - webpack-dev-server—open
  - webpack.config.js 配置

    ```js
    devServer: {
      contentBase: './dist'
    },
    ```

  - package.json 配置

    ```json
    "start": "webpack-dev-server --open"
    ```

- 使用 webpack 插件
  - 常用的插件
  - 使用 html-webpack-plugin 根据模板 html 生成引入 script 的页面
  - 使用 clean-webpack-plugin 清除 dist 文件夹
  - 下载

    ```npm
    npm install --save-dev  html-webpack-plugin clean-webpack-plugin
    ```

  - webpack 配置

    ```js
    const HtmlWebpackPlugin = require('html-webpack-plugin'); //自动生成html文件的插件
     const CleanWebpackPlugin = require('clean-webpack-plugin'); //清除之前打包的文件   
    plugins: [
      new HtmlWebpackPlugin({template: './index.html'}),
      new CleanWebpackPlugin(['dist']),
    ]
    ```

  - 创建页面: index.html

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
      <meta charset="UTF-8">
      <title>webpack test</title>
    </head>
    <body>
    <div id="app"></div>
    <!--打包文件将自动通过script标签注入到此处-->
    </body>
    </html>
    ```

### CSS Sprites

- 如果你使用的 `webpack 3.x` 版本，需要 `CSS Sprites` 的话，可以使用 `webpack-spritesmith` 或者 `sprite-webpack-plugin`。
- 我们以 `webpack-spritesmith` 为例，先安装依赖…

  ```js
  module: {
    loaders: [
      // ... 这里需要有处理图片的 loader，如 file-loader
    ]
  },
  resolve: {
    modules: [
      'node_modules', 
      'spritesmith-generated', // webpack-spritesmith 生成所需文件的目录
    ],
  },
  plugins: [
    new SpritesmithPlugin({
      src: {
        cwd: path.resolve(__dirname, 'src/ico'), // 多个图片所在的目录
        glob: '*.png' // 匹配图片的路径
      },
      target: {
        // 生成最终图片的路径
        image: path.resolve(__dirname, 'src/spritesmith-generated/sprite.png'), 
        // 生成所需 SASS/LESS/Stylus mixins 代码，我们使用 Stylus 预处理器做例子
        css: path.resolve(__dirname, 'src/spritesmith-generated/sprite.styl'), 
      },
      apiOptions: {
        cssImageRef: "~sprite.png"
      },
    }),
  ],...
  ```

- 在你需要的样式代码中引入 `sprite.styl` 后调用需要的 `mixins` 即可

  ```js
  @import '~sprite.styl'
  
  .close-button
      sprite($close)
  .open-button
      sprite($open)
  ```

- 如果你使用的是 `webpack 4.x`，你需要配合使用 `postcss` 和 `postcss-sprites`，才能实现 `CSS Sprites` 的相关构建

### 图片压缩

- 在一般的项目中，图片资源会占前端资源的很大一部分，既然代码都进行压缩了，占大头的图片就更不用说了
- 我们之前提及使用 `file-loader` 来处理图片文件，在此基础上，我们再添加一个 `image-webpack-loader` 来压缩图片文件。简单的配置如下…

  ```js
  module.exports = {
    // ...
    module: {
      rules: [
        {
          test: /.*\.(gif|png|jpe?g|svg|webp)$/i,
          use: [
            {
              loader: 'file-loader',
              options: {}
            },
            {
              loader: 'image-webpack-loader',
              options: {
                mozjpeg: { // 压缩 jpeg 的配置
                  progressive: true,
                  quality: 65
                },
                optipng: { // 使用 imagemin-optipng 压缩 png，enable: false 为关闭
                  enabled: false,
                },
                pngquant: { // 使用 imagemin-pngquant 压缩 png
                  quality: '65-90',
                  speed: 4
                },
                gifsicle: { // 压缩 gif 的配置
                  interlaced: false,
                },
                webp: { // 开启 webp，会把 jpg 和 png 图片压缩为 webp 格式
                  quality: 75
                },
            },
          ],
        },
      ],
    },
  }...
  ```

### 使用 DataURL

- 有的时候我们的项目中会有一些很小的图片，因为某些缘故并不想使用 `CSS Sprites` 的方式来处理（譬如小图片不多，因此引入 CSS Sprites 感觉麻烦），那么我们可以在 webpack 中使用 `url-loader` 来处理这些很小的图片…
- `url-loader` 和 `file-loader` 的功能类似，但是在处理文件的时候，可以通过配置指定一个大小，当文件小于这个配置值时，`url-loader` 会将其转换为一个 `base64` 编码的 `DataURL`

## 代码压缩

- `webpack 4.x` 版本运行时，`mode` 为 `production` 即会启动压缩 `JS` 代码的插件，而对于 `webpack` `3.x`，使用压缩 `JS` 代码插件的方式也已经介绍过了。在生产环境中，压缩 `JS` 代码基本是一个必不可少的步骤，这样可以大大减小 `JavaScript` 的体积，相关内容这里不再赘述。
- 除了 JS 代码之外，我们一般还需要 HTML 和 CSS 文件，这两种文件也都是可以压缩的，虽然不像 JS 的压缩那么彻底（替换掉长变量等），只能移除空格换行等无用字符，但也能在一定程度上减小文件大小。在 webpack 中的配置使用也不是特别麻烦，所以我们通常也会使用。
- 对于 HTML 文件，之前介绍的 `html-webpack-plugin` 插件可以帮助我们生成需要的 HTML 并对其进行压缩…

  ```js
  module.exports = {
    // ...
    plugins: [
      new HtmlWebpackPlugin({
        filename: 'index.html', // 配置输出文件名和路径
        template: 'assets/index.html', // 配置文件模板
        minify: { // 压缩 HTML 的配置
          minifyCSS: true, // 压缩 HTML 中出现的 CSS 代码
          minifyJS: true // 压缩 HTML 中出现的 JS 代码
        }
      }),
    ],
  }...
  ```

- 如上，使用 `minify` 字段配置就可以使用 `HTML` 压缩，这个插件是使用 `html-minifier` 来实现 `HTML` 代码压缩的，`minify` 下的配置项直接透传给 `html-minifier`，配置项参考 `html-minifier` 文档即可。
- 对于 CSS 文件，我们之前介绍过用来处理 CSS 文件的 `css-loader`，也提供了压缩 CSS 代码的功能：…

  ```js
  module.exports = {
    module: {
      rules: [
        // ...
        {
          test: /\.css/,
          include: [
            path.resolve(__dirname, 'src'),
          ],
          use: [
            'style-loader',
            {
              loader: 'css-loader',
              options: {
                minimize: true, // 使用 css 的压缩功能
              },
            },
          ],
        },
      ],
    }
  }...
  ```

- 在 `css-loader` 的选项中配置 `minimize` 字段为 `true` 来使用 `CSS` 压缩代码的功能。`css-loader` 是使用 `cssnano` 来压缩代码的，`minimize` 字段也可以配置为一个对象，来将相关配置传递给 `cssnano`…

### Babel-loader 的 Styling

[“The code generator has deoptimised the styling of [some file\] as it exceeds the max of ”100KB“” mean?](https://stackoverflow.com/questions/29576341/what-does-the-code-generator-has-deoptimised-the-styling-of-some-file-as-it-e)

```
loaders: [
    { test: /\.js$/, loader: 'babel', query: {compact: false} }
]
```

```
{
     test: /\.(js)$/,
     loader: 'babel-loader',
     options: {
          presets: ['es2015'],    // or whatever
          plugins: [require('babel-plugin-transform-class-properties')], // or whatever
          compact: true    // or false during development
     }
},
```

### `compact`

Type: `boolean | "auto"`

Default: `"auto"`

"auto" will set the value by evaluating `code.length > 500_000`

All optional newlines and whitespace will be omitted when generating code in compact mode.

## 分离代码文件

- 关于分离 CSS 文件这个主题，之前在介绍如何搭建基本的前端开发环境时有提及，在 `webpack` 中使用 `extract-text-webpack-plugin` 插件即可。
- 先简单解释一下为何要把 CSS 文件分离出来，而不是直接一起打包在 JS 中。最主要的原因是我们希望更好地利用缓存。
- 假设我们原本页面的静态资源都打包成一个 JS 文件，加载页面时虽然只需要加载一个 JS 文件，但是我们的代码一旦改变了，用户访问新的页面时就需要重新加载一个新的 JS 文件。有些情况下，我们只是单独修改了样式，这样也要重新加载整个应用的 JS 文件，相当不划算。
- 还有一种情况是我们有多个页面，它们都可以共用一部分样式（这是很常见的，CSS Reset、基础组件样式等基本都是跨页面通用），如果每个页面都单独打包一个 JS 文件，那么每次访问页面都会重复加载原本可以共享的那些 CSS 代码。如果分离开来，第二个页面就有了 CSS 文件的缓存，访问速度自然会加快。虽然对第一个页面来说多了一个请求，但是对随后的页面来说，缓存带来的速度提升相对更加可观…
- `3.x` 以前的版本是使用 `CommonsChunkPlugin` 来做代码分离的，而 `webpack 4.x` 则是把相关的功能包到了 `optimize.splitChunks` 中，直接使用该配置就可以实现代码分离。

  ```js
  module.exports = {
    // ... webpack 配置
  
    optimization: {
      splitChunks: {
        chunks: "all", // 所有的 chunks 代码公共的部分分离出来成为一个单独的文件
      },
    },
  }...
  ```

- 我们需要在 HTML 中引用两个构建出来的 JS 文件，并且 `commons.js` 需要在入口代码之前。下面是个简单的例子

  ```html
  <script src="commons.js" charset="utf-8"></script>
  <script src="entry.bundle.js" charset="utf-8"></script>
  ```

- 如果你使用了 `html-webpack-plugin`，那么对应需要的 JS 文件都会在 HTML 文件中正确引用，不用担心。如果没有使用，那么你需要从 `stats` 的 `entrypoints` 属性来获取入口应该引用哪些 JS 文件，可以参考 Node API 了解如何从 stats 中获取信息…

### **显式配置共享类库可以这么操作**

```js
  module.exports = {
    entry: {
      vendor: ["react", "lodash", "angular", ...], // 指定公共使用的第三方类库
    },
    optimization: {
      splitChunks: {
        cacheGroups: {
          vendor: {
            chunks: "initial",
            test: "vendor",
            name: "vendor", // 使用 vendor 入口作为公共部分
            enforce: true,
          },
        },
      },
    },
    // ... 其他配置
  }
  
  // 或者
  module.exports = {
    optimization: {
      splitChunks: {
        cacheGroups: {
          vendor: {
            test: /react|angluar|lodash/, // 直接使用 test 来做路径匹配
            chunks: "initial",
            name: "vendor",
            enforce: true,
          },
        },
      },
    },
  }
  
  // 或者
  module.exports = {
    optimization: {
      splitChunks: {
        cacheGroups: {
          vendor: {
            chunks: "initial",
            test: path.resolve(__dirname, "node_modules") // 路径在 node_modules 目录下的都作为公共部分
            name: "vendor", // 使用 vendor 入口作为公共部分
            enforce: true,
          },
        },
      },
    },
  }...
```

- 上述第一种做法是显示指定哪些类库作为公共部分，第二种做法实现的功能差不多，只是利用了 test 来做模块路径的匹配，第三种做法是把所有在 node_modules 下的模块，即作为依赖安装的，都作为公共部分。你可以针对项目情况，选择最合适的做法..

## 进一步控制 JS 大小

### 按需加载模块

- 在 webpack 的构建环境中，要按需加载代码模块很简单，遵循 ES 标准的动态加载语法 `dynamic-import` 来编写代码即可，`webpack` 会自动处理使用该语法编写的模块

  ```js
  // import 作为一个方法使用，传入模块名即可，返回一个 promise 来获取模块暴露的对象
  // 注释 webpackChunkName: "lodash" 可以用于指定 chunk 的名称，在输出文件时有用
  import(/* webpackChunkName: "lodash" */ 'lodash').then((_) => { 
    console.log(_.lash([1, 2, 3])) // 打印 3
  })...
  ```

- 注意一下，如果你使用了 `Babel` 的话，还需要 `Syntax Dynamic Import` 这个 `Babel` 插件来处理 `import()` 这种语法。
- 由于动态加载代码模块的语法依赖于 `promise`，对于低版本的浏览器，需要添加 `promise` 的 `polyfill` 后才能使用。
- 如上的代码，webpack 构建时会自动把 `lodash` 模块分离出来，并且在代码内部实现动态加载 `lodash` 的功能。动态加载代码时依赖于网络，其模块内容会异步返回，所以 import 方法是返回一个 `promise` 来获取动态加载的模块内容。
- `import` 后面的注释 `webpackChunkName: "lodash"` 用于告知 `webpack` 所要动态加载模块的名称。我们在 webpack 配置中添加一个 `output.chunkFilename` 的配置…

  ```js
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].[hash:8].js',
    chunkFilename: '[name].[hash:8].js' // 指定分离出来的代码文件的名称
  },...
  ```

- 这样就可以把分离出来的文件名称用 lodash 标识了
- 如果没有添加注释 `webpackChunkName: "lodash" 以及 output.chunkFilename` 配置，那么分离出来的文件名称会以简单数字的方式标识，不便于识别

### 完整代码

```js
  const path = require('path')
  const webpack = require('webpack')
  const HtmlWebpackPlugin = require('html-webpack-plugin')
  const ExtractTextPlugin = require('extract-text-webpack-plugin')
  
  module.exports = {
    entry: './src/index.js',
  
    output: {
      path: path.resolve(__dirname, 'dist'),
      filename: '[name].js',
    },
  
    module: {
      rules: [
        {
          test: /\.jsx?/,
          include: [
            path.resolve(__dirname, 'src'),
          ],
          use: 'babel-loader',
        },
        {
          test: /\.less$/,
          use: ExtractTextPlugin.extract({
            fallback: 'style-loader',
            use: [
              'css-loader',
              'postcss-loader',
              'less-loader',
            ],
          }),
        },
        {
          test: /\.(png|jpg|gif)$/,
          use: [
            {
              loader: 'url-loader',
              options: {
                limit: 8192
              },
            },
            {
              loader: 'image-webpack-loader',
              options: {
                mozjpeg: { // 压缩 jpeg 的配置
                  progressive: true,
                  quality: 65
                },
                optipng: { // 使用 imagemin-optipng 压缩 png，enable: false 为关闭
                  enabled: false,
                },
                pngquant: { // 使用 imagemin-pngquant 压缩 png
                  quality: '65-90',
                  speed: 4
                },
                gifsicle: { // 压缩 gif 的配置
                  interlaced: false,
                },
                webp: { // 开启 webp，会把 jpg 和 png 图片压缩为 webp 格式
                  quality: 75
                },
              },
            },
          ],
        },
      ],
    },
  
    optimization: {
      splitChunks: {
        cacheGroups: {
          vendor: {
            chunks: "initial",
            test: path.resolve(__dirname, "node_modules"), // 路径在 node_modules 目录下的都作为公共部分
            name: "vendor", // 使用 vendor 入口作为公共部分
            enforce: true,
          },
        },
      },
    },
  
    plugins: [
      new HtmlWebpackPlugin({
        filename: 'index.html', // 配置输出文件名和路径
        template: 'src/index.html', // 配置文件模板
        minify: { // 压缩 HTML 的配置
          minifyCSS: true, // 压缩 HTML 中出现的 CSS 代码
          minifyJS: true, // 压缩 HTML 中出现的 JS 代码
          removeComments: true,
        },
      }),
      new ExtractTextPlugin('[name].css'),
      new webpack.NamedModulesPlugin(),
      new webpack.HotModuleReplacementPlugin(),
    ],
  
    devServer: {
      hot: true
    }
  }
```

## 公共（common）环境优化

待续，前面的可以先整理好

# Advanced

## sourceMap

https://juejin.im/post/6844903971648372743

https://webpack.docschina.org/configuration/devtool/#devtool

通常，JavaScript 的解释器会告诉你，第几行第几列代码出错。但是，这对于转换后的代码毫无用处。举例来说，jQuery 1.9 压缩后只有 3 行，每行 3 万个字符，所有内部变量都改了名字。你看着报错信息，感到毫无头绪，根本不知道它所对应的原始位置。

这就是 Source map 想要解决的问题。

### 什么是 Source Map

简单说，Source map 就是一个信息文件，里面储存着位置信息。也就是说，转换后的代码的每一个位置，所对应的转换前的位置。

在 Developer Tools 的 Setting 设置中，确认选中 "Enable source maps"。

根据一定的编码规则，即可生成与源码对应的 sourcemap

有了它，出错的时候，浏览器控制台将直接显示原始代码出错的位置，而不是转换后的代码，点击出错信息将直接跳转到原始代码位置。

![img](/img/user/programming/font-end/framework/engineering/webpack/16f567c5b011992e.gif)

### Webpack 调试

点击控制台的 `Sources` 面板，源文件都在 `webpack://` 目录下，或者直接搜索文件，打开源文件后进行断点调试。

> 按需加载的路由，只有页面加载了，源文件才会在这个目录下显示。

### 配置

对于打包后的 `sourceMap`，[webpack提供多种类型的配置](https://webpack.docschina.org/configuration/devtool/#devtool)。

开发环境使用：

- eval：会把每个模块封装到 `eval` 里包裹起来执行，并且会在末尾追加 `map` 文件的地址。`map` 文件映射到转换后的代码 (可执行的 js 文件)，而不是映射到原始代码 (vue 文件)，所以不能正确的显示错误行数。
- eval-source-map： 和 `eval` 类似，为每个模块生成原始的 `sourceMap`，`map` 文件会以 `dataURL` 的形式添加到 js 中（类似于图片的 base64 形式）。原始的 `sourceMap` 可以正确提示错误行数。**可以减少网络请求**
- cheap-eval-source-map： 跟 `eval-source-map` 相同，唯一不同的就是增加了 `cheap`，`cheap` 是指忽略了列信息（绝大部分时候列信息对于错误提示没啥用，只需要提示行数就行）。
- **cheap-module-eval-source-map：** 与 `cheap-eval-source-map` 相同，但是包含了不同 `loader` 模块之间的 `sourceMap`。例如借助 `babel` 编译 `ES6`，如果生成不包含 `loader` 的 `sourcemap`，此时 `debug` 到的将是编译后的代码，而非原始代码。

生产环境使用：

- source-map： `map` 文件包含完整的原始代码，但是打包会很慢。打包后的 `js` 最后一行是 `map` 文件地址的注释
- hidden-source-map：与 `sourceMap` 相同，也生成 map 文件，但是打包后的 `js` 最后没有 `map` 文件地址的引用。浏览器不会主动去请求 `map` 文件，一般用于网站错误分析，需要让错误分析工具按名称匹配到 `map` 文件。
- nosources-source-map：生成的 `map` 文件不包含源码，但是会正确提示错误的行数。另外项目的目录结构和文件名称会暴露在 `Sources` 面板
- **cheap-module-source-map**

    ![img](/img/user/programming/font-end/framework/engineering/webpack/16f55fc2839952b5.gif)

特定环境用（针对一些第三方工具，**用的很少，暂不详细讨论**）：

- inline-source-map
- inline-cheap-source-map
- inline-cheap-module-source-map
- cheap-source-map

其实就是 `inline`,`cheap`,`module`,`source-map` 的自由组合。

- inline 是以 `dateURL` 的形式添加 `map`，不额外生成 `map` 文件
- cheap 是没有列信息
- module 是包含了 `loader` 的 `sourcemap`
- source-map 则是映射到源文件

| devtool                        | 构建速度 | 重新构建速度 | 生产环境 | 品质 (quality)          |
| :----------------------------- | :------- | :----------- | :------- | :--------------------- |
| (none)                         | 非常快速 | 非常快速     | yes      | 打包后的代码           |
| eval                           | 非常快速 | 非常快速     | no       | 生成后的代码           |
| eval-cheap-source-map          | 比较快   | 快速         | no       | 转换过的代码（仅限行） |
| eval-cheap-module-source-map   | 中等     | 快速         | no       | 原始源代码（仅限行）   |
| eval-source-map                | 慢       | 比较快       | no       | 原始源代码             |
|                                |          |              |          |                        |
| cheap-source-map               | 比较快   | 中等         | yes      | 转换过的代码（仅限行） |
| cheap-module-source-map        | 中等     | 比较慢       | yes      | 原始源代码（仅限行）   |
| inline-cheap-source-map        | 比较快   | 中等         | no       | 转换过的代码（仅限行） |
| inline-cheap-module-source-map | 中等     | 比较慢       | no       | 原始源代码（仅限行）   |
| inline-source-map              | 慢       | 慢           | no       | 原始源代码             |
|                                |          |              |          |                        |
| source-map                     | 慢       | 慢           | yes      | 原始源代码             |
| hidden-source-map              | 慢       | 慢           | yes      | 原始源代码             |
|                                |          |              |          |                        |
| nosources-source-map           | 慢       | 慢           | yes      | 无源代码内容           |

> 验证 devtool 名称时， 我们期望使用某种模式， 注意不要混淆 devtool 字符串的顺序， 模式是： `[inline-|hidden-|eval-][nosources-][cheap-[module-]]source-map`.

### Sourcemap 处理

事情都是具有两面性的，方便调试的同时，也将源码暴露在控制台，可能会导致代码泄露的安全问题。

虽然说前端代码是公开的，但是代码的压缩混淆也一定程度上提高了安全性。

如果无特殊需求，生产环境是需要关闭这个选项的，`vue-cli3` 直接配置 `productionSourceMap: false` 即可。或者不关闭但是在测试环境迁移到正式环境时删掉 `map` 文件。也可以通过服务器配置，特殊账号（调试专用）能访问到 map 文件，其他用户则不行。

如果需要控制台能正确提示报错的位置而不暴露源码，推荐用 `nosources-source-map` 模式，但是这个模式会暴露源码的目录结构与文件命名。一般测试环境用这个比较好，QA 测试出来的问题能正确提示错误，即使运维忘了删除 `sourceMap` 文件，也不会暴露源码。

### 本地 Sourcemap 调试

https://juejin.im/post/6844903971648372743

### 相关插件

[SourceMapDevToolPlugin](https://webpack.docschina.org/plugins/source-map-dev-tool-plugin/)

- 这个插件主要是实现了对 `sourceMap` 的更精细控制，比如打包之后的位置，以及修改 `js/css` 文件最后面对 `sourceMap` 的引用地址。 这里主要用到修改 `sourceMap` 引用地址的功能。
- 使用这个必须关闭 `devtool`

[filemanager-webpack-plugin](https://github.com/gregnb/filemanager-webpack-plugin)

- 这个插件的主要功能是打包之后文件的移动，删除，拷贝，压缩等功能，使用之前需要先安装: `npm install filemanager-webpack-plugin --save-dev`
- 注意： 这个插件有 bug，移动操作不支持模糊匹配，只能改成复制 + 删除

## Optimization

### providedExports

`boolean = true`

告知 webpack 去确定那些由模块提供的导出内容，为 `export * from ...` 生成更多高效的代码。

### usedExports

`boolean = true`

让 webpack 确认每个模块哪些导出被使用了。这取决于 [`optimization.providedExports`](https://webpack.docschina.org/configuration/optimization/#optimizationoccurrenceorder) 选项。

由 `optimization.usedExports` 收集的信息会被其它优化手段或者代码生成使用，比如未使用的导出内容不会被生成， 当所有的使用都适配，导出名称会被处理做单个标记字符。

在压缩工具中的无用代码清除会受益于该选项，而且能够去除未使用的导出内容。

### mangleExports

`boolean`

`optimization.mangleExports` 允许控制导出处理 (export mangling)。

默认 `optimization.mangleExports` 会在 `生产` [模式下](https://webpack.docschina.org/configuration/mode/) 启用而其它情况会被禁用。

### innerGraph

`boolean = true`

`optimization.innerGraph` 告知 webpack 是否对未使用的导出内容，实施内部图形分析 (inner graph analysis)。

# Tree Shaking

wiki：http://wiki.baidu.com/pages/viewpage.action?pageId=1215900715

[多入口ts失效](https://github.com/webpack/webpack/issues/4453)

[官网ts介绍](https://webpack.docschina.org/guides/tree-shaking/#clarifying-tree-shaking-and-sideeffects)

https://juejin.im/post/6844903998634328072#heading-0

https://segmentfault.com/a/1190000016767989

https://juejin.im/post/6844903687412776974

[你的tree shaking并没有什么卵用](https://github.com/frontend9/fe9-library/issues/81)

## 基础

### Tree Shaking 是什么

`tree shaking` ，通常用于描述移除 JavaScript 上下文中的未引用代码 (dead-code)。它依赖于 ES2015 模块语法的 [静态结构](http://exploringjs.com/es6/ch_modules.html#static-module-structure) 特性，例如 [`import`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import) 和 [`export`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/export)。这个术语和概念实际上是由 ES2015 模块打包工具 [rollup](https://github.com/rollup/rollup) 普及起来的。

webpack 2 正式版本内置支持 ES2015 模块（也叫做 `harmony modules`）和未使用模块检测能力。新的 webpack 4 正式版本扩展了此检测能力，通过 `package.json` 的 `"sideEffects"` 属性作为标记，向 compiler 提供提示，表明项目中的哪些文件是 "pure(纯正 ES2015 模块)"，由此可以安全地删除文件中未使用的部分。

### DCE

DCE，即 **死码消除**，编译器原理中，**死码消除**（Dead code elimination）是一种编译最优化技术，它的用途是移除对程序运行结果没有任何影响的代码。

### Chunk-splitting

类似的，也会受到影响

https://github.com/webpack/webpack/issues/4453#issuecomment-436128983

### [issues4453](https://github.com/webpack/webpack/issues/4453)

最新版本的貌似是修复了

```
"webpack": "^5.0.0-beta.24",
```

### 官网的相关介绍

其实 tree shaking 在仅使用 webpack 的时候是生效的，上面的很多配置其实在 proucntion 模式下都是默认开启的，所以官方的示例看起来好想很简单

https://webpack.docschina.org/guides/tree-shaking/

## Import 方式

https://juejin.im/post/6844903998634328072#heading-0

```js
// 导入并赋值给 JavaScript 对象，然后在下面的代码中被用到
// 这会被看作“活”代码，不会做 tree-shaking
import Stuff from './stuff';
doSomething(Stuff);
```

```js
// 导入并赋值给 JavaScript 对象，但在接下来的代码里没有用到
// 这就会被当做“死”代码，会被 tree-shaking
import Stuff from './stuff';
doSomething();
```

```js
// 导入但没有赋值给 JavaScript 对象，也没有在代码里用到
// 这会被当做“死”代码，会被 tree-shaking
import './stuff';
doSomething();
```

```js
// 导入整个库，但是没有赋值给 JavaScript 对象，也没有在代码里用到
// 非常奇怪，这竟然被当做“活”代码，因为 Webpack 对库的导入和本地代码导入的处理方式不同。
import 'my-lib';
doSomething();
```

```js
import * as _ from 'lodash-es'
_.debounce
...
```

在编写支持 tree-shaking 的代码时，导入方式非常重要。你应该避免将整个库导入到单个 JavaScript 对象中。当你这样做时，你是在告诉 Webpack 你需要整个库， Webpack 就不会摇它。

以流行的库 Lodash 为例。一次导入整个库是一个很大的错误，但是导入单个的模块要好得多。当然，Lodash 还需要其他的步骤来做 tree-shaking，但这是个很好的起点。

```js
// 全部导入 (不支持 tree-shaking)
import _ from 'lodash';
// 具名导入(支持 tree-shaking)
import { debounce } from 'lodash';
// 直接导入具体的模块 (支持 tree-shaking)
import debounce from 'lodash/lib/debounce';
```

## Side-effect 标记

"side effect(副作用)" 的定义是，在导入时会执行特殊行为的代码，而不是仅仅暴露一个 export 或多个 export。

举例说明，例如 polyfill，它影响全局作用域，并且通常不提供 export。

在一个纯粹的 ESM 模块世界中，很容易识别出哪些文件有 side effect。然而，我们的项目无法达到这种纯度，所以，此时有必要提示 webpack compiler 哪些代码是“纯粹部分”。

因此需要 side-effect 标记的文件是有限的

### 标记项目

通过 package.json 的 `"sideEffects"` 属性，来实现这种方式。

```js
{
  "name": "your-project",
  "sideEffects": false
}
```

如果所有代码都不包含 side effect，我们就可以简单地将该属性标记为 `false`，来告知 webpack，它可以安全地删除未用到的 export。

### 标记文件

如果你的代码确实有一些副作用，可以改为提供一个数组：

```js
{
  "name": "your-project",
  "sideEffects": [
    "./src/some-side-effectful-file.js"
  ]
}
```

数组方式支持相对路径、绝对路径和 glob 模式匹配相关文件。它在内部使用 [micromatch](https://github.com/micromatch/micromatch#matching-features)。

**注意：**所有导入文件都会受到 tree shaking 的影响。这意味着，如果在项目中使用类似 `css-loader` 并 import 一个 CSS 文件，则需要将其添加到 side effect 列表中，以免在生产模式中无意中将它删除：

```json
{
  "name": "your-project",
  "sideEffects": [
    "./src/some-side-effectful-file.js",
    "*.css"
  ]
}
```

最后，还可以在 [`module.rules` 配置选项](https://webpack.docschina.org/configuration/module/#module-rules) 中设置 `"sideEffects"`

这两个其实是没有意义的，因为我们要找出来的是有 side effect 的函数，将他们标记为 side-effect 才对，但是通常是 import 的文件，而不会是一个函数？

### 标记函数为 Side-effect-free

[`sideEffects`](https://webpack.docschina.org/configuration/optimization/#optimizationsideeffects) 和 [`usedExports`](https://webpack.docschina.org/configuration/optimization/#optimizationusedexports)（更多被认为是 tree shaking）是两种不同的优化方式。

**`sideEffects` 更为有效** 是因为它允许跳过整个模块/文件和整个文件子树。

`usedExports` 依赖于 [terser](https://github.com/terser-js/terser) 去检测语句中的副作用。它是一个 JavaScript 任务而且没有像 `sideEffects` 一样简单直接。而且它不能跳转子树/依赖由于细则中说副作用需要被评估。尽管导出函数能运作如常，但 React 框架的高阶函数（HOC）在这种情况下是会出问题的。[示例](https://webpack.docschina.org/guides/tree-shaking/#clarifying-tree-shaking-and-sideeffects)

Terser 尝试去解决以上的问题，但在很多情况下，它不太确定。这是因为在 JavaScript 这种动态语言中实在太难去确定。

但我们可以通过 `/*#__PURE__*/` 注释来帮忙 terser。它给一个语句标记为没有副作用。就这样一个简单的改变就能够使下面的代码被 tree-shake:

```js
var Button$1 = /*#__PURE__*/ withAppProvider()(Button);
```

### 标记函数调用 Side-effect-free

通过 `/*#__PURE__*/` 注释。它可以被放到函数调用之前，用来标记它们是无副作用的 (pure)。

传到函数中的入参是无法被刚才的注释所标记，需要单独每一个标记才可以。如果一个没被使用的变量定义的初始值被认为是无副作用的（pure），它会被标记为死代码，不会被执行且会被压缩工具清除掉。

启当 [`optimization.innerGraph`](https://webpack.docschina.org/configuration/optimization/#optimizationinnergraph) 被设置成 `true` 这个行为被会开

```js
/*#__PURE__*/ double(55);
```

### 示例

**index.js**

```javascript
import './configure';
export * from './types';
export * from './components';
```

**components/index.js**

```javascript
// ...
export { default as Breadcrumbs } from './Breadcrumbs';
export { default as Button, buttonFrom, buttonsFrom, } from './Button';
export { default as ButtonGroup } from './ButtonGroup';
// ...
```

**package.json**

```json
// ...
"sideEffects": [
  "**/*.css",
  "**/*.scss",
  "./index.js",
  "./configure.js"
],
// ...
```

- 导入它：导入并包含该模块，分析评估它并继续进行依赖分析
- 跳过它：不导入它，不分析评估它但会继续进行依赖分析
- 排除它：不导入它，不评估且不做依赖分析

以下是每个匹配到的资源的情况：

- `index.js`: 没有直接的导出被使用，但被标记为有副作用 -> 导入它
  - `configure.js`: 没有导出被使用，但被标记为有副作用 -> 导入它
  - `types/index.js`: 没有导出被使用，没有被标记为有副作用 -> 排除它
  - `components/index.js`: 没有导出被使用，没有被标记为有副作用，但重新导出的导出内容被使用了 -> 跳过它
    - `components/Breadcrumbs.js`: 没有导出被使用，没有被标记为有副作用 -> 排除它。这也会排除所有如同 `components/Breadcrumbs.css` 的依赖，尽管它们都被标记为有副作用。
    - `components/Button.js`: 直接的导出被使用，没有被标记为有副作用 -> 导入它
    - `components/Button.css`: 没有导出被使用，但被标记为有副作用 -> 导入它

在这种情况下，只有 4 个模块被导入到 bundle 中：

- `index.js`: 基本为空的
- `configure.js`
- `components/Button.js`
- `components/Button.css`

## uglifyJs

webpack 介绍：https://webpack.docschina.org/plugins/uglifyjs-webpack-plugin/

minify-options：https://github.com/mishoo/UglifyJS#minify-options

compress-options： https://github.com/mishoo/UglifyJS#compress-options

output:{

  beautify: true;

}

## Babel 配置

https://juejin.im/post/6844903998634328072#heading-0

据我所知，Babel 不支持将其他模块系统编译成 es2015 模块。但是，如果你是前端开发人员，那么你可能已经在使用 es2015 模块编写代码了，因为这是全面推荐的方法。

因此，为了让我们编译的代码使用 es2015 模块，我们需要做的就是告诉 babel 不要管它们。为了实现这一点，我们只需将以下内容添加到我们的 babel.config.js 中 (在本文中，你会看到我更喜欢 JavaScript 配置而不是 JSON 配置)：

```
// es2015 模块的基本 Babel 配置
const config = {
 presets: [
  [
   '[@babel/preset-env](http://twitter.com/babel/preset-env)',
   {
    modules: false
   }
  ]
 ]
};
```

把 `modules` 设置为 `false`，就是告诉 babel 不要编译模块代码。这会让 Babel 保留我们现有的 es2015 import/export 语句。

**划重点**：所有可需要 tree-shaking 的代码必须以这种方式编译。因此，如果你有要导入的库，则必须将这些库编译为 es2015 模块以便进行 tree-shaking 。如果它们被编译为 commonjs，那么它们就不能做 tree-shaking ，并且将会被打包进你的应用程序中。许多库支持部分导入，lodash 就是一个很好的例子，它本身是 commonjs 模块，但是它有一个 lodash-es 版本，用的是 es2015 模块。

此外，如果你在应用程序中使用内部库，也必须使用 es2015 模块编译。为了减少应用程序包的大小，必须将所有这些内部库修改为以这种方式编译。

## 库的 Tree Shaking

https://segmentfault.com/a/1190000016767989

如果你想要对库进行 tree shake，首先要记住的注意点还是前面所说的：使用 ES6 模块。然而许多库并不一定使用 ES6 模块，典型的例子比如 lodash 就是这样，看它的源代码，很明显它没有使用 ES6 模块。

有人创建了一个叫 lodash-es 的库，它改写 lodash 按 ES6 模块的形式导出。

很不幸，webpack 依旧没有对其 tree shake。根据 ECMAScript 规范，所有的子模块都需要去评估，因为它们可能有副作用。我推荐读一下 Sean Larking（他是 webpack 核心团队成员之一）在 Stack Overflow 上写的这篇很好的 [解释](https://stackoverflow.com/questions/49160752/what-does-webpack-4-expect-from-a-package-with-sideeffects-false)。库的作者可以在 package.json 文件里注明它的库没有副作用。打开 lodash 库的 package.json 文件，可以看到有 "sideEffects": false 的标注。那么这儿的问题是什么？

Webpack 默认忽略了 sideEffect 标注，改变此行为需要设置 optimization.sideEffects 为 true。你能手工设置它或通过设置 mode:"production" 模式也行。

现在 webpack 终于对 lodash 库“摇”起来了。

### 压缩输出结果

通过 `import` 和 `export` 语法，我们已经找出需要删除的“未引用代码 (dead code)”，然而，不仅仅是要找出，还要在 bundle 中删除它们。为此，我们需要将 `mode` 配置选项设置为 [`production`](https://webpack.docschina.org/configuration/mode/#mode-production)。

注意，也可以在命令行接口中使用 `--optimize-minimize` *标记，来启用* `TerserPlugin`。

在使用 tree shaking 时必须有 [ModuleConcatenationPlugin](https://webpack.docschina.org/plugins/module-concatenation-plugin) *的支持，您可以通过设置配置项* `mode: "production"` 以启用它。如果您没有如此做，请记得手动引入 [ModuleConcatenationPlugin](https://webpack.docschina.org/plugins/module-concatenation-plugin)。

你可以将应用程序想象成一棵树。绿色表示实际用到的 source code(源码) 和 library(库)，是树上活的树叶。灰色表示未引用代码，是秋天树上枯萎的树叶。为了除去死去的树叶，你必须摇动这棵树，使它们落下。

如果你对优化输出很感兴趣，请进入到下个指南，来了解 [生产环境](https://webpack.docschina.org/guides/production) 构建的详细细节。

### 测试框架罢工以及更多实践经验

https://juejin.im/post/6844903998634328072#heading-0

### 作用域级别的 Tree Shaking

[webpack-deep-scope-analysis-plugin](https://github.com/vincentdchan/webpack-deep-scope-analysis-plugin)

https://juejin.im/post/6844903669100445710

## 配置

开启 tree shaking：

- 使用 ES2015 模块语法（即 `import` 和 `export`）。
- 确保没有编译器将您的 ES2015 模块语法转换为 CommonJS

  > 这是现在常用的 @babel/preset-env 的默认行为，请设置 modules:false

- 使用 `mode` 为 `"production"` 开启 tree shaking，同时也会开启以下相关配置项
  - `optimization.usedExports ` 设置为 true，这意味着 Webpack 将识别出它认为没有被使用的代码，并在最初的打包步骤中给它做标记，`/* unused harmony export */`

### sideEffects 配置

在项目的 `package.json` 文件中，添加 `"sideEffects"` 属性。

> `true` 是默认值，如果不指定其他值的话。这意味着所有的文件都有副作用，也就是没有一个文件可以 tree-shaking
>
> 这句话其实挺奇怪的，难道我设置了一个文件，其他的就自动变成 false 了？还默认值是 true，貌似不设置也可以 tree shaking

全局 css 配置

```js
// 全局 CSS 副作用规则相关的 Webpack 配置
const config = {
 module: {
  rules: [
   {
    test: /regex/,
    use: [loaders],
    sideEffects: true
   }
  ]
 } 
};
```

# 并行构建 Parallel-webpack

# 多页面应用

https://juejin.im/post/5e5889c5e51d4526d059583f

https://segmentfault.com/a/1190000007301770

# 项目的打包与发布

打包：`npm run build`

发布 1: 使用静态服务器工具包

```js
npm install -g serve
serve dist
```

访问: <http://localhost:5000>

用动态 web 服务器 (tomcat)

使用静态服务器 https://www.cnblogs.com/greenteaone/p/10083129.html

# 项目联调

<https://blog.csdn.net/wo_921110/article/details/84980956>

# Lerna

多仓库的管理

shared 模式，参考 vue-next

# Monorepo

# Bundless

https://zhuanlan.zhihu.com/p/256482986
