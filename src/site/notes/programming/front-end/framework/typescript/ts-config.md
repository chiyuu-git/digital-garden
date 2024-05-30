---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2022-10-05-Wed, 8:34:29 pm","date-modified":"2023-04-29-Sat, 8:13:25 pm","permalink":"/programming/front-end/framework/typescript/ts-config/","dgPassFrontmatter":true}
---


# 环境搭建

## 基础

### 环境

TypeScript 的命令行工具安装方法如下：

```
npm install -g typescript
```

以上命令会在全局环境下安装 `tsc` 命令，安装完成之后，我们就可以在任何地方执行 `tsc` 命令了。

编译一个 TypeScript 文件很简单：

```
tsc hello.ts
```

我们约定使用 TypeScript 编写的文件以 `.ts` 为后缀，用 TypeScript 编写 React 时，以 `.tsx` 为后缀。

### 自动编译

#### 命令行

-w 参数

```js
tsc index.ts -w
```

只能单文件监视

tsc 命令不加 path，可以全部文件监视编译，但是前提是需要 tsconfig.json 文件，即使是空的也没关系

#### Vscode

## tsconfig.json

这个 json 是可以写注释的，比较特殊，这里只列出常用的，具体请参阅 [官方手册](http://www.typescriptlang.org/docs/handbook/tsconfig-json.html)（[中文版](https://zhongsp.gitbooks.io/typescript-handbook/content/doc/handbook/tsconfig.json.html)）

`"include"` 和 `"exclude"` 属性指定一个文件 glob 匹配模式列表。 支持的 glob 通配符有：

- `*` 匹配 0 或多个字符（不包括目录分隔符）
- `?` 匹配一个任意字符（不包括目录分隔符）
- `**/` 递归匹配任意子目录

优先级：files > exclude > include

### Include

定义希望被编译的文件所在的目录

默认值：["\*\*/\*"]

示例：

```json
"include": ["src/**/*", "tests/**/*"]
```

### Exclude

默认值：

```json
     "exclude": [
      "node_modules",
      "bower_components",
      "jspm_packages"
    ]
```

示例：

```json
     "exclude": [
      "node_modules",
      "dist",
      "**/*.test.ts",
      "public"
    ]
```

### Extends

定义被继承的配置文件

### Files

## compilerOptions

### Target

设置 ts 代码编译的目标版本

可选值：ES3（默认）、ES5、ES6/ES2015、ES7/ES2016、ES2017、ES2018、ES2019、ES2020、ESNext

示例：

```json
"compilerOptions": {
    "target": "ES6"
}
```

如上设置，我们所编写的 ts 代码将会被编译为 ES6 版本的 js 代码

### Module

设置编译后代码使用的模块化系统

可选值：CommonJS、UMD、AMD、System、es6、ES2020、ESNext、None

```json
"compilerOptions": {
    "module": "es6"
}
```

### Lib

指定代码运行时所包含的库（宿主环境），一般不用改

可选值：ES5、ES6/ES2015、ES7/ES2016、ES2017、ES2018、ES2019、ES2020、ESNext、DOM、WebWorker、ScriptHost ......

示例：

```json
"compilerOptions": {
    "target": "ES6",
    "lib": ["ES6", "DOM"],
}
```

node 环境可以把 dom 改一改

### outDir

编译后文件的所在目录

默认情况下，编译后的 js 文件会和 ts 文件位于相同的目录，设置 outDir 后可以改变编译后文件的位置

示例：

```
"compilerOptions": {
    "outDir": "dist"
}
```

设置后编译后的 js 文件将会生成到 dist 目录

### 语法检查配置

strict：启用所有的严格检查，默认值为 false，设置后相当于开启了所有的严格检查

+ alwaysStrict：默认值为 false，总是以严格模式对代码进行编译。模块化默认就有严格模式
+ noImplicitAny：禁止隐式的 any 类型
+ noImplicitThis：禁止类型不明确的 this
+ strictBindCallApply：严格检查 bind、call 和 apply 的参数列表
+ strictFunctionTypes：严格检查函数的类型
+ strictNullChecks：严格的空值检查
+ strictPropertyInitialization：严格检查属性是否初始化

https://juejin.cn/post/6896680181000634376

### 其他配置

#### outFile

将所有的文件编译为一个 js 文件

默认会将所有的编写在全局作用域中的代码合并为一个 js 文件，如果 module 制定了 None、System 或 AMD 则会将模块一起合并到文件之中

示例：

```
"compilerOptions": {
    "outFile": "dist/app.js"
}
```

#### allowJs

是否对 js 文件编译

当 js 和 ts 文件混用时，tsc 打包是否一起编译

#### checkJs

是否对 js 文件进行检查，是否符合语法规范（隐式类型推论）

示例：

```
"compilerOptions": {
    "allowJs": true,
    "checkJs": true
}
```

#### removeComments

是否删除注释

默认值：false

#### sourceMap

是否生成 sourceMap

默认值：false

#### noEmit

不生产编译后的文件，仅仅用来检查一下类型 & 语法

默认值：false

#### noEmitOnError

有错误的情况下不进行编译

默认值：false

## Demo 常用配置

```json
{
  "compilerOptions": {
    "target": "es5",
    "module": "commonjs",
    "outDir": "output",
    "sourceMap": true,
    "strict": true,// 启用所有严格类型检查选项
    "noImplicitAny": true,// 在表达式和声明上有隐含的 any类型时报错
    "noEmit":true, // 不生成输出文件，通过终端判断错误即可
  },
  "include": [// 需要编译的ts文件一个*表示文件匹配**表示忽略文件的深度问题
    "./src/*.ts",
    "./src/**/*.ts"
  ],
   "exclude": [
    "node_modules",
    "dist",
    "**/*.test.ts",
    "public"
  ]
}
```

新增任务 Tenimal->Run Task 选择 `tsc:build-tsconfig.json`

选择构建则生成，选择监视则自动生成

## 调试

自动编译成 js 之后再调试

使用 ts-node 在 vscode 调试

```
npm install typescript --save-dev
npm install ts-node --save-dev
```

配置 tsconfig.json 主要是将 `sourceMap` 设置为 `true`。

在 launch.json 的配置项 **新增：**

```json
{
  "name": "Current TS File",
  "type": "node",
  "request": "launch",
  "args": [
    "${relativeFile}" // 调试当前打开的文件
  ],
  "runtimeArgs": [
    "--nolazy",
    "-r",
    "ts-node/register"
  ],
  "sourceMaps": true,
  "cwd": "${workspaceRoot}",
  "protocol": "inspector",
  "console": "integratedTerminal",
  "internalConsoleOptions": "neverOpen"
}
```

测试相关：<https://segmentfault.com/a/1190000010605261>

nest 的可以参考一下：https://segmentfault.com/a/1190000019064197

## 结合 Webpack

https://github.com/JasonkayZK/typescript_learn/tree/3-webpack

只留 compileOPtion 就好了

额外的下一个 ts-loader

```js
rules: [
    {
        test: /\.ts$/,
        use: [
            {
                loader: "babel-loader",
                options: {
                    presets: [
                        [
                            "@babel/preset-env",
                            {
                                targets: { "chorme": "88"},
                                corejs: "3",
                                useBuiltIns: "usage"
                            }
                        ]
                    ]
                }
            },
            ts-loader
        ],
        exclude: /node-modules/
    }
]
```

### Better Way

[@babel/preset-typescript](https://babeljs.io/docs/en/babel-preset-typescript) 是 babel 的一个 preset，它编译 ts 的过程很粗暴，它直接去掉 ts 的类型声明，然后再用其他 babel 插件进行编译，所以它很快。

废话补多少，先来安装它：

```
npm install @babel/preset-typescript -D
```

> 注意：我们之前因为 Eslint 的配置地方需要先安装 Typescript，所以之前安装过的就不用再安装一次了。

然后修改 `.babelrc` ：

```
{
  "presets": ["@babel/preset-react", "@babel/preset-typescript"]
}
```

[presets 的执行顺序](https://babeljs.io/docs/en/presets#preset-ordering) 是从后到前的。根据以上代码的 babel 配置，会先执行 `@babel/preset-typescript` ，然后再执行 `@babel/preset-react` 。

`src/` 有以下两个 `.tsx` 文件，代码分别如下：

`index.tsx` ：

```
import React from 'react'
import ReactDOM from 'react-dom'
import App from './app'

ReactDOM.render(
  <App name='vortesnail' age={25} />,
  document.querySelector('#root')
)
```

`app.tsx` ：

```
import React from 'react'

interface IProps {
  name: string
  age: number
}

function App(props: IProps) {
  const { name, age } = props
  return (
    <div className='app'>
      <span>{`Hello! I'm ${name}, ${age} years old.`}</span>
    </div>
  )
}

export default App
```

很简单的代码，在 `<App />` 中输入属性时因为 ts 有了良好的智能提示，比如你不输入 `name` 和 `age` ，那么就会报错，因为在 `<App />` 组件中，这两个属性时必须值！

但是这个时候如果你 `npm run start` ，发现是编译有错误的，我们修改 `webpack.common.js` 文件：

```
module.exports = {
  entry: {
    app: resolve(PROJECT_PATH, './src/index.tsx'),
  },
  output: {//...},
  resolve: {
    extensions: ['.tsx', '.ts', '.js', '.json'],
  },
}
```

一来修改了 `entry` 中的入口文件后缀，变为 `.tsx` 。

二来新增了 `resolve` 属性，在 [extensions](https://webpack.js.org/configuration/resolve/#resolveextensions) 中定义好文件后缀名后，在 import 某个文件的时候，比如上面代码：

```
import App from './app'
```

就可以不加文件后缀名了。webpack 会按照定义的后缀名的顺序依次处理文件，比如上文配置 `['.tsx', '.ts', '.js', '.json']` ，webpack 会先尝试加上 `.tsx` 后缀，看找得到文件不，如果找不到就依次尝试进行查找，所以我们在配置时尽量把最常用到的后缀放到最前面，可以缩短查找时间。

这个时候再进行 `npm run start` ，页面就能正确输出了。

既然都用上了 Typescript，那 React 的类型声明自然不能少，安装它们：

```
npm install @types/react @types/react-dom -D
```

```json
{
  "compilerOptions": {
    // 基本配置
    "jsx": "react",                           // 在 .tsx 文件里支持 JSX
	"isolatedModules": true,
    // 模块解析选项
    "moduleResolution": "node",               // 指定模块解析策略
    "baseUrl": "./",                          // 根路径
    "paths": {								// 路径映射，与 baseUrl 关联
      "Src/*": ["src/*"],
      "Components/*": ["src/components/*"],
      "Utils/*": ["src/utils/*"]
    },

    // 实验性选项
    "experimentalDecorators": true,           // 启用实验性的ES装饰器
    "emitDecoratorMetadata": true,            // 给源码里的装饰器声明加上设计类型元数据

    // 其他选项
    "noEmit": true							// 只想使用tsc的类型检查作为函数时（当其他工具（例如Babel实际编译）时）使用它
  },
  "exclude": ["node_modules"]
}
```

接下来对 `compilerOptions` 重要配置做一下简单的解释：

- `target` 和 `module` ：这两个参数实际上没有用，它是通过 `tsc` 命令执行才能生成对应的 es5 版本的 js 语法，但是实际上我们已经使用 babel 去编译我们的 ts 语法了，根本不会使用 `tsc` 命令，所以它们在此的作用就是让编辑器提供错误提示。
- `isolatedModules` ：可以提供额外的一些语法检查。

比如不能重复 `export` ：

```
import { add } from './utils'
add()

export { add } // 会报错
```

比如每个文件必须是作为独立的模块：

```
const print = (str: string) => { console.log(str) } // 会报错，没有模块导出

// 必须有 export
export print = (str: string) => { 
  console.log(str) 
}
```

### baseUrl 和 Paths

首先 `baseUrl` 一定要设置正确，我们的 `tsconfig.json` 是放在项目根目录的，那么 `baseUrl` 设为 `./` 就代表了项目根路径。于是， `paths` 中的每一项路径映射，比如 `["src/*"]` 其实就是相对根路径。

如果大家像上面一样配置了，并自己尝试用以下方式开始进行模块的引入：

```
import Header from 'Components/Header'
```

因为 eslint 的原因，是会报错的：

这个时候需要改 `.eslintrc.js` 文件的配置了，首先得安装 [eslint-import-resolver-typescript](https://github.com/alexgorbatchev/eslint-import-resolver-typescript) ：

```
npm install eslint-import-resolver-typescript -D
```

然后在 `.eslintrc.js` 文件的 `setting` 字段修改为以下代码：

```
settings: {
  'import/resolver': {
    node: {
      extensions: ['.tsx', '.ts', '.js', '.json'],
    },
    typescript: {},
  },
},
```

是的，只需要添加 `typescript: {}` 即可，这时候再去看已经没有报错了。

但是上面我们完成的工作仅仅是对于编辑器来说可识别这个路径映射，我们需要在 `webpack.common.js` 中的 `resolve.alias` 添加相同的映射规则配置：

```js
module.exports = {
  // other...
  resolve: {
    extensions: ['.tsx', '.ts', '.js', '.json'],
    alias: {
      'Src': resolve(PROJECT_PATH, './src'),
      'Components': resolve(PROJECT_PATH, './src/components'),
      'Utils': resolve(PROJECT_PATH, './src/utils'),
    }
  },
  module: {//...},
  plugins: [//...],
}
```

现在，两者一致就可以正常开发和打包了！可能有的小伙伴会疑惑，我只配置 webpack 中的 alias 不就行了吗？虽然开发时会有报红，但并不会影响到代码的正确，毕竟打包或开发时 webpack 都会进行路径映射替换。是的，的确是这样，但是在 `tsconfig.json` 中配置，会给我们增加智能提示，比如我打字打到 `Com` ，编辑器就会给我们提示正确的 `Components` ，而且其下面的文件还会继续提示。

# 声明文件

https://ts.xcatliu.com/basics/declaration-files.html

https://segmentfault.com/a/1190000009247663#articleHeader6

```ts
declare module 'canvas2image' {
    const classes: any;
    export default classes;
  }
```

## 声明文件配置

1. 与该 npm 包绑定在一起。判断依据是 `package.json` 中有 `types` 字段，或者有一个 `index.d.ts` 声明文件。这种模式不需要额外安装其他包，是最为推荐的，所以以后我们自己创建 npm 包的时候，最好也将声明文件与 npm 包绑定在一起。
2. 发布到 `@types` 里。我们只需要尝试安装一下对应的 `@types` 包就知道是否存在该声明文件，安装命令是 `npm install @types/foo --save-dev`。这种模式一般是由于 npm 包的维护者没有提供声明文件，所以只能由其他人将声明文件发布到 `@types` 里了。

假如以上两种方式都没有找到对应的声明文件，那么我们就需要自己为它写声明文件了。由于是通过 `import` 语句导入的模块，所以声明文件存放的位置也有所约束，一般有两种方案：

1. 创建一个 `node_modules/@types/foo/index.d.ts` 文件，存放 `foo` 模块的声明文件。这种方式不需要额外的配置，但是 `node_modules` 目录不稳定，代码也没有被保存到仓库中，无法回溯版本，有不小心被删除的风险，故不太建议用这种方案，一般只用作临时测试。
2. 创建一个 `types` 目录，专门用来管理自己写的声明文件，将 `foo` 的声明文件放到 `types/foo/index.d.ts` 中。这种方式需要配置下 `tsconfig.json` 中的 `paths` 和 `baseUrl` 字段。

目录结构：

```autoit
/path/to/project
├── src
|  └── index.ts
├── types
|  └── foo
|     └── index.d.ts
└── tsconfig.json
```

`tsconfig.json` 内容：

```json
{
    "compilerOptions": {
        "module": "commonjs",
        "baseUrl": "./",
        "paths": {
            "*": ["types/*"]
        }
    }
}
```

如此配置之后，通过 `import` 导入 `foo` 的时候，也会去 `types` 目录下寻找对应的模块的声明文件了。

注意 `module` 配置可以有很多种选项，不同的选项会影响模块的导入导出模式。这里我们使用了 `commonjs` 这个最常用的选项，后面的教程也都默认使用的这个选项。

## 自动生成声明文件

如果库的源码本身就是由 ts 写的，那么在使用 `tsc` 脚本将 ts 编译为 js 的时候，添加 `declaration` 选项，就可以同时也生成 `.d.ts` 声明文件了。

我们可以在命令行中添加 `--declaration`（简写 `-d`），或者在 `tsconfig.json` 中添加 `declaration` 选项。这里以 `tsconfig.json` 为例：

```json
{
    "compilerOptions": {
        "module": "commonjs",
        "outDir": "lib",
        "declaration": true,
    }
}
```

上例中我们添加了 `outDir` 选项，将 ts 文件的编译结果输出到 `lib` 目录下，然后添加了 `declaration` 选项，设置为 `true`，表示将会由 ts 文件自动生成 `.d.ts` 声明文件，也会输出到 `lib` 目录下。

运行 `tsc` 之后，目录结构如下：

```autoit
/path/to/project
├── lib
|  ├── bar
|  |  ├── index.d.ts
|  |  └── index.js
|  ├── index.d.ts
|  └── index.js
├── src
|  ├── bar
|  |  └── index.ts
|  └── index.ts
├── package.json
└── tsconfig.json
```

在这个例子中，`src` 目录下有两个 ts 文件，分别是 `src/index.ts` 和 `src/bar/index.ts`，它们被编译到 `lib` 目录下的同时，也会生成对应的两个声明文件 `lib/index.d.ts` 和 `lib/bar/index.d.ts`。它们的内容分别是：

```ts
// src/index.ts

export * from './bar';

export default function foo() {
    return 'foo';
}
// src/bar/index.ts

export function bar() {
    return 'bar';
}
// lib/index.d.ts

export * from './bar';
export default function foo(): string;
// lib/bar/index.d.ts

export declare function bar(): string;
```

可见，自动生成的声明文件基本保持了源码的结构，而将具体实现去掉了，生成了对应的类型声明。

使用 `tsc` 自动生成声明文件时，每个 ts 文件都会对应一个 `.d.ts` 声明文件。这样的好处是，使用方不仅可以在使用 `import foo from 'foo'` 导入默认的模块时获得类型提示，还可以在使用 `import bar from 'foo/lib/bar'` 导入一个子模块时，也获得对应的类型提示。

除了 `declaration` 选项之外，还有几个选项也与自动生成声明文件有关，这里只简单列举出来，不做详细演示了：

- `declarationDir` 设置生成 `.d.ts` 文件的目录
- `declarationMap` 对每个 `.d.ts` 文件，都生成对应的 `.d.ts.map`（sourcemap）文件
- `emitDeclarationOnly` 仅生成 `.d.ts` 文件，不生成 `.js` 文件

## 修正 Tsc 打包产出的 Paths 别名

tsc 打包时不会修正 paths，需要额外做修正

> project-init 中 webpack 的 alias 则会被替换，此时的 tsconfig paths 只是起到一个类型提示的作用

问题是我的 wcli 不是用 webpack 打包的，纯粹就是使用 tsc 编译成 js 在 node 上执行的那怎么办呢？后面发现社区上提供了一个库 -`module-alias`。在配置完 tsconfig 的基础上只需要通过简单的两步即可实现。 在 `package.json` 配置跟 webpack alias 的路径指向然后在入口文件调用引用方法即可。

```json
// Aliases
"_moduleAliases": {
  "@root"      : ".", // Application's root
  "@deep"      : "src/some/very/deep/directory/or/file",
  "@my_module" : "lib/some-file.js",
  "something"  : "src/foo", // Or without @. Actually, it could be any string
}

// 在入口文件最顶端调用即可
require('module-alias/register')
```

一些另外的用法可以去官网查看即可，这里是 [**跳转连接**](https://link.juejin.cn/?target=https%3A%2F%2Fwww.npmjs.com%2Fpackage%2Fmodule-alias)。

> 本质就是做了一个字符串替换，所以也可以应用于其他类似的替换场景，替换别名

## 发布声明文件

当我们为一个库写好了声明文件之后，下一步就是将它发布出去了。

此时有两种方案：

1. 将声明文件和源码放在一起
2. 将声明文件发布到 `@types` 下

这两种方案中优先选择第一种方案。保持声明文件与源码在一起，使用时就不需要额外增加单独的声明文件库的依赖了，而且也能保证声明文件的版本与源码的版本保持一致。

仅当我们在给别人的仓库添加类型声明文件，但原作者不愿意合并 pull request 时，才需要使用第二种方案，将声明文件发布到 `@types` 下。

### 将声明文件和源码放在一起

如果声明文件是通过 `tsc` 自动生成的，那么无需做任何其他配置，只需要把编译好的文件也发布到 npm 上，使用方就可以获取到类型提示了。

如果是手动写的声明文件，那么需要满足以下条件之一，才能被正确的识别：

- 给 `package.json` 中的 `types` 或 `typings` 字段指定一个类型声明文件地址
- 在项目根目录下，编写一个 `index.d.ts` 文件
- 针对入口文件（`package.json` 中的 `main` 字段指定的入口文件），编写一个同名不同后缀的 `.d.ts` 文件

第一种方式是给 `package.json` 中的 `types` 或 `typings` 字段指定一个类型声明文件地址。比如：

```json
{
    "name": "foo",
    "version": "1.0.0",
    "main": "lib/index.js",
    "types": "foo.d.ts",
}
```

指定了 `types` 为 `foo.d.ts` 之后，导入此库的时候，就会去找 `foo.d.ts` 作为此库的类型声明文件了。

`typings` 与 `types` 一样，只是另一种写法。

如果没有指定 `types` 或 `typings`，那么就会在根目录下寻找 `index.d.ts` 文件，将它视为此库的类型声明文件。

如果没有找到 `index.d.ts` 文件，那么就会寻找入口文件（`package.json` 中的 `main` 字段指定的入口文件）是否存在对应同名不同后缀的 `.d.ts` 文件。

比如 `package.json` 是这样时：

```json
{
    "name": "foo",
    "version": "1.0.0",
    "main": "lib/index.js"
}
```

就会先识别 `package.json` 中是否存在 `types` 或 `typings` 字段。发现不存在，那么就会寻找是否存在 `index.d.ts` 文件。如果还是不存在，那么就会寻找是否存在 `lib/index.d.ts` 文件。假如说连 `lib/index.d.ts` 都不存在的话，就会被认为是一个没有提供类型声明文件的库了。

有的库为了支持导入子模块，比如 `import bar from 'foo/lib/bar'`，就需要额外再编写一个类型声明文件 `lib/bar.d.ts` 或者 `lib/bar/index.d.ts`，这与自动生成声明文件类似，一个库中同时包含了多个类型声明文件。

### 将声明文件发布到 `@types` 下

如果我们是在给别人的仓库添加类型声明文件，但原作者不愿意合并 pull request，那么就需要将声明文件发布到 `@types` 下。

与普通的 npm 模块不同，`@types` 是统一由 [DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped/) 管理的。要将声明文件发布到 `@types` 下，就需要给 [DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped/) 创建一个 pull-request，其中包含了类型声明文件，测试代码，以及 `tsconfig.json` 等。

pull-request 需要符合它们的规范，并且通过测试，才能被合并，稍后就会被自动发布到 `@types` 下。

在 [DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped/) 中创建一个新的类型声明，需要用到一些工具，[DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped/) 的文档中已经有了 [详细的介绍](https://github.com/DefinitelyTyped/DefinitelyTyped#create-a-new-package)，这里就不赘述了，以官方文档为准。

如果大家有此类需求，可以参考下笔者 [提交的 pull-request](https://github.com/DefinitelyTyped/DefinitelyTyped/pull/30336/files) 。

# Ts-nocheck

You can suppress errors in .ts files using `// @ts-ignore` comments for lines

or use `// @ts-nocheck` after version 3.7 for the whole file.

# 为第三方模块书写声明文件

[Typescript 书写声明文件（可能是最全的） - 掘金 (juejin.cn)](https://juejin.cn/post/6844904034621456398#heading-8)

[巧妙利用TypeScript模块声明帮助你解决声明拓展 - 知乎](https://zhuanlan.zhihu.com/p/542379032)

随便在项目里面加一个 d.ts 就可以了

```ts
// draggable.ts
import {__serializeForClipboard, EditorView} from 'prosemirror-view';
// 注意: __serializeForClipboard 是库的内部方法
declare module 'prosemirror-view' {
    // eslint-disable-next-line @typescript-eslint/no-shadow
    function __serializeForClipboard(view: any, slice: any): {
        dom: any;
        text: any;
    };
}
```
