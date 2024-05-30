---
{"aliases":[],"tags":[],"review-dates":["2019-07-05","2020-06-12","2020-07-15"],"dg-publish":true,"date-created":"2022-09-03-Sat, 10:07:52 pm","date-modified":"2023-04-29-Sat, 8:09:49 pm","permalink":"/programming/front-end/framework/engineering/babel/","dgPassFrontmatter":true}
---


参考：

<https://juejin.im/post/5c19c5e0e51d4502a232c1c6#heading-0>

<https://juejin.im/post/5c21b584e51d4548ac6f6c99>

<https://babel.docschina.org/docs/en/7.0.0/v7-migration#babel-register>

<https://juejin.im/post/5c03a4d0f265da615e053612#heading-3>

<https://developers.weixin.qq.com/community/develop/article/doc/0008a6df750c20b698485c53e56413>

# Babel

## 概述

babel 来自 [巴别塔](https://link.zhihu.com/?target=https%3A//baike.baidu.com/item/%E5%B7%B4%E5%88%AB%E5%A1%94/67557%3Ffr%3Daladdin) 的典故：

当时人类联合起来兴建希望能通往天堂的高塔，为了阻止人类的计划，上帝让人类说不同的语言，使人类相互之间不能沟通，计划因此失败，人类自此各散东西。此事件，为世上出现不同语言和种族提供解释。这座塔就是巴别塔。

 这个巴别塔的典故很符合 babel 的转译器的定位。

读：baby able

### Babel 的作用

简单来说把 JavaScript 中 es2015/2016/2017 的新语法转化为 es5，让低端运行环境 (如浏览器和 node ) 能够认识并执行。本文以 babel 6.x 为基准进行讨论。最近 babel 出了 7.x，放在最后聊。

严格来说，babel 也可以转化为更低的规范。但以目前情况来说，es5 规范已经足以覆盖绝大部分浏览器，因此常规来说转到 es5 是一个安全且流行的做法。

## 使用 Babel

### 使用方法

总共存在三种方式：

- 使用单体文件 (standalone script)
- 命令行 (cli)
- 构建工具的插件 (webpack 的 babel-loader, rollup 的 rollup-plugin-babel)。

许多其他工具都有类似的单体配置文件：ESLint (`.eslintrc`)、Prettier (`.prettierrc`)。

后面两种比较常见。第二种多见于 package.json 中的 `scripts` 段落中的某条命令；

第三种就直接集成到构建工具中。

这三种方式只有入口不同而已，调用的 babel 内核，处理方式都是一样的，所以我们先不纠结入口的问题。

所有 Babel API [参数](https://www.babeljs.cn/docs/options) 都可以被配置。然而，如果该参数需要用到 JavaScript 代码，你可能需要使用 JavaScript 代码版的 [配置文件](https://www.babeljs.cn/docs/config-files)。

### 使用场景

[`babel.config.js`](https://www.babeljs.cn/docs/configuration#babelconfigjs) 文件

- 以编程的方式创建配置文件
- 编译 `node_modules` 目录下的模块

> 我们建议使用 [`babel.config.js`](https://www.babeljs.cn/docs/config-files#project-wide-configuration) 格式的配置文件。[Babel 本身使用的就是这种](https://github.com/babel/babel/blob/master/babel.config.js)。

[`.babelrc`](https://www.babeljs.cn/docs/configuration#babelrc) 文件

- 需要一个简单的并且只用于单个软件包的配置

### babel.config.js

在项目的根目录（`package.json` 文件所在目录）下创建一个名为 `babel.config.js` 的文件，并输入如下内容。

```js
module.exports = function (api) {
  api.cache(true);

  const presets = [ ... ];
  const plugins = [ ... ];

  return {
    presets,
    plugins
  };
}
```

请参阅 [`babel.config.js` 文档](https://www.babeljs.cn/docs/config-files#project-wide-configuration) 以了解更多关于配置参数的信息。

### .babelrc

在你的项目中创建名为 `.babelrc` 的文件，并输入以下内容

```js
{
  "presets": [...],
  "plugins": [...],
   "env":{
       "test":{},
       "production":{},
       "development":{}
   }
}
```

请参阅 [.babelrc 文档](https://www.babeljs.cn/docs/config-files#file-relative-configuration) 以了解更多关于配置参数的信息。

**package.json**

或者，还可以选择将 [`.babelrc`](https://www.babeljs.cn/docs/configuration#babelrc) 中的配置信息作为 `babel` 键（key）的值添加到 `package.json` 文件中，如下所示：

```js
{
  "name": "my-package",
  "version": "1.0.0",
  "babel": {
    "presets": [ ... ],
    "plugins": [ ... ],
  }
}
```

**.babelrc.js**

与 [`.babelrc`](https://www.babeljs.cn/docs/configuration#babelrc) 的配置相同，但你可以使用 JavaScript 编写

```js
const presets = [ ... ];
const plugins = [ ... ];

module.exports = { presets, plugins };
```

你还可以调用 Node.js 的任何 API，例如基于进程环境进行动态配置：

```js
const presets = [ ... ];
const plugins = [ ... ];

if (process.env["ENV"] === "prod") {
  plugins.push(...);
}

module.exports = { presets, plugins };
```

### 使用 CLI (`@babel/cli`)

```shell
babel --plugins @babel/plugin-transform-arrow-functions script.js
```

请参阅 [babel-cli 文档](https://www.babeljs.cn/docs/babel-cli) 以了解更多关于配置参数的信息。

```
npm install --save-dev @babel/core @babel/cli
```

编译命令

```
npx babel script.js
```

### 使用 API (`@babel/core`)

```js
require("@babel/core").transform("code", {
  plugins: ["@babel/plugin-transform-arrow-functions"]
});
```

请参阅 [babel-core 文档](https://www.babeljs.cn/docs/babel-core) 以了解更多关于配置参数的信息。

```js
const babel = require('@babel/core');

babel.transformFile("source.js", {presets:[],plugins:[]}, function(err, res) {
  console.log(res);
});
```

## 插件

### 运行方式和插件

babel 总共分为三个阶段：解析，转换，生成。

babel 本身不具有任何转化功能，它把转化的功能都分解到一个个 plugin 里面。因此当我们不配置任何插件时，经过 babel 的代码和输入是相同的。

**插件总共分为两种**：语法插件和转译插件 @@@

#### 语法插件

- 当我们添加 **语法插件** 之后，在解析这一步就使得 babel 能够解析更多的语法。

  > 顺带一提，babel 内部使用的解析类库叫做 babylon，并非 babel 自行开发
  >
  > 在 babel7 `babylon` 现在重命名为 `@babel/parser`，看起来是被收编了

- 举个简单的例子，当我们定义或者调用方法时，最后一个参数之后是不允许增加逗号的，如 `callFoo(param1, param2,)` 就是非法的。如果源码是这种写法，经过 babel 之后就会提示语法错误。
- 但最近的 JS 提案中已经允许了这种新的写法 (让代码 diff 更加清晰)。为了避免 babel 报错，就需要增加语法插件 `babel-plugin-syntax-trailing-function-commas`

#### 转译插件

- 当我们添加 **转译插件** 之后，在转换这一步把源码转换并输出。这也是我们使用 babel 最本质的需求。
- 比起语法插件，转译插件其实更好理解，比如箭头函数 `(a) => a` 就会转化为 `function (a) {return a}`。完成这个工作的插件叫做 `babel-plugin-transform-es2015-arrow-functions`。
- 同一类语法可能同时存在语法插件版本和转译插件版本。**如果我们使用了转译插件，就不用再使用语法插件了。**

### 配置文件

既然插件是 babel 的根本，那如何使用呢？总共分为 2 个步骤：

1. 使用 `npm install babel-plugin-xxx` 进行安装
2. 将插件的名字增加到配置文件中 (根目录下创建 .babelrc 或者 package.json 的 `babel` 里面，格式相同)

### 路径

如果插件再 npm 上，你可以输入插件的名称，babel 会自动检查它是否已经被安装到 `node_modules` 目录下

```js
{
  "plugins": ["babel-plugin-myPlugin"]
}
// 你还可以指定插件的相对/绝对路径
{
  "plugins": ["./node_modules/asdf/plugin"]
}
```

### 插件的短名称

如果插件名称的前缀为 `babel-plugin-`，你还可以使用它的短名称：

```js
{
  "plugins": [
    "myPlugin",
    "babel-plugin-myPlugin" // 两个插件实际是同一个
  ]
}
```

这也适用于带有冠名（scope）的插件：

```js
{
  "plugins": [
    "@org/babel-plugin-name",
    "@org/name" // 两个插件实际是同一个
  ]
}
```

### 插件开发

请参考 [babel-handbook](https://github.com/thejameskyle/babel-handbook) 学习如何创建自己的插件。

## 共同

### 顺序

插件的排列顺序很重要。

这意味着如果两个转换插件都将处理“程序（Program）”的某个代码片段，则将根据转换插件或 preset 的排列顺序依次执行。

- 插件在 Presets 前运行。
- 插件顺序从前往后排列。
- Preset 顺序是颠倒的（从后往前）。

**示例**

+ ```json
    {
      "plugins": ["transform-decorators-legacy", "transform-class-properties"]
    }
    ```

- 先执行 `transform-decorators-legacy` ，再执行 `transform-class-properties`。
- 重要的是，preset 的顺序是 **颠倒的**。如下设置：@@@

    ```json
    {
      "presets": ["es2015", "react", "stage-2"]
    }
    ```

- 将按如下顺序执行：`stage-2`、`react` 然后是 `es2015`。
- 这主要的是为了确保向后兼容，因为大多数用户将 "es2015" 排在 "stage-0" 之前。有关详细信息，请参阅 [notes on potential traversal API changes](https://github.com/babel/notes/blob/master/2016/2016-08/august-01.md#potential-api-changes-for-traversal)。

### 参数

插件和 preset 都可以接受参数，参数由插件名和参数对象组成一个 **数组**，可以在配置文件中设置。

如果不指定参数，下面这几种形式都是一样的：

```json
{
  "plugins": ["pluginA", ["pluginA"], ["pluginA", {}]]
}
```

要指定参数，请传递一个以参数名作为键（key）的对象。

```json
{
  "plugins": [
    [
      "transform-async-to-module-method",
      {
        "module": "bluebird",
        "method": "coroutine"
      }
    ]
  ]
}
```

preset 的设置参数的工作原理完全相同：

```json
{
  "presets": [
    [
      "env",
      {
        "loose": true,
        "modules": false
      }
    ]
  ]
}
```

## Preset

es2015 是一套规范，包含大概十几二十个转译插件。如果每次要开发者一个个添加并安装，配置文件很长不说，`npm install` 的时间也会很长，更不谈我们可能还要同时使用其他规范呢。

为了解决这个问题，babel 还提供了一组插件的集合。因为常用，所以不必重复定义 & 安装。

> 单点和套餐的差别，套餐省下了巨多的时间和配置的精力

Preset 可以理解为一套插件，所以相关的路径、短名称、顺序、参数都是和插件的使用是一致的

### 种类

1. 官方内容，目前包括 env, react, flow, minify 等。这里最重要的是 env，后面会详细介绍。

   - [@babel/preset-env](https://www.babeljs.cn/docs/babel-preset-env)
   - [@babel/preset-flow](https://www.babeljs.cn/docs/babel-preset-flow)
   - [@babel/preset-react](https://www.babeljs.cn/docs/babel-preset-react)
   - [@babel/preset-typescript](https://www.babeljs.cn/docs/babel-preset-typescript)

   > 许多由社区维护的 preset 可以从 [npm](https://www.npmjs.com/search?q=babel-preset) 上获取！

2. es201x, latest

   - 这些是已经纳入到标准规范的语法。例如 es2015 包含 `arrow-functions`，es2017 包含 `syntax-trailing-function-commas`。但因为 env 的出现，使得 es2016 和 es2017 都已经废弃。所以我们经常可以看到 es2015 被单独列出来，但极少看到其他两个。
   - **babel7**：淘汰 es201x 的目的是把选择环境的工作交给 env 自动进行，而不需要开发者投入精力。**凡是使用 es201x 的开发者，都应当使用 env 进行替换**。但这里的淘汰 (原文 deprecated) 并不是删除，只是不推荐使用了，不好说 babel 8 就真的删了。

3. stage-x，这里面包含的都是当年最新规范的草案，每年更新。这里面还细分为：

   - Stage 0 - 稻草人: 只是一个想法，经过 TC39 成员提出即可。
   - Stage 1 - 提案: 初步尝试。
   - Stage 2 - 初稿: 完成初步规范。
   - Stage 3 - 候选: 完成规范和浏览器初步实现。
   - Stage 4 - 完成: 将被添加到下一年度发布。
   - 例如 `syntax-dynamic-import` 就是 stage-2 的内容，`transform-object-rest-spread` 就是 stage-3 的内容。
   - 此外，低一级的 stage 会包含所有高级 stage 的内容，例如 stage-1 会包含 stage-2, stage-3 的所有内容。
   - stage-4 在下一年更新会直接放到 env 中，所以没有单独的 stage-4 可供使用。
   - **babel7**：与之相比，stage-x 就没那么好运了，它们直接被删了。这是因为 babel 团队认为为这些 “不稳定的草案” 花费精力去更新 preset 相当浪费。stage-x 虽然删除了，但它包含的插件并没有删除 (只是被更名了，可以看下面一节)，我们依然可以显式地声明这些插件来获得等价的效果。[完整列表](https://link.juejin.im?target=https%3A%2F%2Fgithub.com%2Fbabel%2Fbabel%2Ftree%2Fmaster%2Fpackages%2Fbabel-preset-stage-0%23babelpreset-stage-0)

   > 为了减少开发者替换配置文件的机械工作，babel 开发了一款 `babel-upgrade` 的 [工具](https://link.juejin.im?target=https%3A%2F%2Fgithub.com%2Fbabel%2Fbabel-upgrade)，它会检测 babel 配置中的 stage-x 并且替换成对应的 plugins。除此之外它还有其他功能，我们一会儿再详细看。(总之目的就是让你更加平滑地迁移到 babel 7)

   - 有关详细信息，请务必查看 [current TC39 proposals](https://github.com/tc39/proposals) 及其 [process document](https://tc39.github.io/process-document)。
   - TC39 各阶段的流程也在一些文章中有详细的解释，在 Yehuda Katz (@wycatz) 的 [thefeedbackloop.xyz](https://thefeedbackloop.xyz/) 网站上：[Stage 0 and 1](https://thefeedbackloop.xyz/tc39-a-process-sketch-stages-0-and-1/)、[Stage 2](https://thefeedbackloop.xyz/tc39-process-sketch-stage-2/)、[Stage 3](https://thefeedbackloop.xyz/tc39-process-sketch-stage-3/)

4. latest 是 env 的雏形，它是一个每年更新的 preset，目的是包含所有 es201x。但也是因为更加灵活的 env 的出现，已经废弃。

### Env (重点)

因为 env 最为常用也最重要，所以我们有必要重点关注。

env 的核心目的是通过配置得知目标环境的特点，然后只做必要的转换。例如目标浏览器支持 es2015，那么 es2015 这个 preset 其实是不需要的，于是代码就可以小一点 (一般转化后的代码总是更长)，构建时间也可以缩短一些。

如果不写任何配置项，env 等价于 latest，也等价于 es2015 + es2016 + es2017 三个相加 (不包含 stage-x 中的插件)。

env 包含的插件列表维护在 [这里](https://github.com/babel/babel-preset-env/blob/master/data/plugin-features.js)

### Modules

另外一个有用的配置项是 `modules`。它的取值可以是 `amd`, `umd`, `systemjs`, `commonjs` 和 `false`。这可以让 babel 以特定的模块化格式来输出代码。如果选择 `false` 就不进行模块化处理。

### [创建 Preset](https://www.babeljs.cn/docs/presets#创建-preset)

## 其他配套插件

以上讨论了 babel 的核心处理机制和配置方法等，不论任何入口调用 babel 都走这一套。但文章开头提的那一堆 `babel-*` 还是让人一头雾水。实际上这些 `babel-*` 大多是不同的入口 (方式) 来使用 babel，下面来简单介绍一下。

### Babel-cli

顾名思义，cli 就是命令行工具。安装了 `babel-cli` 就能够在命令行中使用 `babel` 命令来编译文件。

在开发 npm package 时经常会使用如下模式：

- 把 `babel-cli` 安装为 `devDependencies`
- 在 package.json 中添加 `scripts` (比如 `prepublish`)，使用 `babel` 命令编译文件
- `npm publish`

这样既可以使用较新规范的 JS 语法编写源码，同时又能支持旧版环境。因为项目可能不太大，用不到构建工具 (webpack 或者 rollup)，于是在发布之前用 `babel-cli` 进行处理。

### Babel-node

`babel-node` 是 `babel-cli` 的一部分，它不需要单独安装。

它的作用是在 node 环境中，直接运行 es2015 的代码，而不需要额外进行转码。例如我们有一个 js 文件以 es2015 的语法进行编写 (如使用了箭头函数)。我们可以直接使用 `babel-node es2015.js` 进行执行，而不用再进行转码了。

可以说：`babel-node` = `babel-polyfill` + `babel-register`。那这两位又是谁呢？

### Babel-register

babel-register 模块改写 `require` 命令，为它加上一个钩子。此后，每当使用 `require` 加载 `.js`、`.jsx`、`.es` 和 `.es6` 后缀名的文件，就会先用 babel 进行转码。

使用时，必须首先加载 `require('babel-register')`。

需要注意的是，babel-register 只会对 `require` 命令加载的文件转码，而 **不会对当前文件转码**。

另外，由于它是实时转码，所以 **只适合在开发环境使用**。

### Babel-polyfill

见实践

### Babel-runtime 和 Babel-plugin-transform-runtime (重点)

见实践

### Babel-loader

前面提过 babel 的三种使用方法，并且已经介绍过了 `babel-cli`。但一些大型的项目都会有构建工具 (如 webpack 或 rollup) 来进行代码构建和压缩 (uglify)。理论上来说，我们也可以对压缩后的代码进行 babel 处理，但那会非常慢。因此如果在 uglify 之前就加入 babel 处理，岂不完美？

所以就有了 babel 插入到构建工具内部这样的需求。以 (我还算熟悉的) webpack 为例，webpack 有 loader 的概念，因此就出现了 `babel-loader`。

和 `babel-cli` 一样，`babel-loader` 也会读取 .babelrc 或者 package.json 中的 `babel` 段作为自己的配置，之后的内核处理也是相同。唯一比 `babel-cli` 复杂的是，它需要和 webpack 交互，因此需要在 webpack 这边进行配置。比较常见的如下：

```js
module: {
  rules: [
    {
      test: /\.js$/,
      exclude: /(node_modules|bower_components)/,
      loader: 'babel-loader'
    }
  ]
}
```

如果想在这里传入 babel 的配置项，也可以把改成：

```js
// loader: 'babel-loader' 改成如下：
use: {
  loader: 'babel-loader',
  options: {
    // 配置项在这里
  }
}
```

这里的配置项优先级是最高的。但我认为放到单独的配置文件中更加清晰合理，可读性强一些。

### 小结一下

| 名称                                           | 作用                                                         | 备注                                                         |
| ---------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| babel-cli                                      | 允许命令行使用 babel 命令转译文件                            |                                                              |
| babel-node                                     | 允许命令行使用 babel-node 直接转译 + 执行 node 文件            | 随 `babel-cli` 一同安装 `babel-node` = `babel-polyfill` + `babel-register` |
| babel-register                                 | 改写 `require` 命令，为其加载的文件进行转码，不对当前文件转码 | 只适用于开发环境                                             |
| babel-polyfill                                 | 为所有 API 增加兼容方法                                      | 需要在所有代码之前 `require`，且体积比较大                   |
| babel-plugin-transform-runtime & babel-runtime | 把帮助类方法从每次使用前定义改为统一 `require`，精简代码     | `babel-runtime` 需要安装为依赖，而不是开发依赖               |
| babel-loader                                   | 使用 webpack 时作为一个 loader 在代码混淆之前进行代码转换    |                                                              |

那浏览器环境的优化呢？@@@

webpack 环境 babel-loader 都包含了写啥？babel-register 等等类似的作用的库在里面吗？@@@

## Polyfill 参考

### 箭头函数

在箭头函数（arrow function）中重新映射了 `this` 和 `arguments`

箭头函数与普通函数 **不同**。`arguments` 和 `this` 在箭头函数中 引用的是包裹箭头函数的 *外部函数* ，例如：

```js
const user = {
  firstName: "Sebastian",
  lastName: "McKenzie",
  getFullName: () => {
    // whoops! `this` doesn't actually reference `user` here
    return this.firstName + " " + this.lastName;
  },
  // use the method shorthand in objects
  getFullName2() {
    return this.firstName + " " + this.lastName;
  }
};
```

### This 重新映射到 Undefined

Babel 假设所有输入的代码都是 ES2015 模块。ES2015 模块默认是严格模式（strict mode），因此这意味着 浏览器中的顶级 `this` 不是 `window`，在 node 中也不是 `exports` 。

## Babel 7.x

最近 babel 发布了 7.0。因为上面部分都是针对 6.x 编写的，所以我们关注一下 7.0 带来的变化 (核心机制方面没有变化，插件，preset，解析转译生成这些都没有变化)

我只挑选一些和开发者关系比较大的列在这里，省略的多数是针对某一个 plugin 的改动。完整的列表可以参考 [官网](https://link.juejin.im?target=https%3A%2F%2Fbabeljs.io%2Fdocs%2Fen%2Fv7-migration)。

### Npm Package 名称的变化 (重点)

这是 babel 7 的一个重大变化，把所有 `babel-*` 重命名为 `@babel/*`，例如：

1. `babel-cli` 变成了 `@babel/cli`。
2. `babel-preset-env` 变成了 `@babel/preset-env`。进一步，还可以省略 `preset` 而简写为 `@babel/env`。
3. `babel-plugin-transform-arrow-functions` 变成了 `@babel/plugin-transform-arrow-functions`。和 `preset` 一样，`plugin` 也可以省略，于是简写为 `@babel/transform-arrow-functions`。

这个变化不单单应用于 package.json 的依赖中，包括 .babelrc 的配置 (`plugins`, `presets`) 也要这么写，为了保持一致。例如

```js
{
  "presets": [
    "env"
    "@babel/preset-env"
  ]
}
```

上文提过的 stage-x 被删除了，它包含的插件虽然保留，但也被重命名了。babel 团队希望更明显地区分已经位于规范中的插件 (如 es2015 的 `babel-plugin-transform-arrow-functions`) 和仅仅位于草案中的插件 (如 stage-0 的 `@babel/plugin-proposal-function-bind`)。方式就是在名字中增加 `proposal`，所有包含在 stage-x 的转译插件都使用了这个前缀，语法插件不在其列。

最后，如果插件名称中包含了规范名称 (`-es2015-`, `-es3-` 之类的)，一律删除。例如 `babel-plugin-transform-es2015-classes` 变成了 `@babel/plugin-transform-classes`。(这个插件我自己没有单独用过，惭愧)

### 不再支持低版本 Node

babel 7.0 开始不再支持 nodejs 0.10, 0.12, 4, 5 这四个版本，相当于要求 nodejs >= 6 (当前 nodejs LTS 是 8，要求也不算太过分吧)。

这里的不再支持，指的是在这些低版本 node 环境中不能使用 babel 转译代码，但 babel 转译后的代码依然能在这些环境上运行，这点不要混淆。

### Only 和 Ignore 匹配规则的变化

在 babel 6 时，`ignore` 选项如果包含 `*.foo.js`，实际上的含义 (转化为 glob) 是 `./**/*.foo.js`，也就是当前目录 **包括子目录** 的所有 `foo.js` 结尾的文件。这可能和开发者常规的认识有悖。

于是在 babel 7，相同的表达式 `*.foo.js` 只作用于当前目录，不作用于子目录。如果依然想作用于子目录的，就要按照 glob 的完整规范书写为 `./**/*.foo.js` 才可以。`only` 也是相同。

这个规则变化只作用于通配符，不作用于路径。所以 `node_modules` 依然包含所有它的子目录，而不单单只有一层。(否则全世界开发者都要爆炸)

### @babel/node 从 @babel/cli 中独立了

和 babel 6 不同，如果要使用 `@babel/node`，就必须单独安装，并添加到依赖中。

### Babel-upgrade

在提到删除 stage-x 时候提过这个 [工具](https://link.juejin.im?target=https%3A%2F%2Fgithub.com%2Fbabel%2Fbabel-upgrade)，它的目的是帮助用户自动化地从 babel 6 升级到 7。

这款升级工具的功能包括：(这里并不列出完整列表，只列出比较重要和常用的内容)

**package.json**

+ 把依赖 (和开发依赖) 中所有的 `babel-*` 替换为 `@babel/*`
+ 把这些 `@babel/*` 依赖的版本更新为最新版 (例如 `^7.0.0`)
+ 如果 `scripts` 中有使用 `babel-node`，自动添加 `@babel/node` 为开发依赖
+ 如果有 `babel` 配置项，检查其中的 `plugins` 和 `presets`，把短名 (`env`) 替换为完整的名字 (`@babel/preset-env`)

**.babelrc**

+ 检查其中的 `plugins` 和 `presets`，把短名 (`env`) 替换为完整的名字 (`@babel/preset-env`)
+ 检查是否包含 `preset-stage-x`，如有替换为对应的插件并添加到 `plugins`
+ 使用方式如下：

    ```
    不安装到本地而是直接运行命令，npm 的新功能
    
    npx babel-upgrade --write
    或者常规方式
    
    npm i babel-upgrade -g
    babel-upgrade --write
    复制代码
    ```

+ `babel-upgrade` 工具本身也还在开发中，还列出了许多 TODO 没有完成，因此之后的功能可能会更加丰富，例如上面提过的 `ignore` 的通配符转化等等。

# Babel 深入

抽象语法树

工作流程

工具集

插件实践

https://juejin.im/post/5c21b584e51d4548ac6f6c99

升级至 babel7

https://babel.docschina.org/docs/en/7.0.0/v7-migration#babel-register

配置文件加载逻辑

https://developers.weixin.qq.com/community/develop/article/doc/0008a6df750c20b698485c53e56413

# FAQ

## 其他语言有 Babel 吗？

以 java 为例，kotlin 可以编译成指定的 java 版本代码，其实就是类似的作用

但是在编译型语言里 babel like 工具不会广泛使用，因为依赖包依赖的 java 版本是固定的，而且编译之后不好再改了？所以光是自己的包可以支持编译到指定版本是不够的，还是依赖包也支持

在 js 就不会有这个问题，所以的编译都被打包到一个 js 文件中，如果依赖中有高版本的代码，也一起处理掉就可以了

## 最后打包成一个 Js 文件

因为我们相当于是手动的在按需引用

不就解决了反复实现的问题了吗？所以问题在我们 swan-component 编译完之后是怎么样的？根据描述来看肯定是打包成多个 js 了

因为 webpack 的打包规则，会把每个模块包装在一个单独的作用域，所以还是会存在重复定义

## 转换的内容分三大类

语法：es6 语法，latest 语法

全局对象：promise，set，map 等，全局对象的静态方法

实例方法：在原型上

## 为什么 Babel-runtime 没能实现实例的方法而 Polyfill 做到了呢？

明明说 polyfill 是一个简单的包，nbcs

## Babel-polyfill 是不推荐使用的

## Env 什么时候是 Require 的，什么时候是重复实现的

猜测：内置的维护列表都是 require

非内置的就重复实现？

## Runtime 是必备的

通过引用可以有效减小打包体积

## 什么时候只需要 Runtime 即可？

问题的关键是 runtime 的功能以及可配置的选项

不同版本不同的答案

对于 babel6 的版本，

## 什么时候必须引入 polyfill？

同上，两个问题互斥，不需要 polyfill 就是仅使用 runtime，因为 runtime 是可以有效减少打包提及的，所以是必备的

## 必须引入 Polyfill 的时候如何减小成本？

只能勉强减小，通过 useBuilIns，可以配合 browserlist？还需要检测

## 仅仅引入 Env 就可以使用 Async await？

是的，本质是 env 的维护列表有哪些插件，为什么有时候不行

## 默认只解析语法层面的？是指 Preset 都只解析语法层面？这里的默认指的是什么

env 默认只解析语法层面的，不会解析新的 api，async await 属于新的语法

对于 babel6 和 babel7 都是一样，开启了 useBuiltIn，才会转换 api

## Env 包含了哪些

查看 package.json

## 一直以来都适用 Preset 的，这些 Preset 都和 Env 是类似的

babel/core 又是干了些啥？

babel 本身是没有语法转换的功能的，所有功能都是靠插件实现的。而 preset 仅仅是插件的集合而已

## 更好的优化 Polyfill 的加载问题

在使用@babel/preset-env 的 `useBuiltIns:usage` 这个配置项是，还是会存在一些问题。比如当项目的文件无法进行静态分析时，需要提供一种方案来进行 polyfill 的加载。另一个问题是 `useBuiltIns:usage` 可能会在一个文件头注入数十个 core-js 的导入语句。当项目中有几百上千个文件的时候，这些注入的语法会占据数量客观的体积。我们需要一个机制来收集所有需要用到的模块，并进行去重操作，最后统一注入到项目里。

对于那些需要支持低版本浏览器的开发人员来说，为了支持 IE11 这种浏览器，polyfill 文件的大小会急剧膨胀。一种解决方案是使用 type = module / nomodules 属性，生成两个不同的包，一个用来支持现代浏览器，一个用来支持低版本的浏览器，但这并不是一个完美的解决方案；另一种解决方式是提供一个 polyfill 的服务，根据请求中的 UA 来判断浏览器的型号，返回这个浏览器需要的 polyfill 文件，[类似的服务有polyfill.io](http://xn--polyfill-8c2mk4z3r2dlbaz170b0pr.io)。但是 polyfill.io 的返回并不准确，可用性不是很高。

## Others

- 增加对 web 标准的支持，比如 fetch
- @babel/runtime 提供对目标环境的支持，类似@babel/preset-env 中 targets 字段

## Babel6 可以使用 Babel7 的库吗

不可以

# Core-js @@@

## 概述

### Core-js 是什么

它是 JavaScript 标准库的 polyfill，其他实现 polyfill 的库，比如 babel-bolyfill，babel-runtime 都是依赖 core-js 的

可以说是 core-js 的升级，造成了 babel6 和 babel7 的主要差异

它尽可能的进行模块化，让你能选择你需要的功能

它 **可以不** 污染全局空间

它和 babel 高度集成，可以对 core-js 的引入进行最大程度的优化

### Core-js 升级的动机

core-js 中的破坏性变更只能在主版本的升级中进行

core-js@2.0 的版本已经在 2018 年左右冻结了，所有的新特性只会添加到 3.0 的分支中

### core-js@3 的重要改变

对于 ECMAScript 中已经稳定的功能，core-js 已经几乎完全支持，并在 core-js@3 中引入了一些新的功能

对于一些已经加入到 ES2016-ES2019 中的提案，现在已经被标记为稳定功能

增加了 proposals 配置项，对处在提案阶段的 api 提供支持，但是因为提案阶段并不稳定，在正式加入标准之前，可能会有大的改动，需要谨慎使用；对于一些改变巨大的提案，也进行了对应的更新

增加了对一些 web 标准的支持，比如 URL 和 URLSearchParams

删除了一些过时的特性

在 cores-js@3 的版本中，所以规范中的特性都使用 `es.` 这个前缀，而提案中的特性使用 `esnext.` 这个前缀。

几乎所有的 CommonJS 的入口文件都已经发生改变。在 core-js@3 中，包含了更多的模块入口。这使得对于目标浏览器的按需支持更加的具有灵活性，同时可以带来文件大小方面的优化。

在 core-js@2 中，@babel/preset-env 在插件内部有一个 data-table，维护了不同浏览器对于特定 API 的支持，通过这个 data-table 来实现不同 targets 按需加载所需要的 core-js 模块。由于这个 compat-table 存在一些固有的问题，作者重新维护了一个包，即 core-js-compat，用来提供不用目标引擎所需要的 core-js 的模块信息。

```js
const {
  list,              // array of required modules
  targets,           // object with targets for each module
} = require('core-js-compat')({
  targets: '> 2.5%', // browserslist query
  filter: 'es.',     // optional filter - string-prefix, regexp or list of modules
});

console.log(targets);

/* =>
{
  'es.symbol.description': { ios: '12.0-12.1' },
  'es.array.reverse': { ios: '12.0-12.1' },
  'es.string.replace': { firefox: '63', ios: '12.0-12.1' },
  'es.string.trim': { ios: '12.0-12.1' },
  'es.promise': { firefox: '63' },
  'es.promise.finally': { firefox: '63' },
  'es.array-buffer.slice': { ios: '12.0-12.1' },
  'es.typed-array.int8-array': { ios: '12.0-12.1' },
  'es.typed-array.uint8-array': { ios: '12.0-12.1' },
  'es.typed-array.uint8-clamped-array': { ios: '12.0-12.1' },
  'es.typed-array.int16-array': { ios: '12.0-12.1' },
  'es.typed-array.uint16-array': { ios: '12.0-12.1' },
  'es.typed-array.int32-array': { ios: '12.0-12.1' },
  'es.typed-array.uint32-array': { ios: '12.0-12.1' },
  'es.typed-array.float32-array': { ios: '12.0-12.1' },
  'es.typed-array.float64-array': { ios: '12.0-12.1' },
  'es.typed-array.from': { ios: '12.0-12.1' },
  'es.typed-array.of': { ios: '12.0-12.1' }
}
*/
```

对于 core-js@3 新的入口文件，下面有一些简单的例子

```js
// polyfill all `core-js` features:
import "core-js";
// polyfill only stable `core-js` features - ES and web standards:
import "core-js/stable";
// polyfill only stable ES features:
import "core-js/es";

// if you want to polyfill `Set`:
// all `Set`-related features, with ES proposals:
import "core-js/features/set";
// stable required for `Set` ES features and features from web standards
// (DOM collections iterator in this case):
import "core-js/stable/set";
// only stable ES features required for `Set`:
import "core-js/es/set";
// the same without global namespace pollution:
import Set from "core-js-pure/features/set";
import Set from "core-js-pure/stable/set";
import Set from "core-js-pure/es/set";

// if you want to polyfill just required methods:
import "core-js/features/set/intersection";
import "core-js/stable/queue-microtask";
import "core-js/es/array/from";

// polyfill reflect metadata proposal:
import "core-js/proposals/reflect-metadata";
// polyfill all stage 2+ proposals:
import "core-js/stage/2";
```

### Monorepos 包的拆分

core-js@2 一个最常见的问题就是包的体积太大 (~2M)，并且有很多重复的文件被引用。基于这个原因，core-js@3 对包进行拆分，三个核心的包分别是

- [core-js](https://www.npmjs.com/package/core-js)：定义全局的 polyfill（~500k, 40k minified and gzipped）
- [core-js-pure](https://www.npmjs.com/package/core-js-pure)：提供不污染全局环境的 polyfill，等价于 core-js@2/library（~440k）
- [core-js-compat](https://github.com/zloirock/core-js/tree/master/packages/core-js-compat)：包含了 core-js 模块和 API 必要的数据，通过 browserslist 来生成所需要的 core-js 模块的列表

在以前的版本中，已进入 ECMAScript 标准的特性用 `es6.` 的前缀来表示，提案阶段的特性用 `es7.` 的前缀来表示，选择这个前缀的原因是在 2014 年的时候 ES6 以后的所有特性都考虑使用 ES7 来进行命名。

## core-js@3 与 @babel/

corejs:3 支持实例方法

如上面提到的，core-js 与 babel 是高度集成的，babel 的集成给 core-js 的按需加载提供了可能。在 babel7.4.0 的版本中已经支持 core-js@3 的版本。

### @babel/prest-env

在升级到 7.4.0 以上的版本以后，既支持 core-js@2，也支持 core-js@3。所以增加了 `corejs` 的配置，来控制所需的版本，默认是 core-js@2 并且会有文字输出提示升级到 3 的版本。

@babel/prest-env 可以通过配置 useBuiltIns 来根据 targets 加载@babel/polyfill。

### @babel/polyfill 的改动

@babel/polyfill 是一个简单的包，包含 core-js 和 regenerator-runtime 这两个包。当 core-js 升级到 3.0 的版本后，将放弃使用@babel/polyfill，因为它只包含 core-js 2.0 的版本。

所以在@babel/prest-env 升级到 7.4.0 并且使用 core-js@3，需要做如下的替换工作

```js
// 安装core-js@3.0 和 regenerator-runtime
yarn add core-js@3
yarn add regenerator-runtime


// babel.config.js
presets: [
  ["@babel/preset-env", {
    useBuiltIns: "entry", // or "usage"
    corejs: 3,
  }]
]


// 入口文件index.js
// before
import "@babel/polyfill";

// after
import "core-js/stable";
import "regenerator-runtime/runtime";

```

### @babel/runtime

当使用 core-js@3 的时候，@babel/transform-runtime 会从 core-js-pure 这个包里去加载对应的 polyfill 代码，core-js-pure 里面的代码不会污染全局变量，适合第三方库的开发。

在@babel/transform-runtime 的最新版本中，已经支持 core-js@3，需作如下操作。

```js
yarn remove @babel/runtime-corejs2
yarn add @babel/runtime-corejs3

//babel.config.js
plugins: [
  ["@babel/transform-runtime", {
    corejs: 3,
  }]
]
```

#### 改变一

+ 在之前的版本中，@babel/runtime 最大的问题就是无法模拟实例上的方法，比如数组的 includes 方法就无法被 polyfill。
+ 但是在 core-js@3 的版本中，所有的实例方法都可以被 polyfill 了。

    ```js
    array.includes(something)
    
    ↓ ↓ ↓ ↓ ↓ ↓
    
    import _includesInstanceProperty from "@babel/runtime-corejs3/core-js-stable/instance/includes";
    _includesInstanceProperty(array).call(array, something);
    ```

#### 改变二

+ core-js@3 支持对 ECMAScript 提案的 API 进行模拟。
+ @babel/plugin-transform-runtime 的默认配置中，是不会注入对提案的 polyfill 代码。如果想要支持提案中的 API，只需要增加和@babel/preset-env 类似的配置项。

    ```js
    plugins: [
      ["@babel/transform-runtime", {
        corejs: { version: 3, proposals: true },
      }]
    ]
    ```

    ```js
    new Set([1, 2, 3, 2, 1]);
    string.matchAll(/something/g);
    
    ↓ ↓ ↓ ↓ ↓ ↓
    
    // without proposals flag
    import _Set from "@babel/runtime-corejs3/core-js-stable/set";
    new _Set([1, 2, 3, 2, 1]);
    string.matchAll(/something/g);
    
    
    // with proposals: true
    import _Set from "@babel/runtime-corejs3/core-js/set";
    import _matchAllInstanceProperty from "@babel/runtime-corejs3/core-js/instance/match-all";
    new _Set([1, 2, 3, 2, 1]);
    _matchAllInstanceProperty(string).call(string, /something/g);
    ```

# @babel/preset-env

参考：https://juejin.im/post/5d74d4376fb9a06ad16f9644#heading-0

## 概述

### 作用

babel/preset-env 主要的作用是用来转换那些已经被正式纳入 TC39 中的语法。所以它无法对那些还在提案中的语法进行处理，对于处在 stage 中的语法，需要安装对应的 plugin 进行处理。

> 比如 async await，扩展运算符，箭头函数等

新加入标准库的，可能是一些语法特性，比如箭头函数等，还有可能是一些新的 API，比如 promise、set、inclues 等

> 比如 Iterator、Generator、Set、Maps、Proxy、Reflect、Symbol、Promise 等全局对象
>
> 以及一些定义在全局对象上的方法 (比如 `Object.assign`) 都不会转码。

env 默认只会转换语法特性，而不会转换新的 API，即使是最新版本也是一样的。只有配置了 usages 字段才会帮我们转换新的 api

> 方式是 require 到全局，适合业务项目

对于语法，babel 可以通过生成静态语法树，去做一些转换，生成对应的 ES5 的代码。

但是对于新的 API，需要浏览器去原生支持，或者使用大量的代码去进行 API 的模拟，通过引入这些垫片，使得低版本的浏览器能模拟实现那些新的 API。

## Targets

### 浏览器列表集合

对基于浏览器或者 `Electron` 的项目, 我们推荐使用一个 `.browserslistrc` 文件指定编译目标.如果你已经有了这个配置文件, 它将被很多前段工程化生态的工具利用到, 比如 `autoprefixer`, `stylelint`, `eslint-plugin-compat`...

如果没有配置 `targets` 或者 `ignoreBrowserslistConfig`, `@babel/preset-env` 将使用默认的 `Browserslist` 配置

如果你想支持市场份额大于 0.25% 而且忽略安全更新的浏览器如 `IE 10` 和 `BlackBerry` 的语法转换和语法实现, 你可以采用如下的配置

```js
{
  "presets": [
    [
      "@babel/preset-env",
      {
        "useBuiltIns": "entry"
      }
    ]
  ]
}
```

.browserlistrc

```
> 0.25%
not dead
```

或者你可以在 `package.json` 文件里配置

```
"browserslist": "> 0.25%, not dead"
```

### Targets

https://juejin.im/post/5d74d4376fb9a06ad16f9644#heading-0

### env.targets

```json
"targets": {
    "browsers": ["last 2 versions", "safari >= 7"]
}
```

如上配置将考虑所有浏览器的最新 2 个版本 (safari 大于等于 7.0 的版本) 的特性，将必要的代码进行转换。而这些版本已有的功能就不进行转化了。这里的语法可以参考 [browserslist](https://link.juejin.im?target=https%3A%2F%2Fgithub.com%2Fbrowserslist%2Fbrowserslist)

```json
"targets": {
    "node": "6.10"
}
```

如上配置将目标设置为 nodejs，并且支持 6.10 及以上的版本。也可以使用 `node: 'current'` 来支持最新稳定版本。例如箭头函数在 nodejs 6 及以上将不被转化，但如果是 nodejs 0.12 就会被转化了。

配置

```js
{
  "presets": [
    ["env", {
      "targets": {
        "browsers": ["last 2 versions", "safari >= 7"],
          "node": "6.10",
          "node": "current"
      }
    }]
  ]
}
```

使用 [browserslist](https://github.com/ai/browserslist) 通过像 `> 1%, last 2 versions` 查询语句来声明支持的环境。

对浏览器版本范围的配置，babel-preset-env 借助了 [browserslist](https://github.com/browserslist/browserslist) 这个库，还有更多的配置方式，可以自行探究。

```js
{
  "presets": [
    ["env", {
      "targets": {
        "chrome": 52
      }
    }]
  ]
}
{
  "presets": [
    ["env", {
      "targets": {
        "chrome": 52,
        "browsers": ["last 2 versions", "safari 7"]
      }
    }]
  ]
}
{
  "presets": [
    ["env", {
      "targets": {
        "browsers": "ie 11"
      }      
    }]
  ]
}
{
        "browsers": "edge 16"
}      
"browsers": [ "ie >= 8", "chrome >= 62" ]
```

实现原理很简单。官方文档写的挺简洁的，挑重点大致翻译下。

1、首先，检测浏览器对 JS 特性的支持程度，比如通过通过 [compat-table](https://github.com/kangax/compat-table) 这样的外部数据。

将 JS 特性 跟 特定的 babel 插件 建立映射，映射关系可以参考 [这里](https://github.com/babel/babel-preset-env/blob/master/data/plugin-features.js)。

3、stage-x 的插件不包括在内。

4、根据开发者的配置项，确定至少需要包含哪些插件。比如声明了需要支持 IE8+、chrome62+，那么，所有 IE8+ 需要的插件都会被包含进去。

## Env Option

https://babeljs.io/docs/en/6.26.3/babelrc#env-option

可以实现根据不同的环境，指定不同的插件

## useBuiltIns

### 概述

可选值： `false | "usage" | "entry" `

默认值：`false`

这个选项配置了 `@babel/preset-env` 如何引用 polyfill

因为 `@babel/polyfill` 在 `Babel 7.4.0` 已被废弃, 我们推荐直接添加 `core-js` 和通过 [`corejs`](https://babeljs.io/docs/en/babel-preset-env#corejs) 选项设置版本

```
npm install core-js@3 --save

# or

npm install core-js@2 --save
```

当 `usage` 或者 `entry` 选项被使用, `@babel/preset-env` 将直接引用 `cores-js` 模块相当于暴露 `imports` 或者 `requries`, 这一位置 `core-js` 将被直接解析到文件本身而且需要可访问

> 在 babel-preset-env 的配置项中是一个 boolean 值，在@babel/preset-env 的时候则扩展了几个选项 "useage" 和 "entry"。

### False

默认值

不在每一个文件自动添加语法填充, 不转换 `import "core-js"` 和 `import "@babel/polyfill"` 为单独的语法填充

表示不会自动引入 polyfills，并且不会处理 `import “@babel/polyfill” ` 和 `import “core-js”`

> 表现为不会拆分 core-js
>
> 此时如果在业务入口 `import '@babel/polyfill'`, 会无视 `.browserslist` 将所有的 polyfill 加载进来

### Entry

关键词是：**拆分、browserslist`**

需要手动在入口文件添加：

- `import '@babel/polyfill'` 不推荐使用
- `import "core-js/stable"` 等各种 core-js
- `import "regenerator-runtime/runtime"`

才会生效，会被自动将以上包分割为单独的模块，根据.browserslist 的过滤结果导入。

> 不一定是用得上的，只是根据 browserslist 过滤一部分，到底有没有过滤呢？
>
> 类似 [polyfill.io](http://polyfill.io) 方案

```js
require("core-js/modules/es.symbol");

require("core-js/modules/es.symbol.description");

require("core-js/modules/es.symbol.async-iterator");

require("core-js/modules/es.symbol.has-instance");

...
...
...

require("core-js/modules/web.url-search-params");

require("regenerator-runtime/runtime");
```

**注意：**

+ 你的整个 app 里只能使用一次 `import "core-js` 和 `import "regenerator-runtime/runtime"`
+ 多次引入这些包将导致全局的冲突和其它难易追踪的问题
+ 如果你正在使用 `@babel/polyfill`, 其已包含了 `core-js` 和 `regenerator-runtime`，请注意
+ 我们推荐创建一个只包含 `import` 声明的单入口文件

**示例**

In

```js
import "core-js";
```

Out(基于不同的环境)

```js
import "core-js/modules/es.string.pad-start";
import "core-js/modules/es.string.pad-end";
```

引入 `"core-js"` 加载了对于每一个可能的 `ECMAScript` 特性的语法填充，如果你知道你只需要他们其中的某一部分, 当使用 `core-js@3` 和 `@babel/preset-env`, 能够对每一个 `core-js` 入口和其依赖的优化. 比如, 你看值需要填充数组方法和新的 `Math` 提案:

In

```js
import "core-js/es/array";
import "core-js/proposals/math-extensions";
```

Out(基于不同的环境)

```js
import "core-js/modules/es.array.unscopables.flat";
import "core-js/modules/es.array.unscopables.flat-map";
import "core-js/modules/esnext.math.clamp";
import "core-js/modules/esnext.math.deg-per-rad";
import "core-js/modules/esnext.math.degrees";
import "core-js/modules/esnext.math.fscale";
import "core-js/modules/esnext.math.rad-per-deg";
import "core-js/modules/esnext.math.radians";
import "core-js/modules/esnext.math.scale";
```

你可以阅读 [`core-js`](https://github.com/zloirock/core-js) 的文档获取关于不同入口的信息

### Usage

**按需引入。同样会造成全局污染**。这个选项只是 entry 的一种增强：

- 不需要在入口手动引入一次
- 根据 `.browserslist` + 业务代码使用到的新 API 按需进行 polyfill

> 不需要手动 `import '@babel/polyfill'` (加上也无妨，编译时会自动去掉)
>
> 这种方式打包体积不大，但是如果我们排除 node_modules/目录，遇上没有经过转译的第三方包，就检测不到第三方包内部的 ‘hello‘.includes(‘h‘) 这种句法，这时候我们就会遇到 bug

当文件里被使用时, 添加特定的引入来语法填充, 我们利用它, 一个打包的文件只会加载一次相同的语法填充

> 部分是如此

**In**

a.js

```
var a = new Promise();
```

b.js

```
var b = new Map();
```

**Out(如果环境需要语法填充)**

```js
import "core-js/modules/es.promise";
var a = new Promise();
import "core-js/modules/es.map";
var b = new Map();
```

**Out(如果环境支持该语法)**

```js
var a = new Promise();
var b = new Map();
```

## Corejs

可选值： `2, 3` 或者 `{ version: 2 | 3, proposals: boolean }`

默认值： `2`

这个选项只会在与 `useBuiltIns: usage` 或者 `useBuiltIns: entry` 一起使用时才会生效, 确保 `@babel/preset-env` 为你的 `core-js` 版本注入了正确的引入

默认只有稳定的 `ECMAScript` 特性的语法填充才被注入, 你有三种方式去做语法填充:

- 当使用 `useBuiltIns: "entry"`, 你可直接引入一个 [`proposal polyfill`](https://github.com/zloirock/core-js/tree/master/packages/core-js/proposals): `import "core-js/proposals/string-replace-all"`

+ 当时用 `useBuiltIns: usage` 的时候又有两个可选：
    + 将 `shippedProposals`（`@babel/preset-env` 的另一个配置项）选项设置为 true。这将启用已在浏览器中提供一段时间的提议的 polyfill 和转换。
    + 使用 `corejs: {version：3，proposal：true}` 这样可以实现对 `core-js` 支持的每个提案的填充。

## 不足

会存在几个反复定义的，观测到的包括：

+ 即是无法简单通过语法树解析转换的

- async await
- 扩展运算符

上面提到的 `useBuiltIns：'usage'` 似乎已经很完美解决我们的需要了，但是我们构建的时候发现：

```js
// es6+ 源码：
const asyncFun = async ()=>{
  await new Promise(setTimeout, 2000)
  
  return '2s 延时后返回字符串'
}
export default asyncFun
```

根据上述的 `useBuiltIns：'usage'` 配置编译后：

```js
import "core-js/modules/es6.promise";
import "regenerator-runtime/runtime";

function asyncGeneratorStep(gen, resolve, reject, _next, _throw, key, arg) { try { var info = gen[key](arg); var value = info.value; } catch (error) { reject(error); return; } if (info.done) { resolve(value); } else { Promise.resolve(value).then(_next, _throw); } }

function _asyncToGenerator(fn) { return function () { var self = this, args = arguments; return new Promise(function (resolve, reject) { var gen = fn.apply(self, args); function _next(value) { asyncGeneratorStep(gen, resolve, reject, _next, _throw, "next", value); } function _throw(err) { asyncGeneratorStep(gen, resolve, reject, _next, _throw, "throw", err); } _next(undefined); }); }; }

var asyncFun =
/*#__PURE__*/
function () {
  var _ref = _asyncToGenerator(
  /*#__PURE__*/
  regeneratorRuntime.mark(function _callee() {
    return regeneratorRuntime.wrap(function _callee$(_context) {
      while (1) {
        switch (_context.prev = _context.next) {
          case 0:
            _context.next = 2;
            return new Promise(setTimeout, 2000);

          case 2:
            return _context.abrupt("return", '2s 延时后返回字符串');

          case 3:
          case "end":
            return _context.stop();
        }
      }
    }, _callee, this);
  }));

  return function asyncFun() {
    return _ref.apply(this, arguments);
  };
}();

export default asyncFun;
```

上述代码中，我们看到，`asyncGeneratorStep`, `_asyncToGenerator` 这两个函数是被内联进来，而不是 import 进来的。

也就是说，**如果你有多个文件都用到了 async，那么每个文件都会内联一遍 `asyncGeneratorStep`, `_asyncToGenerator` 函数**。

这代码明显是重复了，那么有什么方法可以进行优化呢？ 答案是 `@babel/plugin-transform-runtime`

# @babel/plugin-transform-runtime

> 也就是说 @babel/runtime 是一个核心， 一种实现方式， 而 @babel/plugin-transform-runtime 就是一个管家， 负责更好的重复使用 @babel/runtime
>
> 版本 2 不支持内置对象 ， 但自从 Babel 7.4.0 之后，拥有了 @babel/runtime-corejs3 ， 我们可以放心使用 corejs: 3 对实例方法做支持

移除了 polyfill 的配置项添，加了 corejs 配置项。

@babel/plugin-transform-runtime 的默认配置如下：

```js
{
  plugins: [
    ["@babel/plugin-transform-runtime", {
      "absoluteRuntime": false, // 不是很清除干啥的
      "corejs": false, // 下面详解
      "helpers": true, // 助手函数是否提取，同babel-plugin-transform
      "regenerator": true, // 同babel-plugin-transform
      "useESModules": false
    }]
  ]
}
```

## Corejs

对于配置项 `corejs: false | 2 | 3`

`false` ：依赖 @babel/runtime，是默认选项，认为需要填充的 API 以被填充，所以不对 API 作 polyfill

> 对于@babel/runtime 的依赖是提取的 helper 的依赖）。

`2` ：依赖@babel/runtime-corejs2，只转换 **全局变量** (比如：`Object`) 和 **静态方法**(比如：`Object.assign`)

`3` ：依赖@babel/runtime-corejs3，会转换原型上的方法

## 特性

依赖于 `@babel/runtime`，`@babel/runtime` 这个插件添加的 polyfill 都是私有的，不会影响到全局环境

`@babel/runtime` 内部集成了：

1. `core-js@2/@3`: 转换一些内置类 (`Promise`, `Symbols` 等等) 和静态方法 (`Array.from` 等)，@3 可以转换实例方法
2. `regenerator`: 作为 `core-js` 的拾遗补漏，主要是 `generator/yield` 和 `async/await` 两组的支持。当代码中有使用 `generators/async` 时自动引入。
3. `helpers`, 如上面的 `asyncToGenerator` 就是其中之一，其他还有如 `jsx`, `classCallCheck` 等等，可以查看 https://babeljs.io/docs/en/babel-helpers。

`@babel/plugin-transform-runtime` 在其基础上实现了按需引入，当代码中有内置的 `helpers` 使用时，会移除定义，并插入引用。

## 作用

1. 当使用 generators/async 的时候自动引入 @babel/runtime/regenerator

    > 因为默认值是 true？

2. 为使用到的新特性的 API 添加实现
3. 提取每个模块内联的 helper 转换为引用

babel 在每个需要的文件的顶部都会插入一些 poylfill 代码，这可能会导致多个文件都会有重复的 poylfill 代码。 `@babel/plugin-transform-runtime` 的 helpers 选项就可以把这些模块抽离出来

```js
// .babelrc.js
module.exports = {
    "plugins": [
        [
            "@babel/plugin-transform-runtime",
            {
                "corejs": false, // 默认值，可以不写
                "helpers": true, // 默认，可以不写
                "regenerator": false, // 通过 preset-env 已经使用了全局的 regeneratorRuntime, 不再需要 transform-runtime 提供的 不污染全局的 regeneratorRuntime
                "useESModules": true, // 使用 es modules helpers, 减少 commonJS 语法代码
            }
        ]
    ],
    presets: [
        [
            "@babel/preset-env",
            
            {
                "modules": false, // 模块使用 es modules ，不使用 commonJS 规范 
                "useBuiltIns": 'usage', // 默认 false, 可选 entry , usage
            }
        ]
    ]
}
```

```js
// 添加新配置后编译出来的代码
import "core-js/modules/es6.promise";
import "regenerator-runtime/runtime";
import _asyncToGenerator from "@babel/runtime/helpers/esm/asyncToGenerator";

var asyncFun =
/*#__PURE__*/
function () {
  var _ref = _asyncToGenerator(
  /*#__PURE__*/
  regeneratorRuntime.mark(function _callee() {
    return regeneratorRuntime.wrap(function _callee$(_context) {
      while (1) {
        switch (_context.prev = _context.next) {
          case 0:
            _context.next = 2;
            return new Promise(setTimeout, 2000);

          case 2:
            return _context.abrupt("return", '2s 延时后返回字符串');

          case 3:
          case "end":
            return _context.stop();
        }
      }
    }, _callee, this);
  }));

  return function asyncFun() {
    return _ref.apply(this, arguments);
  };
}();

export default asyncFun;
```

可以看到，已经没有了内联的 helpers 代码，大功告成。

# Babel7 最佳实践

## 同时引用且存在冲突时的状况

[https://blog.nixiaolei.com/2019/12/03/%E3%80%90%E5%B7%A5%E7%A8%8B%E5%8C%96%E3%80%91-babel-env-%E4%B8%8E-babel-plugin-transform-runtime-%E4%BD%BF%E7%94%A8%E5%8F%8A%E5%9C%BA%E6%99%AF%E5%8C%BA%E5%88%AB/](https://blog.nixiaolei.com/2019/12/03/[工程化]-babel-env-与-babel-plugin-transform-runtime-使用及场景区别/)

最后的部分有提及

runtime 的 corejs 会覆盖 env 的 corejs 做到引用

runtime = corejs-pure

env = corejs-compat

区别有 全局和局部，都是引用的，特例是 async await 扩展运算符

下面两种都是 ok 的，一劳永逸

usage3 & runtime 就够用了，适用于项目

usage & runtime3 是第三方库使用的配置

## 使用 Async 语法

env 即可，回家看看为什么不行 oricanon 的项目

## 代码

如果没有什么特殊的需求，使用 babel 7 的最佳配置是:

1. 首先安装依赖包：
2. `npm i -S core-js@3 @babel/runtime `

    > 因为 babel 编译之后的代码直接依赖

3. `npm i -D @babel/preset-env @babel/plugin-transform-runtime`
4. 配置 `.babelrc.js`

```js
// .babelrc.js
module.exports = {
    "plugins": [
        [
            "@babel/plugin-transform-runtime",
            {
                "corejs": false, // 选择false或者3，取决于第三方库or业务
                "helpers": true, // 默认值
                "regenerator": false, // 取决于第三方库or业务
                "useESModules": true, // 使用 es modules helpers, 减少 commonJS 语法代码
            }
        ]
    ],
    presets: [
        [
            "@babel/preset-env",
            {
                "modules": false, // 模块使用 es modules ，不使用 commonJS 规范 
                "useBuiltIns": 'usage', // 默认 false, 可选 entry , usage
            }
        ]
    ]
}
```

# Babel6

四种方法引入 polyfill

## Babel-polyfill

babel-polyfill 的作用是兼容新的 API。

babel 默认只转换 js 语法，而不转换新的 API

举例来说，es2015 在 Array 对象上新增了 `Array.from` 方法。babel 就不会转码这个方法。如果想让这个方法运行，必须使用 `babel-polyfill`。(内部集成了 `core-js` 和 `regenerator`)

### 使用

使用时，在入口文件运行之前增加 `require('babel-polyfill')`

> 每个入口都需要添加

或者更常规的操作是在 `webpack.config.js` 中将 `babel-polyfill` 作为第一个 entry。因此必须把 `babel-polyfill` 作为 `dependencies` 而不是 `devDependencies`

### 优点

一次引入，全局使用。

会转换实例方法和静态方法，比较全面。

#### 缺点

`babel-polyfill` 主要有两个缺点：

1. 使用 `babel-polyfill` 会导致打出来的包非常大，因为 `babel-polyfill` 是一个整体，把所有方法都加到原型链上。

    > 比如我们只使用了 `Array.from`，但它把 `Object.defineProperty` 也给加上了，这就是一种浪费了。这个问题可以通过单独使用 `core-js` 的某个类库来解决，`core-js` 都是分开的。

2. `babel-polyfill` 会污染全局变量，给很多类的原型链上都作了修改，如果我们开发的也是一个类库供其他开发者使用，这种情况就会变得非常不可控。

因此在实际使用中，如果我们无法忍受这两个缺点 (尤其是第二个)，通常我们会倾向于使用 `babel-plugin-transform-runtime`。

> 反复实现其实不是一个缺点

### 使用场景

开发业务项目，比较全面，不会漏了从而出问题，比如 Object.assign 这样的方法在 ios8 上面还是需要 polyfill 的。

babel-ployfill 直接无视，不推荐使用，有更好的替代方案

## Babel-runtime

和直接在入口引入 polyfill 不同，该插件引入的 polyfill 是模块私有的。

对于需要的 polyfill 需要 **手动引入**：`import Promise from 'babel-runtime/core-js/promise'`

它内部集成了

1. `core-js`: 转换一些内置类 (`Promise`, `Symbols` 等等) 和静态方法 (`Array.from` 等)。绝大部分转换是这里做的。自动引入。
2. `regenerator`: 作为 `core-js` 的拾遗补漏，主要是 `generator/yield` 和 `async/await` 两组的支持。当代码中有使用 `generators/async` 时自动引入。
3. `helpers`, 如上面的 `asyncToGenerator` 就是其中之一，其他还有如 `jsx`, `classCallCheck` 等等，可以查看 [babel-helpers](https://link.juejin.im?target=https%3A%2F%2Fgithub.com%2Fbabel%2Fbabel%2Fblob%2F6.x%2Fpackages%2Fbabel-helpers%2Fsrc%2Fhelpers.js)。当代码中有内置的 `helpers` 使用时 (如上面的第一段代码)，会移除定义，并插入引用 (于是就变成了第二段代码)。

> 补充，把 helpers 抽离并统一起来，避免重复代码的工作还有一个 plugin 也能做，叫做 `babel-plugin-external-helpers`。但因为我们使用的 `transform-runtime` 已经包含了这个功能，因此不必重复使用。而且 babel 的作者们也已经开始讨论这两个插件过于类似，正在讨论在 babel 7 中把 `external-helpers` 删除，讨论在 [issue#5699](https://link.juejin.im?target=https%3A%2F%2Fgithub.com%2Fbabel%2Fbabel%2Fissues%2F5699) 中。

### 优点

该模块私有，不会影响到全局作用域。

打出来的包因为按需引入包不会很大。

### 缺点

手动引入所需，搞不好会漏掉。

### 使用场景

开发库，框架之类可以使用，因为别人用你的东西然后不知情的情况下你改了别人的全局环境，然后出错了就尴尬了。

## Babel-plugin-transform-runtime

### 特性

同 7

该插件依赖 babel-runtime。

插件 babel-runtime 一样引入的 polyfill 是私有的，不会影响全局作用域。

并且是自动按需引入需要的 polyfill，不需要想 babel-runtime 一样一个一个手动引入。

可以提取语法转换时候每个模块都生成的各种 helper，成一个引用。

自动转换 generators/async。

### 优点

该模块私有，不会影响到全局作用域。

打出来的包因为按需引入包不会很大。

自动按需引入，不需要手动，防止遗漏。

取 helper，大大减少冗余代码。

### 缺点

不会转实例和静态方法这样的 API。

### 配置

```js
["transform-runtime",{
  "helpers": true, // 默认值
  "polyfill": true, // 默认值
  "regenerator": true, // env自带全局的regenerator，该配置提供的 不污染全局的 regeneratorRuntime
  "moduleName": "babel-runtime" // defaults to "babel-runtime"
}]
```

**示例**

in

```js
var promise = new Promise;
```

out

```js
var _promise = require("babel-runtime/core-js/promise");  // 注意这里，根本是从core-js里面引入的
 
var _promise2 = _interopRequireDefault(_promise);

function _interopRequireDefault(obj) { return obj && obj.__esModule ? obj : { default: obj }; }

var promise = new _promise2.default();
```

helper 示例

```js
class Person {}
```

```js
"use strict";
 // 这就是helper函数，每个模块都会被实现一遍，十分浪费，冗余。
function _classCallCheck(instance, Constructor) { if (!(instance instanceof Constructor)) { throw new TypeError("Cannot call a class as a function"); } }
 
var Person = function Person() {
  _classCallCheck(this, Person);
};
```

通过 runtime 转一下：

```js
"use strict";
 
var _classCallCheck2 = require("babel-runtime/helpers/classCallCheck"); // 从runtime中引入，没有再实现一遍
 
var _classCallCheck3 = _interopRequireDefault(_classCallCheck2);
 
function _interopRequireDefault(obj) { return obj && obj.__esModule ? obj : { default: obj }; }
 
var Person = function Person() {
  (0, _classCallCheck3.default)(this, Person);
};
```

generator 同样是 `require("babel-runtime/regenerator");` 引入的。

#### Ployfill 和 Regenerator

是分开的，regenerator 用的太多了，就算 polyfill 是 false，也不影响对 regenerator 的转换

#### Helper True Ployfill False

覆盖了 env 了 regenerator，很特殊，不是自己引入的，也动了

但是略有不同，re：true，引入了 4 个，re：false，只有两个

因为 env 引入的是全局的 regeneratorRuntime，而 runtime 自己的是私有的，也是业务和第三方库的区别

#### Helper True Ployfill True

相当于覆盖了 env，自己去根据代码按需引入，不是自己引入的不会动

7 就不一样了，不是自己引入的，也全部动了，从全局改成了私有的

## Babel-preset-env

无法识别扩展运算符的语法，直接报错了

> 怪不得引入了 `transform-object-rest-spread`
>
> 但是 class 应该是不用的才对，引入的是 `transform-class-properties`
>
> 因为 env 的依赖里没有这两个，大部分都是 2015 的内容，但是却有 aysnc，真的神奇，对象的 spread 是 es7 的内容

新的 api 都没有转换，但是转换了 async await

### useBuiltIns

boolean

#### False

不会转换入口的 corejs

#### True

这个选项可以启用一个新的插件来替换语句 `import "babel-polyfill"` 或者 `require("babel-polyfill")` 以及基于浏览器环境的 `babel-polyfill` 个性化需求。

> 如果入口没有引入，则没有效果

该方式同第一中引入 polyfill 的方式，但是会按照配置的环境去按需引入，稍微好点。

> 这里的 useBuiltIns:true，对应的是 babel7 的 entry

注意: 在你的整个应用里只使用一次 `require("babel-polyfill");`。 多次 imports 或 requires `babel-polyfill` 会引起报错，因为它可能导致全局冲突和其他难以追踪的问题。 我们建议创建一个只包含 `require` 语句的单个入口文件。

**示例**

In

```js
import "babel-polyfill";
```

Out (基于环境的不同)

```js
import "core-js/modules/es7.string.pad-start";
import "core-js/modules/es7.string.pad-end";
import "core-js/modules/web.timers";
import "core-js/modules/web.immediate";
import "core-js/modules/web.dom.iterable";
```

这也将直接引用 `core-js` (`import "core-js";`)

因此

```
npm install core-js --save
```

# 总结

文档翻译：https://juejin.im/post/5d74d4376fb9a06ad16f9644#heading-5

总结的比较好的：

https://blog.csdn.net/letterTiger/article/details/100717666

新的文章，扩展阅读

动态识别，浏览器，而不是根据 babel 配置 https://juejin.im/post/5c03a4d0f265da615e053612#heading-0 最后的部分

https://github.com/SunshowerC/blog/issues/4

## Polyfill Vs Runtime

内部都集成了 `core-js` 和 `regenerator`

**区别就在于**

helpers，是否是全局的

其实是这样的，core-js 是全局引入的，runtime 再做出了一层包装，transform-runtime 对 runtime 实现了 helpers

## 必须使用 Polyfill

corejs-2 的，corejs-3 只有 babel7 能用

但如果代码中包含高版本 js 中类型的实例方法 (例如 `[1,2,3].includes(1)`)，这还是要使用 polyfill。

`babel-plugin-transform-runtime` **不支持** 实例方法 (例如 `[1,2,3].includes(1)`)

## env、runtime 的升级本质是 Corejs 的升级，两者的特性还是一致的

参考：https://juejin.im/post/5ce693b45188252db303ff23#heading-0

babel 想要实现新的 API 的，核心依赖就是 core-js，现在已经发布了 3.0 的版本，而且@babel/preset-env 在 7.4.0 的版本已经支持这个最新的版本。@babel/runtime 的新版也已经支持这个 API

大版本的升级，会带来一些破坏性，但是相应的也会带来很多优势。

## Babel6

仅用 runtime，不用 env 貌似不稳定，仅转换了 promise，没有转换 async，即便是设置了 regenerator:true

> env 难道不是应该转换到最新的语法吗？怎么 es7 的都不支持了，比如扩展运算符
>
> 这个也是分版本的，以上说法只适用于 babel7，通过 package.json 查看 env 维护的插件列表

| 配置                    | es2015 语法 | async/await    | latest 全局 API | latest 实例方法 | 是否拆分 core-js |
| ----------------------- | ----------- | -------------- | -------------- | -------------- | --------------- |
| env                     | ⭕️           | ⭕️ 反复实现     | ❌              | ❌              | ❌ 无视          |
| env:useBuiltIns         | ⭕️           | ⭕️ 反复实现     | ⭕️ 全局引用     | ⭕️ 全局引用     | ⭕️               |
| env:useBuiltIns&runtime | ⭕️           | ⭕️ 按需引用实现 | ⭕️ 按需引用实现 | ⭕️ 全局引用     | ⭕️               |

> 因为，babel6 的 runtime 没有实现 latest 实例方法对应的 helpers
>
> 也不知道为什么，明明 core-js@2 也是有的，nbcs
>
> Browser list

## Babel7

| 配置                    | latest 语法    | async/await    | latest 全局 API | latest 实例方法 | 是否拆分 core-js |
| ----------------------- | -------------- | -------------- | -------------- | -------------- | --------------- |
| env                     | ⭕️ 反复实现     | ⭕️ 反复实现     | ❌              | ❌              | ❌ 无视          |
| env:useBuiltIns:entry   | ⭕️ 反复实现     | ⭕️ 反复实现     | ⭕️ 全局引用     | ⭕️ 全局引用     | ⭕️               |
| env:useBuiltIns:usage   | ⭕️ 反复实现     | ⭕️ 反复实现     | ⭕️ 按需全局引用 | ⭕️ 按需全局引用 | ❌ 消除          |
| env:useBuiltIns&runtime | ⭕️ 按需引用实现 | ⭕️ 按需引用实现 | ⭕️ 按需引用实现 | ⭕️ 按需引用实现 | 取决于是否 entry |

```
npm i --save core-js regenerator-runtime
```

需要直接安装 core-js 和

# 进一步验证

## Targets 与 Browserlist 实验

entry 的时候真的能有效减少吗？

usage 取决于谁？，用了哪些？还是 browserlist 指定了哪些？如果是后者那不就是和 entry 一样了吗

usage 的时候和 runtime 又很重合，重合就对了，全部改成按需引用的

## Core-js-pure 等子集的拆分实验

使用 core-js-pure 作为入口，是不是就可以做到引用实现呢？

## Runtime Regenerator 默认值是 true？

# 对比

npm i -S babel-runtime

npm i -D babel-plugin-transform-runtime

["transform-runtime",{

  "helpers": true, // 默认值

  "polyfill": false, // 默认值

  "regenerator": false, // env 自带全局的 regenerator，该配置提供的 不污染全局的 regeneratorRuntime

  "moduleName": "babel-runtime" // defaults to "babel-runtime"

}]

## Runtime 前

![image-20200727093145687](/img/user/programming/front-end/framework/engineering/babel/image-20200727093145687.png)

![image-20200727093844524](/img/user/programming/front-end/framework/engineering/babel/image-20200727093844524.png)

_classCallCheck 反复定义了 17 次

_createClass = 定义了 7 次

反复定义了 23 次

_defineProperty3 引用了 23 次

_toConsumableArray3 引用了 4 次

_typeof3 4 次

_symbol2

_iterator2

_getIterator3

_create2

_setPrototypeOf2

引用了 100 多次，_interopRequireDefault 总共 331，每次一都会重复定义，定义完之后用于引用 babel-runtime

babel-runtime 已经是用了哪些才引入哪些了，这部分是 webpack 的工作，webpack 只引用了 runtime 用到的那些用于打包

有很多 runtime 前没有定义的，runtime 后也被转换定义了

原本需要转换的语法非常的少，很多新语法都是已经实现了，不需要转换的， 上了 runtime 之后反而做了多余的转换

而且 runtime 还有很多基础函数，貌似没办法去掉，主要是那一部分带来的体积影响超过了提取定义带来的收益

反而没必要引入了

## Runtime 后

Dev

![image-20200727093637387](/img/user/programming/front-end/framework/engineering/babel/image-20200727093637387.png)

build

![image-20200727093742972](/img/user/programming/front-end/framework/engineering/babel/image-20200727093742972.png)

Dev:polyfill:false

如果 polyfill 是 true，转换的有点彻底，原本不会转换的也会转换了，比如 promise

![image-20200727094531161](/img/user/programming/front-end/framework/engineering/babel/image-20200727094531161.png)

build:polyfil:false

![image-20200727094639576](/img/user/programming/front-end/framework/engineering/babel/image-20200727094639576.png)

# Core

## 前

![image-20200727104747777](/img/user/programming/front-end/framework/engineering/babel/image-20200727104747777.png)

## 后

![image-20200727104653242](/img/user/programming/front-end/framework/engineering/babel/image-20200727104653242.png)

# 结论

如果想优化的话，应该单个引入 babel-runtime 的具体语法插件，然后从定义形式转换成引用形式，不过收益不大，而且感觉还挺麻烦的

疑问，为什么会出现那么多多余的代码？

感觉在 babel 的时候也没有这个问题啊，不对，需要在 webpack 上查看，babel-demo 的时候 require 的引用还是 node_modules

重点在 babel 和 runtime 打包的时候 targets 不同？

babel 的 targets 版本比较高，只转换了较少的语法

runtime 的本本较低，转换了更多的语法

ts&runtime 后

![image-20200827105838620](/img/user/programming/front-end/framework/engineering/babel/image-20200827105838620.png)

![image-20200827103722094](/img/user/programming/front-end/framework/engineering/babel/image-20200827103722094.png)

![image-20200827103835054](/img/user/programming/front-end/framework/engineering/babel/image-20200827103835054.png)

relate issue:https://github.com/babel/babel/issues/10250

确实是 babel-runtime 没有遵循 browserlist

, {

​ debug: true, // this will make babel output actual transform plugins and polyfills in terminal

​ useBuiltIns: 'usage',

​ targets: {

​ ie: '9',

​ },

​ corejs: 3,

​ }
