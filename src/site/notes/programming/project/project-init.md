---
{"dg-publish":true,"permalink":"/programming/project/project-init/"}
---


参考一：[我是这样搭建Typescript+React项目环境的！（2.7w字详解）](https://github.com/vortesnail/blog/issues/14#)

参考二：https://github.com/gwuhaolin/dive-into-webpack/blob/master/4%E4%BC%98%E5%8C%96/4-7%E5%8C%BA%E5%88%86%E7%8E%AF%E5%A2%83.html

项目初始化相关的都放在这里

webpack 可以放 webpack 的 webpack 的核心概念；编译过程；依赖分析原理；tapable 机制，与其他编译工具的对比；webpack 5 的新特性等

eslint 可以放：ESLint 的基本使用与原理、ESLint 插件怎么写

```json
"todohighlight.keywords": [
    "DEBUG:",
    "REVIEW:",
    {
      "text": "NOTE:",
      "color": "#ff0000",
      "backgroundColor": "yellow",
      "overviewRulerColor": "grey"
    }
  ],
```

# Npm Init

## 项目基础

### 新建 Git 仓库

参考 git

### package.json

每一个项目都需要一个 `package.json` 文件，它的作用是记录项目的配置信息，比如我们的项目名称、包的入口文件、项目版本等，也会记录所需的各种依赖，还有很重要的 `script` 字段，它指定了运行脚本命令的 `npm` 命令行缩写。

通过以下命令就能快速生成该文件：

```
npm init
```

通过修改生成的默认配置，现在的内容如下：

```json
{
  "name": "react-ts-quick-starter",
  "version": "1.0.0",
  "description": "Quickly create react + typescript project development environment and scaffold for developing npm package components",
  "main": "index.js",
  "scripts": {},
  "repository": {
    "type": "git",
    "url": "git+https://github.com/vortesnail/react-ts-quick-starter.git"
  },
  "keywords": ["react-project", "typescript-project", "react-typescript", "react-ts-quick-starter"],
  "author": {
    "name": "vortesnail",
    "url": "https://github.com/vortesnail",
    "email": "1091331061@qq.com"
  },
  "license": "MIT",
  "bugs": {
    "url": "https://github.com/vortesnail/react-ts-quick-starter/issues"
  },
  "homepage": "https://github.com/vortesnail/react-ts-quick-starter#readme"
}

```

暂时修改了以下配置：

- `description` ：增加了对该项目的描述，github 进行 repo 搜索时，关键字匹配会使你的项目更容易被搜索到。
- `scripts` ：把默认生成的删了，没啥用。
- `keywords` ：增加了项目关键字，其他开发者在 npm 上搜索的时候，适合的关键字能你的包更容易被搜索到。
- `author` ：添加了更具体的作者信息。
- `license` ：修改为 [MIT](https://opensource.org/licenses/MIT) 协议。

### LICENSE

我们在建仓库的时候会有选项让我们选择开源协议，我当时就选了 MIT 协议，如果没选的也不要紧，去网站 [choosealicense](http://choosealicense.online/) 选择合适的 license（一般会选宽松的 MIT 协议），复制到项目根目录下的 `LICENSE` 文件内即可，然后修改作者名和年份，如下：

### .gitignore

所有不需要上传至 git 仓库的都要添加进来，比如我们常见的 `build` 、 `dist` 等，还有操作系统默认生成的，比如 MacOs 会生成存储项目文件夹显示属性的 `DS_Store` 文件。

使用 vscode 的 [gitignore](https://marketplace.visualstudio.com/items?itemName=codezombiech.gitignore) 插件，下载安装该插件之后， `ctrl+shift+p` 召唤命令面板，输入 `Add gitignore` 命令，即可在输入框输入系统或编辑器名字，来自动添加需要忽略的文件或文件夹至 `.gitignore` 中。

我添加了以下： `Node` 、 `Windows` 、 `MacOS` 、 `SublimeText` 、 `Vim` 、 `Vscode` ，大家酌情添加吧。如果默认中没有的，可自行手动输入至 `.gitignore` 中，比如我自己加了 `dist/` 和 `build/` ，用于忽略之后 webpack 打包生成的文件。

```
node_modules
dist
```

### readme.md

## ESLint

### 基本

```
npm install eslint -D
```

安装成功后，执行以下命令：

```
npx eslint --init
```

在漫长的安装结束后，项目根目录下多出了新的文件 `.eslintrc.js` ，这便是我们的 `eslint` 配置文件了。其默认内容如下：

```js
export default {
  env: {
    browser: true,
    es2020: true,
    node: true,
  },
  extends: ['plugin:react/recommended', 'airbnb'],
  parser: '@typescript-eslint/parser',
  parserOptions: {
    ecmaFeatures: {
      jsx: true,
    },
    ecmaVersion: 11,
    sourceType: 'module',
  },
  plugins: ['react', '@typescript-eslint'],
  rules: {},
}
```

`extends` 和 `plugins` 的关系，其实 `plugins` 就是 **插件** 的意思，都是需要 npm 包的安装才可以使用，只不过默认支持简写，官网都有说；至于 `extneds` 其实就是使用我们已经下载的插件的某些预设规则。

现在我们对该配置文件作以下修改：

- 根据 [eslint-config-airbnb](https://www.npmjs.com/package/eslint-config-airbnb) 官方说明，如果要开启 React Hooks 的检查，需要在 extends 中添加一项 `'airbnb/hooks'` 。
- [eslint-config-airbnb-base@latest](https://github.com/airbnb/javascript/tree/master/packages/eslint-config-airbnb-base) ，后端项目使用这个没有 react 相关规则的配置即可
- 根据 [@typescript-eslint/eslint-plugin](https://www.npmjs.com/package/@typescript-eslint/eslint-plugin) 官方说明，在 extends 中添加 `'plugin:@typescript-eslint/recommended'` 可开启针对 ts 语法推荐的规则定义。

需要添加一条很重要的 `rule` ，不然在 `.ts` 和 `.tsx` 文件中引入另一个文件模块会报错，比如：

```js
rules: {
  'import/extensions': [
    ERROR,
    'ignorePackages',
    {
      ts: 'never',
      tsx: 'never',
      json: 'never',
      js: 'never',
    },
  ],
}
```

在之后我们安装 `typescript` 之后，会出现以下的怪异错误：

![image-20210902164012630](/img/user/programming/project/project-init/image-20210902164012630.png)

大家先添加以下配置，毕竟之后一定要安装 `typscript` 的，把最常用的扩展名排在最前面，这里寻找文件时最快能找到：

```js
  settings: {
    'import/resolver': {
      node: {
        extensions: ['.tsx', '.ts', '.js', '.json'],
      },
    },
  },
```

接下来安装 2 个社区中比较火的 `eslint` 插件：

- `eslint-plugin-promise` ：让你把 Promise 语法写成最佳实践。
- `eslint-plugin-unicorn` ：提供了更多有用的配置项，比如我会用来规范关于文件命名的方式
- `eslint-plugin-import`

```
npm install eslint-plugin-promise eslint-plugin-unicorn -D
```

更多配置请参考 scaffold

### Vscode 支持 Eslint 自动修复

我们知道 `eslint` 由编辑器支持是有自动修复功能的，首先我们需要安装扩展：eslint

再到之前创建的 `.vscode/settings.json` 中添加以下代码：

```json
{
  "eslint.validate": ["javascript", "javascriptreact", "typescript", "typescriptreact"],
  "typescript.tsdk": "./node_modules/typescript/lib", // 代替 vscode 的 ts 语法智能提示
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true,
  },
}
```

不过有时候我们并不希望 `ESLint` 或 `Prettier` 去对某些文件做任何修改，比如某个特定的情况下我想去看看打包之后的文件内容，里面的内容一定是非常不符合各种 lint 规则的，但我不希望按保存时对其进行格式化，此时就需要我们添加 `.eslintignore` 和 `.prettierignore` ，我一般会使这两个文件的内容都保持一致：

```
/node_modules
/build
/dist
```

### Js 和 Ts 混用问题

特征是项目代码都是 ts 的，但是工具链配置文件都是 js，而且是 commonjs 模块的风格

需要 overwrite，针对工具链配置文件忽略一些 rule

```js
    overrides: [
        {
            files: ['**/*.js'],
            rules: {
                'unicorn/prefer-module': OFF,
            },
        },
    ],
```

## StyleLint

官网：https://stylelint.io/

中文文档：https://cloud.tencent.com/developer/section/1489630

更好的中文文档：https://stylelint.docschina.org/user-guide/configuration/

```
npm install stylelint stylelint-config-standard -D
```

```js
module.exports =  {
    extends: ['stylelint-config-standard'],
    rules: {
        'comment-empty-line-before': null,
        'declaration-empty-line-before': null,
        'function-name-case': 'lower',
        'no-descending-specificity': null,
        'no-invalid-double-slash-comments': null,
        'rule-empty-line-before': 'always',
    },
    ignoreFiles: ['node_modules/**/*', 'build/**/*'],
}
```

同样，简单介绍下配置上的三个属性：

- `extends` ：其实和 `eslint` 的类似，都是扩展，使用 `stylelint` 已经预设好的一些规则。
- `rules` ：就是具体的规则，如果默认的你不满意，可以自己决定某个规则的具体形式。
- `ignoreFiles` ：不像 `eslint` 需要新建 ignore 文件， `stylelint` 配置就支持忽略配置字段，我们先添加 `node_modules` 和 `build` ，之后有需要大家可自行添加。

与 `eslint` 一样，想要在编辑代码时有错误提示以及自动修复功能，我们需要 vscode 安装一个扩展：styleint

并且在 `.vscode/settings.json` 中增加以下代码：

```json
{
	// 使用 stylelint 自身的校验即可
  "css.validate": false,
  "less.validate": false,
  "scss.validate": false,
  
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    // 同时支持了eslint和stylelint
    "source.fixAll.eslint": true,
  },
}
```

我们可以在社区下载一些优秀的 `stylelint extends` 和 `stylelint plugins` ：

- [stylelint-order](https://github.com/hudochenkov/stylelint-order)：order 配置的基础库，允许通过数组的形式自定义声明顺序
- [stylelint-config-hudochenkov](https://github.com/hudochenkov/stylelint-config-hudochenkov)：基于 stylelint-order，维护的比较勤快的一个库
- [stylelint-declaration-block-no-ignored-properties](https://github.com/kristerkari/stylelint-declaration-block-no-ignored-properties) 用于提示我们写的矛盾样式，比如下面的代码中 `width` 是会被浏览器忽略的，这可以避免我们犯一些低级错误～ `{ display: inline; width: 100px; }`。暂时没有跟进 stylelint 14

```
npm install stylelint-order stylelint-config-hudochenkov stylelint-declaration-block-no-ignored-properties -D
```

现在更改一下我们的配置文件：

```js
module.exports = {
  extends: ['stylelint-config-standard', 'stylelint-config-hudochenkov/full'],
  plugins: ['stylelint-order', 'stylelint-declaration-block-no-ignored-properties'],
  rules: {
    'plugin/declaration-block-no-ignored-properties': true,
    'comment-empty-line-before': null,
    'declaration-empty-line-before': null,
    'function-name-case': 'lower',
    'no-descending-specificity': null,
    'no-invalid-double-slash-comments': null,
    'rule-empty-line-before': 'always',
  },
  ignoreFiles: ['node_modules/**/*', 'build/**/*'],
}
```

## Prettier @deprecated

### 配置

如果说 `EditorConfig` 帮你统一编辑器风格，那 `Prettier` 就是帮你统一项目风格的。 `Prettier` 拥有更多配置项（实际上也不多，数了下二十个），且能在发布流程中执行命令自动格式化，能够有效的使项目代码风格趋于统一

```
npm install prettier -D
```

安装 vscode prettier 扩展

安装成功之后在根目录新建文件 `.prettierrc` ，输入以下配置：

```js
{
  "trailingComma": "all",
  "tabWidth": 2,
  "semi": false,
  "singleQuote": true,
  "endOfLine": "lf",
  "printWidth": 120,
  "bracketSpacing": true,
  "arrowParens": "always"
}
```

其实 `Prettier` 的配置项很少，大家可以去 [Prettier Playground](https://prettier.io/playground/) 大概把玩一会儿，下面我简单介绍下上述的配置：

- `trailingComma` ：对象的最后一个属性末尾也会添加 `,` ，比如 `{ a: 1, b: 2 }` 会格式为 `{ a: 1, b: 2, }` 。
- `tabWidth` ：缩进大小。
- `semi` ：分号是否添加，我以前从 C++ 转前端的，有一段时间非常不能忍受不加分号的行为，现在香的一匹。
- `singleQuote` ：是否单引号，绝壁选择单引号啊，不会真有人还用双引号吧？不会吧！😏
- `jsxSingleQuote` ：jsx 语法下是否单引号，同上。
- `endOfLine` ：与 `.editorconfig` 保持一致设置。
- `printWidth` ：单行代码最长字符长度，超过之后会自动格式化换行。
- `bracketSpacing` ：在对象中的括号之间打印空格， `{a: 5}` 格式化为 `{ a: 5 }` 。
- `arrowParens` ：箭头函数的参数无论有几个，都要括号包裹。比如 `(a) => {}` ，如果设为 `avoid` ，会自动格式化为 `a => {}`

### setting.json

```json
{
      // 指定哪些文件不参与搜索
    "search.exclude": {
        "**/node_modules": true,
        "dist": true,
        "yarn.lock": true
    },
    // 启动 formatter 的 格式化功能，区别于 linter 的 fix 功能
    "editor.formatOnSave": true,
    "[javascript]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode"
    },
    "[javascriptreact]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode"
    },
    "[typescript]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode"
    },
    "[typescriptreact]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode"
    },
    "[json]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode"
    },
    "[html]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode"
    },
    "[markdown]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode"
    },
    "[css]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode"
    },
    "[less]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode"
    },
    "[scss]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode"
    }
}
```

## 解决冲突 @deprecated

基本原则：凡是代码格式相关统一交给 prettier，eslint 和 style 不在配置相关的 rule

### Eslint-config-prettier

官方提供了很好的解决方案，查阅 [Integrating with Linters](https://prettier.io/docs/en/integrating-with-linters.html) 可知，针对 `eslint` 和 `stylelint` 都有很好的插件支持，其原理都是禁用与 `prettier` 发生冲突的规则。

> https://stackoverflow.com/questions/44690308/whats-the-difference-between-prettier-eslint-eslint-plugin-prettier-and-eslint

安装插件 [eslint-config-prettier](https://github.com/prettier/eslint-config-prettier) ，这个插件会禁用所有和 prettier 起冲突的规则：

```
npm install eslint-config-prettier -D
```

添加以下配置到 `.eslintrc.js` 的 `extends` 中：

```js
{
  extends: [
    // other configs ...
    // All configs have been merged into one
   	'prettier',
  ]
}
```

这里需要注意， `'prettier'` 及之后的配置要放到原来添加的配置的后面，这样才能让 `prettier` 禁用之后与其冲突的规则。

### Eslint-plugin-prettier

Plugins usually contain implementations for additional rules that ESLint will check for. This plugin uses Prettier under the hood and will raise ESLint errors when your code differs from Prettier's expected output.

该插件实现了额外的 eslint 规则，将 prettier 的规则作为 eslint 的一部分去使用，作用仅仅是提示，说明这些 prettier 的 eslint rule，没有实现 --fix，还是需要使用 formatter

```
npm i eslint-plugin-prettier -D
```

```js
{
  plugins: ['prettier'],
  rules: {
    'prettier/prettier': ERROR
  }
}
```

### Stylelint

`stylelint` 的冲突解决也是一样的，先安装插件 [stylelint-config-prettier](https://github.com/prettier/stylelint-config-prettier) ：

```js
npm install stylelint-config-prettier -D
```

添加以下配置到 `.stylelintrc.js` 的 `extends` 中：

```js
{  
	extends: [
  	// other configs ...
    'stylelint-config-prettier'
  ]
}
```

再安装

```
npm install --save-dev stylelint-prettier
```

```
{
  "plugins": ["stylelint-prettier"],
  "rules": {
    "prettier/prettier": true
  }
}
```

## Prefer

https://eslint.org/docs/rules/

### Eslint

```js
rule: {
    /**
     * 代码格式相关的规则，代替prettier
     */
    'indent': [ERROR, 4, { SwitchCase: 1 }],
    // 空格相关的规则
    'keyword-spacing': ERROR,
    'object-curly-spacing': [ERROR, 'always'],
    'lines-between-class-members': [ERROR, 'always'],
    // 同一个对象内部，是否使用单引号、双引号保持一致即可
    'quote-props': [ERROR, 'consistent'],
    'semi': [ERROR, 'always'],
    'max-len': [ERROR, 120],
    'quotes': [ERROR, 'single'],
    'jsx-quotes': [ERROR, 'prefer-single'],
    'brace-style': [ERROR, 'stroustrup', { 'allowSingleLine': false }],
    // 换行符，不同的系统不一样，不做要求
    'linebreak-style': OFF,
    // 控制对象、数组的换行，要么全部换行，要么全部不换行，保持一致即可
    'object-curly-newline': [ERROR, { consistent: true }],
    'array-bracket-newline': [ERROR, 'consistent'],
    'array-element-newline': [ERROR, 'consistent'],
    'no-multiple-empty-lines': [
        ERROR,
        {
            'max': 1,
            'maxBOF': 0,
            // 与 eol-last 规则保持一致
            'maxEOF': 1,
        },
    ],
}
```

使用我自己的 eslint 配置

https://github.com/antfu/eslint-config

https://zhuanlan.zhihu.com/p/572527461

### Prettier

```js
{
    "singleQuote": true,
    "quoteProps": "consistent",
}
```

换行问题，prettier 总是无脑在一行：https://github.com/prettier/prettier/issues/2716

prettier 的经验值控制换行，如果结构比较复杂的话，也还是回换行的，只是会完全由 prettier 控制，而 prettier 控制是否换行的基础是 printWidth，只能把 printWidth 调小一点，让他格式化，之后别再触发 formatter

## Lint 命令

我们在 `package.json` 的 `scripts` 中增加以下三个配置：

```json
{
	scripts: {
  	"lint": "npm run lint-eslint && npm run lint-stylelint",
    "lint-eslint": "eslint --color -c .eslintrc.js --ext .ts,.tsx,.js src",
    "lint-stylelint": "stylelint --config .stylelintrc.js src/**/*.{less,css,scss}"
  }
}
```

在控制台执行 `npm run lint-eslint` 时，会去对 `src/` 下的指定后缀文件进行 `eslint` 规则检测， `lint-stylelint` 也是同理， `npm run lint` 会两者都按顺序执行。

其实我个人来说，这几个命令我是都不想写进 `scripts` 中的，因为我们写代码的时候，不规范的地方就已经自动修复了，只要保持良好的习惯，看到有爆红线的时候想办法去解决它，而不是视而不见，那么根本不需要对所有包含的文件再进行一次命令式的规则校验。

但是对于新提交缓存区的代码还是有必要执行一次校验的，这个后面会说到。

## Husky & Lint-staged

在项目开发过程中，每次提交前我们都要对代码进行格式化以及 `eslint` 和 `stylelint` 的规则校验，以此来强制规范我们的代码风格，以及防止隐性 BUG 的产生。

那么有什么办法只对我们 git 缓存区最新改动过的文件进行以上的格式化和 lint 规则校验呢？答案就是 [lint-staged](https://github.com/okonet/lint-staged) 。

我们还需要另一个工具 [husky](https://github.com/typicode/husky) ，它会提供一些钩子，比如执行 `git commit` 之前的钩子 `pre-commit` ，借助这个钩子我们就能执行 `lint-staged` 所提供的代码文件格式化及 lint 规则校验！

```
npm install husky@3 lint-staged -D
```

随后在 `package.json` 中添加以下代码（位置随意，我比较习惯放在 `repository` 上面）：

```json
{
	"husky": {
    "hooks": {
      "pre-commit": "lint-staged",
    }
  },
  "lint-staged": {
    "*.{ts,tsx,js}": [
      "eslint --color --config .eslintrc.js"
    ],
    "*.{css,less,scss}": [
      "stylelint --config .stylelintrc.js"
    ],
    "*.{ts,tsx,js,json,html,yml,css,less,scss,md}": [
      "prettier --write"
    ]
  },
}
```

首先，我们会对暂存区后缀为 `.ts .tsx .js` 的文件进行 `eslint` 校验， `--config` 的作用是指定配置文件。之后同理对暂存区后缀为 `.css .less .scss` 的文件进行 `stylelint` 校验，注意⚠️，我们没有添加 `--fix` 来自动修复不符合规则的代码，因为自动修复的内容对我们不透明，你不知道哪些代码被更改，这对我来说是无法接受的。

但是在使用 `prettier` 进行代码格式化时，完全可以添加 `--write` 来使我们的代码自动格式化，它不会更改语法层面上的东西，所以无需担心。

### @7 新版操作

在 package.json 似乎没有办法初始化脚本了

Edit `package.json > prepare` script and run it once: `npm run prepare`

```
npx husky add .husky/pre-commit "lint-staged"
git add .husky/pre-commit

npx husky add .husky/commit-msg "commitlint --config .commitlintrc.js -E HUSKY_GIT_PARAMS"
git add .husky/commit-msg
```

但是无法执行 lint-staged

### @3

安装@3 版本就好了，相关脚本可以正常安装

## Commitlint + Changelog

建议阅读 [Commit message 和 Change log 编写指南（阮一峰）](http://www.ruanyifeng.com/blog/2016/01/commit_message_change_log.html)

继续参考：https://github.com/vortesnail/blog/issues/14#

```
npm install @commitlint/cli @commitlint/config-conventional -D
```

[@commitlint/config-conventional](https://github.com/conventional-changelog/commitlint/tree/master/@commitlint/config-conventional) 类似 `eslint` 配置文件中的 `extends` ，它是官方推荐的 angular 风格的 commitlint 配置，提供了少量的 lint 规则，默认包括了以下除了我自己新增的 `type` 。

随后在根目录新建文件 `.commitlintrc.js` ，这就是我们的 commitlint 配置文件，写入以下代码：

```js
module.exports = {
  extends: ['@commitlint/config-conventional']
}
```

随后回到 `package.json` 的 `husky` 配置，增加一个钩子：

```json
{
  "husky": {
    "hooks": {
      "pre-commit": "lint-staged",
      "commit-msg": "commitlint --config .commitlintrc.js -E HUSKY_GIT_PARAMS"
    }
  },
}
```

`E HUSKY_GIT_PARAMS` 简单理解就是会拿到我们的 message ，然后 commitlint 再去进行 lint 校验。

接着配置生成我们的 changelog ，首先安装依赖：

```
npm install conventional-changelog-cli -D
```

在 `package.json` 的 `scripts` 下增加一个命令：

```
{
  "scripts": {
    "changelog": "conventional-changelog -p angular -i CHANGELOG.md -s"
  },
}
```

之后就可以通过 `npm run changelog` 生成 angular 风格的 changelog ，需要注意的是，上面这条命令产生的 changelog 是基于上次 tag 版本之后的变更（feat、fix 等等）所产生的。

现在就来测试一下我们上面的工作有没有正常运行吧！执行以下提交信息不规范（chore 写成 chora）的命令：

```
# 提交所有变化到缓存区
git add -A
# 把暂存区的所有修改提交到分支 
git commit -m "chora: add commitlint to force commit style"
```

### 设置 Commit 模板

```
git config commit.template ./ci_template
```

在项目的本地终端执行即可

也可以加上 --global 参数设置全局模板，只是这样的话不方便团队统一

# Rollup Init

## Rollup or Webpack

### Rollup

- rollup 配置少，开箱即用，支持 treeshaking，打包后比较干净
- 适合构建第三方库

### Webpack

- rollup 能做的 webpack 也能做，但是添加额外的内容较多，配置相对繁琐
- 适合构建 webapp

## 所需插件

```js
import typescript from 'rollup-plugin-typescript2'; // 处理typescript
import babel from 'rollup-plugin-babel'; // 处理es6
import resolve from '@rollup/plugin-node-resolve'; // 你的包用到第三方npm包
import commonjs from '@rollup/plugin-commonjs'; // 你的包用到的第三方只有commonjs形式的包
import builtins from 'rollup-plugin-node-builtins'; // 如果你的包或依赖用到了node环境的builtins fs等
import globals from 'rollup-plugin-node-globals'; // 如果你的包或依赖用到了globals变量
import { terser } from 'rollup-plugin-terser'; // 压缩，可以判断模式，开发模式不加入到plugins
```

## 发布配置

```js
export default {
  input: 'src/index.ts', // 源文件入口
  output: [
    {
      file: 'dist/index.esm.js', // package.json 中 "module": "dist/index.esm.js"
      format: 'esm', // es module 形式的包， 用来import 导入， 可以tree shaking
      sourcemap: true
    }, {
      file: 'dist/index.cjs.js', // package.json 中 "main": "dist/index.cjs.js",
      format: 'cjs', // commonjs 形式的包， require 导入 
      sourcemap: true
    }, {
      file: 'dist/index.umd.js',
      name: 'GLWidget',
      format: 'umd', // umd 兼容形式的包， 可以直接应用于网页 script
      sourcemap: true
    }
  ],
  plugins: plugins
}
```

这样就可以同时发布 3 种格式的包供其他人选择使用

## 发布 Ts 声明文件

tsconfig.json

```json
{
  "compilerOptions": {
    "declaration": true // 生成*.d.ts
    ...
  }
  ...
}
```

如果 rollup-plugin-typescript2 没有额外配置的话，会在 dist 文件夹生成对应的声明文件，在 package.json 中指定 types 字段，那其他人用 typescript 开发时就可以获取提示了

```json
{
  "types": "dist/index.d.ts"
  ...
}
```

## 自定义插件

在开发中我有个需求，想要模块化 glsl，这样可以更好的组织 shader 代码，另一方面编写 glsl 文件可以获得编辑器的提示和高亮辅助

### 已有方案

现在有比较知名的库 glslify 来做模块化，我为什么没用呢

- 因为我想做 webgl 库,把 glslify 整个打包进来没有必要，我的 glsl 应该在编译好后就不会有太大变更了

那怎么不用 glslify 的 rollup 插件在打包阶段解决呢

- glslify 其实做的是模块化，我的需求更多的是类似 include 把代码插入的功能

那听起来和 scss 做的模块化类似，可以@import 变量进来，那我就仿照 scss 的方式写一个简单的 rollup 插件解决自己的需求

### 插件形式

```js
function includeText(userOptions = {}) {
  return {
    name: 'include-text',
    async transform(source, id) { // hooks
      let transformedCode = xxx(souce) // 按你的方式改变code
      return { code: `export default ${JSON.stringify(transformedCode.toString())}`, map: { mappings: '' }};
    }
  }
module.exports = includeText;
```

#### 主要功能

我主要用了 transform 这个 hook，source 是代码，id 是这个代码对应的文件，我要做的就是

- 找到代码里的@import "**/*.glsl";
- 递归把代码替换到相应的位置
- 压缩 glsl 代码，去掉注释、空行、代码段的回车等
- 因为 glsl 代码不是 js，为了后续正常处理，将代码转成 string，export 出去当做 js 的 string 变量来处理

#### 监听文件变化

到这里基本功能就完成了，在使用中会发现，你修改 js 中 import 的 glsl 中代码，是可以触发自动编译打包的，但是 glsl 中 import 的 glsl 文件是无法触发的，那么就要用到 addWatchFile 这个 api

```js
async transform(source, id) {
  this.addWatchFile(xxx)
}
```

递归的将所有找到的@import 文件全部进行 addWatchFile 操作

## 总结

以上就是在 GLWidget 项目中用到 rollup 相关内容，完成了用 typescript 编写，发布 3 种形式 npm 包的步骤，在过程中编写了处理 glsl 文件的插件。

# Webpack Init

重点在于入口、出口、loader、plugins，这几个顶级属性配好了就可以启动了。

其他的是后期要优化的，比如热更新、代码分离等。

## 基础开发环境

### 基础

`npm install --save-dev webpack webpack-cli `

新建 config 文件夹，新建 webpack.base.conf.js

```js
const path = require('path')

module.exports = {
  // 入口起点，从项目的根目录开始读取路径，而不是配hi文件所在的config目录
  entry: {
    app: './src/index.js',
  },
  // 输出
  output: {
    // 使用了path包，是相对路径，从本文件开始
    path: path.resolve(__dirname, '../dist'),
    filename: "[name].[contenthash].js",
    publicPath: '/',
    // webpack 默认通过箭头函数包裹打包内容实现作用域
    environment: {
        arrowFunction: false
    }
  },
  module: {
    rules: [
    ],
  },
  // 代码模块路径解析的配置
  resolve: {
	// 自动添加模块后缀名
    extensions: [".wasm", ".mjs", ".js", ".json", ".jsx"],
  },
  plugins: [
  ],
}
```

### html-webpack-plugin：关联 HTML

`webpack` 默认从作为入口的 `.js` 文件进行构建（更多是基于 `SPA` 去考虑），但通常一个前端项目都是从一个页面（即 HTML）出发的，最简单的方法是，创建一个 HTML 文件，使用 `script` 标签直接引用构建好的 JS 文件，如…

```html
<script src="./dist/bundle.js"></script>
```

但是，如果我们的文件名或者路径会变化，例如使用 `[hash]` 来进行命名，那么最好是将 `HTML` 引用路径和我们的构建结果关联起来，这个时候我们可以使用 `html-webpack-plugin`

`html-webpack-plugin` 是一个独立的 `node package`，所以在使用之前我们需要先安装它，把它安装到项目的开发依赖中

`npm install --save-dev html-webpack-plugin`

然后在 `webpack` 配置中，将 `html-webpack-plugin` 添加到 `plugins` 列表中

```js
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
  // ...
  plugins: [
    new HtmlWebpackPlugin({
      filename: 'index.html', // 配置输出文件名和路径
      template: 'public/index.html', // 配置文件模板，也就是入口
      inject: 'body', // inject
    }),
  ],
}
```

这样配置好之后，构建时 `html-webpack-plugin` 会为我们创建一个 `HTML` 文件，其中会引用构建出来的 JS 文件。实际项目中，默认创建的 `HTML` 文件并没有什么用，我们需要自己来写 `HTML` 文件，可以通过 `html-webpack-plugin` 的配置，传递一个写好的 HTML 模板…

这样，通过 `html-webpack-plugin` 就可以将我们的页面和构建 `JS` 关联起来，回归日常，从页面开始开发。如果需要添加多个页面关联，那么实例化多个 `html-webpack-plugin`， 并将它们都放到 `plugins` 字段数组中就可以了… @@@

**配置参数**

**filename**：**输出** 文件的文件名称，默认为 **index.html**，不配置就是该文件名；此外，还可以为输出文件指定目录位置（例如 'html/index.html'）

- filename 配置的 html 文件目录是相对于 webpackConfig.output.path 路径而言的，不是相对于当前项目目录结构的。
- 指定生成的 html 文件内容中的 `link` 和 `script` 路径是相对于 **生成目录** 下的，写路径的时候请写生成目录下的相对路径。

**template**: **本地模板文件** 的位置，支持加载器 (如 handlebars、ejs、undersore、html 等)，如比如 `handlebars!src/index.hbs`；

- 用于指定入口 html ，路径相对于根路径
- template 配置项在 html 文件使用 `file-loader` 时，其所指定的位置找不到，导致生成的 html 文件内容不是期望的内容。
- 为 template 指定的模板文件没有指定 **任何 loader 的话**，默认使用 `ejs-loader`。如 `template: './index.html'`，若没有为 `.html` 指定任何 loader 就使用 `ejs-loader`

**inject**：向 *template* 或者 *templateContent* 中注入所有静态资源，不同的配置值注入的位置不经相同。

- **true 或者 body**：所有 **JavaScript** 资源插入到 body 元素的底部
- **head**: 所有 **JavaScript** 资源插入到 head 元素中
- **false**： 所有静态资源 css 和 JavaScript 都不会注入到模板文件中

从这里的配置可以看出，想要启动项目，我们需要一个 html 模板，一个入口 js

### 启用静态服务

我们可以使用 `webpack-dev-server` 在本地开启一个简单的静态服务来进行开发

`npm install --save-dev webpack-dev-server`

webpack.config.js

```js
module.exports = {
    // 开发服务器
    devServer: {
        static: false, // 默认 dev-server 会为根文件夹提供本地服务器，如果想为另外一个目录下的文件提供本地服务器，应该在这里设置其所在目录，设置为 false 禁用
        historyApiFallback: true, // 在开发单页应用时非常有用，它依赖于HTML5 history API，如果设置为true，所有的跳转将指向index.html
        open: true, // 自动打开浏览器
        compress: true, // 启用gzip压缩
        hot: true, // 模块热更新，取决于HotModuleReplacementPlugin
        host: '127.0.0.1', // 设置默认监听域名，如果省略，默认为“localhost”
        port: 8080, // 设置默认监听端口，如果省略，默认为“8080”
        devMiddleware: {
            stats: 'errors-only', // 控制终端仅打印 error
        },
        client: {
            logging: 'error', // 控制浏览器控制台显示的信息
            overlay: true, // Shows a full-screen overlay in the browser when there are compiler errors or warnings
            progress: true, // 将运行进度输出到控制台
        },
    },
  // ...
  plugins: [
  ],
} 

```

一些配置项的说明：

+ inline：设置为 true，当源文件改变时会自动刷新页面，在 webpack5 中默认支持
+ `stats`：当设为 `error-only` 时，终端中只会打印错误日志，这个配置个人觉得很有用，现在开发中经常会被一堆的 warn 日志占满，比如一些 eslint 的提醒规则，编译信息等，头疼的很。在 webpack5 被迁移到了 devMiddleware 中 https://www.webpackjs.com/configuration/stats/
+ `clientLogLevel`：在 webpack5 被迁移到了 client 中，且改为 `logging`。设为 `none` 之后，原来的三条信息会变为只有一条。https://www.webpackjs.com/configuration/dev-server/#devserver-clientloglevel

package.json

```js
"scripts": {
  "dev":"webpack-dev-server --mode development --config config/webpack.base.conf.js",
  "start": "npm run dev"
},
```

> 这里的 mode 也可以直接在配置文件中声明

尝试着运行 `npm start` 或者 `yarn start`，然后就可以访问 `http://localhost:8080/` 来查看你的页面了。默认是访问 `index.html`，如果是其他页面要注意访问的 URL 是否正确

### 构建 CSS

我们编写 `CSS`，并且希望使用 `webpack` 来进行构建，为此，需要在配置中引入 `loader` 来解析和处理 `CSS` 文件

`npm install --save-dev css-loader style-loader `

```js
module.exports = {
  module: {
    rules: [
      // ...
      {
        test: /\.css$/,
        include: [
          path.resolve(__dirname, '../src'),
        ],
        use: ['style-loader','css-loader',],
      },
    ],
  }
}...
```

`css-loader` 负责解析 `CSS` 代码，主要是为了处理 `CSS` 中的依赖，例如 `@import` 和 `url()` 等引用外部文件的声明；

`style-loader` 会将 `css-loader` 解析的结果转变成 `JS` 代码，运行时动态插入 `style` 标签来让 `CSS` 代码生效…

经由上述两个 `loader` 的处理后，**CSS 代码会转变为 JS**，和 `index.js` 一起打包了。如果需要单独把 CSS 文件分离出来：

1. 在 `webpack4` 中我们需要使用 `extract-text-webpack-plugin` 插件
2. 在 `webapck5` 中 `mini-css-extract-plugin`，而且不再需要 `style-loader` 了

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

```js
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
module.exports = {
  plugins: [ new MiniCssExtractPlugin({ 
      // Options similar to the same options in webpackOptions.output 
      // both options are optional filename: "[name].css", chunkFilename: "[id].css" })
  ],
    module: {
        rules: [
            {
                test: /\.css$/,
                include: [
                    path.resolve(__dirname, '../src'),
                ],
                use: [MiniCssExtractPlugin.loader, 'css-loader'],
            },
        ],
    },
}
```

### CSS 预处理器

`npm install --save-dev less less-loader `

> node-sass sass-loader

在上述使用 CSS 的基础上，通常我们会使用 `Less/Sass` 等 CSS 预处理器，webpack 可以通过添加对应的 `loader` 来支持，以使用 `Less` 为例，我们可以在官方文档中找到对应的 `loader`

```js
module.exports = {
    // ...
    module: {
        rules: [
            {
                test: /\.less$/,
                include: [
                    path.resolve(__dirname, '../src'),
                ],
                use: [
                    MiniCssExtractPlugin.loader,
                    'css-loader',
                    {
                        loader: 'less-loader',
                        // 支持antd 按需载入
                        options: {
                            lessOptions: {
                                javascriptEnabled: true,
                            },
                        },
                    },
                ],
            },
        ],
    },
    // ...
}...
```

### 处理图片文件

在前端项目的样式中总会使用到图片，虽然我们已经提到 `css-loader` 会解析样式中用 `url()` 引用的文件路径，但是图片对应的 `jpg/png/gif` 等文件格式，`webpack` 处理不了。是的，我们只要添加一个处理图片的 `loader` 配置就可以了

`npm i --save-dev file-loader url-loader`

#### File-loader

- 现有的 `file-loader` 就是个不错的选择…

    ```js
    module.exports = {
      // ...
      module: {
        rules: [
          {
            test: /\.(png|jpg|gif)$/,
            use: [
              {
                loader: 'file-loader',
                options: {},
              },
            ],
          },
        ],
      },
    }...
    ```

- 在 HTML 和 CSS 内使用时，我们可以像平常一样使用相对路径和绝对路径

    ```css
    // html
    <img src="./images/bg_img.png">
    
    // css
    { background: url("./images/bg_img.png"); }
    ```

- 在 JS 内使用的时候，我们需要通过引入图片，作为模块使用

    ```jsx
    import imgURL from '../src/img/aboutme-background.jpg';
    <img src={imgURL } />  
    ```

    ```jsx
     <img src={require('../../../src/img/aboutme-background.jpg')} /> 
    ```

#### Url-loader

- url-loader 功能基本和 file-loader 一致，所以也可用 url-loader 替代。但是 file-loader 是前置，还是需要的，为什么明明是依赖包，但是不自己搞定依赖呢
- url-loader 还可对小于某个大小尺寸的图片进行 base64 格式的转化处理。

    ```js
    module.exports={
        module:{
            rules:[
                {
                    test: /\.(png|jpg|gif|svg)$/,
                    use: ['url-loader'],
                    options: {
                        name: './images/[name].[ext]',
                        limit: 1024
                    }        
                }
            ]
        }
    }
    ```

- limit 属性的作用就是，将文件小于 1024B 大小的图片转成 base64 格式，而大于的则以 file-loader 方式打包处理。

#### 对比

- 使用 file-loader 方式打包：

    ![img](project-init/168eb2112358cc34)

- 使用 url-loader 方式打包：

    ![image-20200816173033983](project-init/168eb2127cd9a434)

- 如果不写 limit 属性，则不会以 url-loader 方式打包

### Ts 图片类型报错

不幸的是，当你尝试引入一张图片的时候，会有以下 ts 的报错（如果你安装了 ts 的话）：

![image.png](https://camo.githubusercontent.com/db93179e1a4cc94c386afc6e2ca3018191c9fc44b1567b2a4800b9eb5df4798d/68747470733a2f2f63646e2e6e6c61726b2e636f6d2f79757175652f302f323032302f706e672f3334313331342f313539363236373137353236392d61373735333534322d663737652d343865342d623834382d6235346635353363613932322e706e6723616c69676e3d6c65667426646973706c61793d696e6c696e65266865696768743d313539266d617267696e3d2535426f626a6563742532304f626a656374253544266e616d653d696d6167652e706e67266f726967696e4865696768743d333138266f726967696e57696474683d313337362673697a653d3534303731267374617475733d646f6e65267374796c653d6e6f6e652677696474683d363838)

这个时候在 `src/` 下新建以下文件 `typings/file.d.ts` ，输入以下内容即可：

```ts
declare module '*.svg' {
  const path: string
  export default path
}

declare module '*.bmp' {
  const path: string
  export default path
}

declare module '*.gif' {
  const path: string
  export default path
}

declare module '*.jpg' {
  const path: string
  export default path
}

declare module '*.jpeg' {
  const path: string
  export default path
}

declare module '*.png' {
  const path: string
  export default path
}
```

### 内置静态资源构建能力 —— Asset Modules

在 Webpack5 之前，我们一般都会使用以下几个 loader 来处理一些常见的静态资源，比如 PNG 图片、SVG 图标等等，他们的最终的效果大致如下所示：

- raw-loader：允许将文件处理成一个字符串导入
- file-loader：将文件打包导到输出目录，并在 import 的时候返回一个文件的 URI
- url-loader：当文件大小达到一定要求的时候，可以将其处理成 base64 的 URIS ，内置 file-loader

Webpack5 提供了内置的静态资源构建能力，我们不需要安装额外的 loader，仅需要简单的配置就能实现静态资源的打包和分目录存放。如下：满足规则匹配的资源就能够被存放在 assets 文件夹下面。

```js
// webpack.config.js
module.exports = {
    ...,
    module: {
      rules: [
          {
            test: /\.(png|jpg|svg|gif)$/,
            type: 'asset/resource',
            generator: {
                // [ext]前面自带"."
                filename: 'assets/[contenthash].[name][ext]',
            },
        },
      ],
    },
}
```

其中 type 取值如下几种：

- asset/source ——功能相当于 raw-loader。
- asset/inline——功能相当于 url-loader，若想要设置编码规则，可以在 generator 中设置 dataUrl。
- asset/resource——功能相当于 file-loader。项目中的资源打包统一采用这种方式，得益于团队项目已经完全铺开使用了 HTTP2 多路复用的相关特性，我们可以将资源统一处理成文件的形式，在获取时让它们能够并行传输，避免在通过编码的形式内置到 js 文件中，而造成资源体积的增大进而影响资源的加载。
- asset—— 默认会根据文件大小来选择使用哪种类型，当文件小于 8 KB 的时候会使用 asset/inline，否则会使用 asset/resource。也可手动进行阈值的设定
- 具体可以参考：https://webpack.js.org/guides/asset-modules/#custom-data-uri-generator

### 使用 Babel

`Babel` 是一个让我们能够使用 `ES` 新特性的 `JS` 编译工具，我们可以在 `webpack` 中配置 Babel，以便使用 `ES6`、`ES7` 标准来编写 `JS` 代码

`npm i --save-dev babel-loader @babel/core`

`npm i --save-dev @babel/preset-env @babel/preset-react`

```js
module.exports = {
    // ...
    module: {
        rules: [
            {
                test: /\.(tsx|jsx)?/, // 支持 js 和 jsx
                include: [
                    path.resolve(__dirname, '../src'), // src 目录下的才需要经过 babel-loader 处理
                ],
                options: {
                    cacheDirectory: true,
                    presets: ['@babel/env', '@babel/react', '@babel/typescript'],
                    plugins: [
                        ['@babel/plugin-proposal-decorators', { legacy: true }],
                        '@babel/plugin-proposal-class-properties',
                        ['@babel/plugin-proposal-object-rest-spread', { useBuiltIns: true }],
                    ],
                },
                loader: 'babel-loader',
            },
        ],
    },
}...
```

`babel-loader` 在执行的时候，可能会产生一些运行期间重复的公共文件，造成代码体积大冗余，同时也会减慢编译效率，所以我们开启 `cacheDirectory` 将这些公共文件缓存起来，下次编译就会加快很多。

### 使用 Async 语法

```js
npm i --save-dev @babel/runtime @babel/plugin-transform-runtime
```

babelrc

```js
{
  "presets": [
    "@babel/preset-env",
    "@babel/preset-react"],
  "plugins": [[
    "@babel/plugin-transform-runtime",
    {
      "helpers": true, // 默认，可以不写
      "regenerator": true, // 提供的 不污染全局的 regeneratorRuntime
      "useESModules": true // 使用 es modules helpers, 减少 commonJS 语法代码
    }
  ]]
}
```

### 配置执行命令

package.json 中修改 scripts 属性为：

```json
  "scripts": {
    "dev": "webpack-dev-server --colors --config config/webpack.base.conf.js",
    "start": "npm run dev",
    "build": "webpack --progress --colors --config config/webpack.base.conf.js"
  },
```

`dev` 字段配置开发环境命令 `npm run dev`：

- `webapck-dev-server` 启动开发环境服务器，使用 http 协议，没有开启静态文件服务，浏览器会有 file 协议解析 html
- `--config config/webpack.dev.conf.js` 表示输入 npm run dev 时，实际执行的文件是 package.json 同级目录下，config 文件夹下的 webapck.dev.conf.js 文件（自己编写）。 如果去掉，将执行默认配置，这里我们将全部使用自己写的配置，以便更好地发现和解决问题。

## 环境差异配置

我们在日常的前端开发工作中，一般都会有两套构建环境：一套开发时使用，构建结果用于本地开发调试，不进行代码压缩，打印 `debug` 信息，包含 `sourcemap` 文件

另外一套构建后的结果是直接应用于线上的，即代码都是压缩后，运行时不打印 `debug` 信息，静态文件不包括 `sourcemap` 的。有的时候可能还需要多一套测试环境，在运行时直接进行请求 `mock` 等工作

`webpack 4.x` 版本引入了 `mode` 的概念，在运行 `webpack` 时需要指定使用 `production` 或 `development` 两个 `mode` 其中一个，这个功能也就是我们所需要的运行两套构建环境的能力。

### 常见的环境差异配置

- 生产环境可能需要分离 `CSS` 成单独的文件，以便多个页面共享同一个 `CSS` 文件
- 生产环境需要压缩 `HTML/CSS/JS` 代码
- 生产环境需要压缩图片
- 开发环境需要生成 `sourcemap` 文件
- 开发环境需要打印 `debug` 信息
- 开发环境需要 `live reload` 或者 `hot reload` 的功能…
- `webpack 4.x` 的 `mode` 已经提供了上述差异配置的大部分功能，`mode` 为 `production` 时默认使用 `JS` 代码压缩，而 `mode` 为 `development` 时默认启用 `hot` `reload`，等等。这样让我们的配置更为简洁，我们只需要针对特别使用的 `loader` 和 `plugin` 做区分配置就可以了…

### 在配置文件中区分 Mode

前面我们列出了几个环境差异配置，可能这些构建需求就已经有点多了，会让整个 `webpack` 的配置变得复杂，尤其是有着大量环境变量判断的配置。我们可以把 `webpack` 的配置按照不同的环境拆分成多个文件，运行时直接根据环境变量加载对应的配置即可。基本的划分如下…

- `webpack.base.conf.js`：基础部分，即多个文件中共享的配置
- `webpack.dev.conf.js`：开发环境使用的配置
- `webpack.prod.conf.js`：生产环境使用的配置
- `webpack.test.js`：测试环境使用的配置…

首先我们要明白，对于 `webpack` 的配置，其实是对外暴露一个 `JS` 对象，所以对于这个对象，我们都可以用 `JS` 代码来修改它，例如

```js
const config = {
  // ... webpack 配置
}

// 我们可以修改这个 config 来调整配置，例如添加一个新的插件
config.plugins.push(new YourPlugin());

module.exports = config;...
```

因此，只要有一个工具能比较智能地合并多个配置对象，我们就可以很轻松地拆分 webpack 配置，然后通过判断环境变量，使用工具将对应环境的多个配置对象整合后提供给 webpack 使用。这个工具就是 `webpack-merge`

我们的 webpack 配置基础部分，即 `webpack.base.js` 应该大致是这样的

```js
module.exports = {
  entry: '...',
  output: {
    // ...
  },
  resolve: {
    // ...
  },
  module: {
    // 这里是一个简单的例子，后面介绍 API 时会用到
    rules: [
      {
        test: /\.js$/, 
        use: ['babel'],
      },
    ],
    // ...
  },
  plugins: [
    // ...
  ],
}...
```

然后 `webpack.development.js` 需要添加 `loader` 或 `plugin`，就可以使用 `webpack-merge` 的 `API`，例如

```js
const { merge } = require('webpack-merge')
const webpack = require('webpack')
const base = require('./webpack.base.js')

module.exports = merge(base, {
  module: {
    rules: [
      // 用 smart API，当这里的匹配规则相同且 use 值都是数组时，smart 会识别后处理
      // 和上述 base 配置合并后，这里会是 { test: /\.js$/, use: ['babel', 'coffee'] }
      // 如果这里 use 的值用的是字符串或者对象的话，那么会替换掉原本的规则 use 的值
      {
        test: /\.js$/,
        use: ['coffee'],
      },
      // ...
    ],
  },
  plugins: [
    // plugins 这里的数组会和 base 中的 plugins 数组进行合并
    new webpack.DefinePlugin({
      'process.env.NODE_ENV': JSON.stringify(process.env.NODE_ENV),
    }),
  ],
})...
```

可见 `webpack-merge` 提供的 `merge` 方法，可以帮助我们更加轻松地处理 `loader` 配置的合并。`webpack-merge` 还有其他 `API` 可以用于自定义合并行为 <https://github.com/survivejs/webpack-merge>

### 完整代码

#### Base

configs/webpack.base.conf.js

```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module.exports = {
    // 入口起点，从项目的根目录开始读取路径，而不是配置文件所在的config目录
    entry: {
        app: './src/index.js',
    },
    // 输出
    output: {
        // 使用了path包，是相对路径，从本文件开始
        path: path.resolve(__dirname, '../dist'),
        filename: '[name].[contenthash].js',
        publicPath: '/',
        // webpack 默认通过箭头函数包裹打包内容实现作用域
        environment: {
            arrowFunction: false,
        },
    },
    module: {
        rules: [
            {
                test: /\.css$/,
                include: [
                    path.resolve(__dirname, '../src'),
                ],
                use: [MiniCssExtractPlugin.loader, 'css-loader'],
            },
            {
                test: /\.less$/,
                include: [
                    path.resolve(__dirname, '../src'),
                ],
                use: [
                    MiniCssExtractPlugin.loader,
                    'css-loader',
                    {
                        loader: 'less-loader',
                        // 支持antd 按需载入
                        options: {
                            lessOptions: {
                                javascriptEnabled: true,
                            },
                        },
                    },
                ],
            },
            {
                test: /\.(png|jpg|svg|gif)$/,
                type: 'asset',
                generator: {
                    // [ext]前面自带"."
                    filename: 'assets/[contenthash].[name][ext]',
                },
            },
            {
                test: /\.(tsx|js)$/, // 支持 js 和 jsx
                include: [
                    path.resolve(__dirname, '../src'), // src 目录下的才需要经过 babel-loader 处理
                ],
                options: {
                    cacheDirectory: true,
                    presets: [['@babel/env'], '@babel/typescript'],
                    plugins: [
                        [
                            '@babel/plugin-transform-runtime',
                            {
                                'helpers': true, // 默认，可以不写
                                'regenerator': true, // 提供的 不污染全局的 regeneratorRuntime
                                'useESModules': true, // 使用 es modules helpers, 减少 commonJS 语法代码
                            },
                        ],
                        // ['@babel/plugin-proposal-decorators', { legacy: true }],
                        // '@babel/plugin-proposal-class-properties',
                        // ['@babel/plugin-proposal-object-rest-spread', { useBuiltIns: true }],
                    ],
                },
                loader: 'babel-loader',
            },
        ],
    },
    plugins: [
        new HtmlWebpackPlugin({
            filename: 'index.html', // 配置输出文件名和路径
            template: 'public/index.html', // 配置文件模板，也就是入口
            inject: 'body', // inject
        }),
        new MiniCssExtractPlugin(),
    ],
};

```

#### Dev

configs/webpack.dev.conf.js

```js
const { merge } = require('webpack-merge');
const baseWebpackConfig = require('./webpack.base.conf'); // 引入公用的config

module.exports = merge(baseWebpackConfig, {
    // 模式
    mode: 'development',
    // 调试工具
    devtool: 'inline-source-map',
    // 开发服务器
    devServer: {
        static: false, // 默认 dev-server 会为根文件夹提供本地服务器，如果想为另外一个目录下的文件提供本地服务器，应该在这里设置其所在目录，设置为 false 禁用
        historyApiFallback: true, // 在开发单页应用时非常有用，它依赖于HTML5 history API，如果设置为true，所有的跳转将指向index.html
        open: true, // 自动打开浏览器
        compress: true, // 启用gzip压缩
        hot: true, // 模块热更新，自动添加了HotModuleReplacementPlugin，也无需在启动时添加参数
        host: '127.0.0.1', // 设置默认监听域名，如果省略，默认为“localhost”
        port: 8080, // 设置默认监听端口，如果省略，默认为“8080”
        devMiddleware: {
            stats: 'errors-only', // 控制终端仅打印 error
        },
        client: {
            logging: 'error', // 控制浏览器控制台显示的信息
            overlay: true, // Shows a full-screen overlay in the browser when there are compiler errors or warnings
            progress: true, // 将运行进度输出到控制台
        },
    },
    // 插件
    plugins: [
    ],
    optimization: {
        nodeEnv: 'development',
    },
    // 代码模块路径解析的配置
    resolve: {
    // 自动添加模块后缀名
        extensions: ['.wasm', '.mjs', '.js', '.json', '.jsx'],
    },
});

```

#### Prod

configs/webpack.prod.conf.js

```js
const { merge } = require('webpack-merge');

const baseConfig = require('./webpack.base.conf');

const config = merge(baseConfig, {
    mode: 'production',
    module: {
        rules: [

        ],
    },
    plugins: [

    ],
});

module.exports = config;
```

### 更新配置执行命令

package.json

```json
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "dev": "webpack-dev-server --hot --inline --progress --colors --config config/webpack.dev.conf.js",
    "start": "npm run dev",
    "build": "webpack --progress --colors --config config/webpack.prod.conf.js"
  }
```

不同的命令执行不同的 webpack.config

## 开发环境完善

### 模块热替换

`HMR` 全称是 `Hot Module Replacement`，即模块热替换。在这个概念出来之前，我们使用过 `Hot Reloading`，当代码变更时通知浏览器刷新页面，以避免频繁手动刷新浏览器页面。HMR 可以理解为增强版的 `Hot Reloading`，但不用整个页面刷新，而是局部替换掉部分模块代码并且使其生效，可以看到代码变更后的效果。所以，`HMR` 既避免了频繁手动刷新页面，也减少了页面刷新时的等待，可以极大地提高前端页面开发效率…

HMR，模块热替换（HMR）功能会在应用程序运行过程中替换、添加或删除模块，而无需重新加载整个页面。主要通过以下几种方式：

- 保留在完全重新加载页面时丢失的应用状态
- 只更新变更的内容以节省开发时间
- 更改样式不需要刷新页面

#### 配置使用 HMR

+ `HMR` 是 `webpack` 提供的非常有用的一个功能，跟我们之前提到的一样，安装好 `webpack-dev-server`， 添加一些简单的配置，即在 `webpack` 的配置文件中添加启用 `HMR` 需要的两个插件
+ configs/webpack.dev.conf.js

  ```JS
  const webpack = require('webpack')
  const { merge } = require('webpack-merge');
  const baseWebpackConfig = require('./webpack.base.conf'); // 引入公用的config
  
  module.exports = merge(baseWebpackConfig, {
    // ...
    devServer: {
      hot: true // dev server 的配置要启动 hot，或者在命令行中带参数开启
    },
    plugins: [
      // ...
      new webpack.NamedModulesPlugin(), // 用于启动 HMR 时可以显示模块的相对路径
    ],
  }
  ```

#### module.hot 常见的 API

- 前面 `HMR` 实现部分已经讲解了实现 HMR 接口的重要性，下面来看看常见的 `module.hot` `API` 有哪些，以及如何使用
- `module.hot.accept` 方法指定在应用特定代码模块更新时执行相应的 `callback`，第一个参数可以是字符串或者数组，如

  ```js
  if (module.hot) {
    module.hot.accept(['./bar.js', './index.css'], () => {
      // ... 这样当 bar.js 或者 index.css 更新时都会执行该函数
    })
  }...
  ```

- `module.hot.decline` 对于指定的代码模块，拒绝进行模块代码的更新，进入更新失败状态，如 `module.hot.decline('./bar.js')`。这个方法比较少用到
- `module.hot.dispose` 用于添加一个处理函数，在当前模块代码被替换时运行该函数，例如

  ```js
  if (module.hot) {
    module.hot.dispose((data) => {
      // data 用于传递数据，如果有需要传递的数据可以挂在 data 对象上，然后在模块代码更新后可以通过 module.hot.data 来获取
    })
  }...
  ```

- `module.hot.accept` 通常用于指定当前依赖的某个模块更新时需要做的处理，如果是当前模块更新时需要处理的动作，使用 `module.hot.dispose` 会更加容易方便
- `module.hot.removeDisposeHandler` 用于移除 `dispose` 方法添加的 `callback`

#### 对比

- 热更新的原理？如何控制不打印日志
- 热加载和 html 的插件
  - 热加载找到 index.html 之后在自己的微型服务器内存里面操作，不会生产静态的文件
  - html 插件则是以 index.html 为模板在指定目录生产所有的静态文件，css 在 js 里面打包，大的图片同一路径，小的图片也在 js 里面，然后生产的 index.html 直接引用同一路径的 js
  - 热加载用 webpack-dev-server 开启
  - html 插件和普通的打包编译压缩一样用 webpack 同一执行
  - 调试在 webpack-dev-server 进行，结束后用 webpack 生产所有静态文件

### Css Import 使用 Alias 相对路径

**问题**

+ 在用 Webpack 处理打包时，可将某一目录配置一个别名，代码中就能使用与别名的相对路径引用资源。

  ```js
  resolve: {
    ...,
    alias: {
      assets: path.resolve('src/assets')
    }
  }
  ```

+ 这样我们就可以在 js 文件中用形如 `import tool from 'assets/xxx'` 的方式引用 `/src/assets/xxx.js` 文件，并且 Webpack 能正确识别并打包。
+ 但是在 css 文件，如 less, sass, stylus 中，使用 `@import "assets/style/theme"` 的语法引用相对 `assets` 的目录确会报错，”找不到 ‘assets’ 目录”，说明 webpack 没有正确识别资源相对路径。

**原因**

+ 原因是 css 文件会被用 `css-loader` 处理，这里 css `@import` 后的字符串会被 `css-loader` 视为绝对路径解析，因为我们并没有添加 [`css-loader` 的 alias](https://github.com/webpack-contrib/css-loader#alias)，所以会报找不到 `@` 目录。

**解决**

+ 在 Webpack 中 css import 使用 alias 相对路径的解决办法有两种；
  + 一是直接为 `css-loader` 添加 [ailas](https://github.com/webpack-contrib/css-loader#alias) 的路径，但是在 `vue-webpack` 给的模板中，单独针对这个插件添加配置就显得麻烦冗余了；
  + 二是在引用路径的字符串最前面添加上 `~` 符号，如 `@import "~@/style/theme"`；Webpack 会将以 `~` 符号作为前缀的路径视作依赖模块而去解析，这样 `@` 的 alias 配置就能生效了。
+ `~` 视为模块解析是 webpack 做的事，不是 css-loader 做的事。各类非 js 直接引用（`import` `require`）静态资源，依赖相对路径加载问题，都可以用 `~` 语法完美解决；例如
  + css module 中： `@import "~@/style/theme"`
  + css 属性中： `background: url("~@/assets/xxx.jpg")`
  + html 标签中： `<img src="~@/assets/xxx.jpg" alt="alias">`

### PostCSS 处理浏览器兼容问题

postcss 一种对 css 编译的工具，类似 babel 对 js 一样通过各种插件对 css 进行处理，在这里我们主要使用以下插件：

- [postcss-flexbugs-fixes](https://github.com/luisrudge/postcss-flexbugs-fixes) ：用于修复一些和 flex 布局相关的 bug。
- [postcss-preset-env](https://github.com/csstools/postcss-preset-env) ：将最新的 CSS 语法转换为目标环境的浏览器能够理解的 CSS 语法，目的是使开发者不用考虑浏览器兼容问题。我们使用 [autoprefixer](https://github.com/postcss/autoprefixer) 来自动添加浏览器头。
- [postcss-normalize](https://github.com/csstools/postcss-normalize) ：从 browserslist 中自动导入所需要的 normalize.css 内容。

安装上面提到的所需的包：

```
npm install postcss-loader postcss-flexbugs-fixes postcss-preset-env autoprefixer postcss-normalize -D
```

将 `postcss-loader` 放到 `css-loader` 后面，配置如下：

```js
{
  loader: 'postcss-loader',
  options: {
    ident: 'postcss',
    plugins: [
      require('postcss-flexbugs-fixes'),
      require('postcss-preset-env')({
        autoprefixer: {
          grid: true,
          flexbox: 'no-2009'
        },
        stage: 3,
      }),
      require('postcss-normalize'),
    ],
    sourceMap: isDev,
  },
},
```

但是我们要为每一个之前配置的样式 loader 中都要加一段这个，这代码会显得非常冗余，于是我们把公共逻辑抽离成一个函数，与 `cra` 一致，命名为 `getCssLoaders` ，因为新增了 `postcss-loader` ，所以我们要修改 `importLoaders` ，于是我们现在的 `webpack.common.js` 修改为以下这样：

```js
const getCssLoaders = (importLoaders) => [
  'style-loader',
  {
    loader: 'css-loader',
    options: {
      modules: false,
      sourceMap: isDev,
      importLoaders,
    },
  },
  {
    loader: 'postcss-loader',
    options: {
      ident: 'postcss',
      plugins: [
        // 修复一些和 flex 布局相关的 bug
        require('postcss-flexbugs-fixes'),
        require('postcss-preset-env')({
          autoprefixer: {
            grid: true,
            flexbox: 'no-2009'
          },
          stage: 3,
        }),
        require('postcss-normalize'),
      ],
      sourceMap: isDev,
    },
  },
]

module.exports = {
	// other...
  module: {
    rules: [
      {
        test: /\.css$/,
        use: getCssLoaders(1),
      },
      {
        test: /\.less$/,
        use: [
          ...getCssLoaders(2),
          {
            loader: 'less-loader',
            options: {
              sourceMap: isDev,
            },
          },
        ],
      },
      {
        test: /\.scss$/,
        use: [
          ...getCssLoaders(2),
          {
            loader: 'sass-loader',
            options: {
              sourceMap: isDev,
            },
          },
        ],
      },
    ]
  },
  plugins: [//...],
}
```

最后，我们还得在 `package.json` 中添加 `browserslist` （指定了项目的目标浏览器的范围）：

```json
{
  "browserslist": [
    ">0.2%",
    "not dead", 
    "ie >= 9",
    "not op_mini all"
  ],
}
```

现在，在如果你在入口文件（比如我之前一直用的 `app.js` ）随便引一个写了 `display: flex` 语法的样式文件， `npm run start` 看看是不是自动加了浏览器前缀了呢？快试试吧！

### Dev-server 配置跨域请求

如果你有单独的后端开发服务器 API，并且希望在**同域名**下发送 API 请求 ，那么代理某些 URL 会很有用。

dev-server 使用了非常强大的 [http-proxy-middleware](https://github.com/chimurai/http-proxy-middleware) 包。更多高级用法，请查阅其 [文档](https://github.com/chimurai/http-proxy-middleware#options)。

```ts
    proxy: {
      '/api': {
        target: 'http://localhost:6503',
        pathRewrite: {'^/api' : ''},
        changeOrigin:true,
      }
    }
```

#### 基本使用

在 `localhost:3000` 上有后端服务的话，你可以这样启用代理：

webpack.config.js:

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

请求到 `/api/users` 现在会被代理到请求 `http://localhost:3000/api/users`。

#### 重写路径

如果你不想始终传递 `/api` ，则需要重写路径：

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

后端不需要再加上 ‘api’ ，但是前端请求还是要的，用作**转发的**标志

#### 代理多个路径

如果你想要代理多个路径特定到同一个 target 下，你可以使用由一个或多个「具有 `context` 属性的对象」构成的数组：

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

#### 配合 HTTPs

https://webpack.docschina.org/configuration/dev-server/#devserver-proxy

#### 代理根路径

## 生产环境完善

### 打包编译前清理 Dist 目录

我们发现每次打出来的文件都会继续残留在 dist 目录中，当然如果你足够勤快，可以每次打包前手动清理一下，但是这种勤劳是毫无意义的。

借助 [clean-webpack-plugin](https://github.com/johnagan/clean-webpack-plugin) 可以实现每次打包前先处理掉之前的 dist 目录，以保证每次打出的都是当前最新的，我们先安装它：

```
npm install clean-webpack-plugin -D
```

打开 `webpack.prod.js` 文件，增加以下代码：

```js
const { CleanWebpackPlugin } = require('clean-webpack-plugin')

module.exports = {
	// other...
  plugins: [
    new CleanWebpackPlugin(),
  ],
}
```

它不需要你去指定要删除的目录的位置，会自动找到 `output` 中的 `path` 然后进行清除。

现在再执行一下 `npm run build` ，看看打出来的 dist 目录是不是干净清爽了许多？

# 完善 Typescript 配置

## tsconfig.json

每个 Typescript 都会有一个 `tsconfig.json` 文件，其作用简单来说就是：

- 编译指定的文件
- 定义了编译选项

一般都会把 `tsconfig.json` 文件放在项目根目录下。在控制台输入以下代码来生成此文件：

```
npx tsc --init
```

打开生成的 `tsconfig.json` ，有很多注释和几个配置，有点点乱，我们就将这个文件的内容删掉吧，重新输入我们自己的配置。

此文件中现在的代码为：

```json
{
    "compilerOptions": {
        // 基本配置
        // 编译成哪个版本的 es
        "target": "ES5",
        // 指定生成哪个模块系统代码
        "module": "ESNext",
        // 编译过程中需要引入的库文件的列表
        "lib": [
            "dom",
            "dom.iterable",
            "esnext"
        ],
        // 允许编译 js 文件
        "allowJs": true,
        "isolatedModules": true,
        // 启用所有严格类型检查选项
        "strict": true,

        // 模块解析选项
        // 指定模块解析策略
        "moduleResolution": "node",
        // 支持 CommonJS 和 ES 模块之间的互操作性
        "esModuleInterop": true,
        // 支持导入 json 模块
        "resolveJsonModule": true,
        // 根路径
        "baseUrl": "./",
        // 路径映射，与 baseUrl 关联
        "paths": {
            "Src/*": [
                "src/*"
            ],
            "Components/*": [
                "src/components/*"
            ],
            "Utils/*": [
                "src/utils/*"
            ],
            // 因为相对于 baseUrl 所以可以直接访问 node_modules
            "jquery": ["node_modules/jquery/dist/jquery"]
        },

        // 实验性选项
        // 启用实验性的ES装饰器
        "experimentalDecorators": true,
        // 给源码里的装饰器声明加上设计类型元数据
        "emitDecoratorMetadata": true,

        // 开启 ts 严格模式
        "strict": true,
        // 禁止对同一个文件的不一致的引用
        "forceConsistentCasingInFileNames": true,
        // 忽略所有的声明文件（ *.d.ts）的类型检查
        "skipLibCheck": true,
        // 允许从没有设置默认导出的模块中默认导入
        "allowSyntheticDefaultImports": true,
        // 只想使用tsc的类型检查作为函数时（当其他工具（例如Babel实际编译）时）使用它
        "noEmit": true
    },
    "exclude": [
        "node_modules"
    ]
}
```

`compilerOptions` 用来配置编译选项，其完整的可配置的字段从 [这里](https://www.tslang.cn/docs/handbook/compiler-options.html) 可查询到； `exclude` 指定了不需要编译的文件，我们这里是只要是 `node_modules` 下面的我们都不进行编译，当然，你也可以使用 `include` 去指定需要编译的文件，两个用一个就行。

### 编译配置

`target` 和 `module` ：这两个参数实际上没有用，它是通过 `tsc` 命令执行才能生成对应的 es5 版本的 js 语法，但是实际上我们已经使用 babel 去编译我们的 ts 语法了，根本不会使用 `tsc` 命令，所以它们在此的作用就是让编辑器提供错误提示。

### 模块配置

`isolatedModules` ：可以提供额外的一些语法检查。

1. 不能重复 `export`
2. 每个文件必须是作为独立的模块：

`baseUrl` 和 `paths`：可以用于快速查找路径

1. 首先 `baseUrl` 一定要设置正确，我们的 `tsconfig.json` 是放在项目根目录的，那么 `baseUrl` 设为 `./` 就代表了项目根路径。于是， `paths` 中的每一项路径映射，比如 `["src/*"]` 其实就是相对根路径。
2. 需要改 `.eslintrc.js` 文件的配置了，首先得安装 `npm install eslint-import-resolver-typescript -D`
3. ```js
    settings: {
      'import/resolver': {
        node: {
          extensions: ['.tsx', '.ts', '.js', '.json'],
        },
        typescript: {},
      },
    },
    ```
4. 需要添加 `typescript: {}` 即可

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

## Fork-ts-checker-webpack-plugin

babel-loader 和 typescript-preset 支持编译 ts，但是没有支持类型检查，通过这个插件做支持即可，会另外开启一个进程做类型检查，不会影响编译的速度

```
npm i -D fork-ts-checker-webpack-plugin
```

https://github.com/TypeStrong/fork-ts-checker-webpack-plugin

```js
        new ForkTsCheckerWebpackPlugin({
            typescript: {
                diagnosticOptions: {
                    semantic: true,
                    syntactic: true,
                },
                mode: 'write-references',
            },
        }),
```

上述是官网的推荐配置

## 产出类型文件

如果是使用 tsc 编译，则默认会产出类型文件

如果是使用 rollup 编译，也可以通过配置产出类型文件

```ts
        typescript({
            typescript: ttypescript,
            tsconfig: path.resolve(__dirname, 'tsconfig.json'),
            clean: true,
            tsconfigOverride: {
                compilerOptions: {
                    sourceMap: true,
                    declaration: true,
                    declarationMap: true,
                    'plugins': [
                        {'transform': '@zerollup/ts-transform-paths'}
                    ]
                }
            }
        }),
```

如果使用 babel 编译，则无法产出类型文件，需要另外运行 tsc --emitDeclarationOnly，通过指定 package.json types 字段保证 import 包时可以获得正确的类型

# React Init

## 环境配置

### 使用脚手架

```
npm install -g create-react-app 
npx create-react-app my-app --template typescript
cd hello-react
npm start
```

`npm run ject` ，会将封装在 CRA 中的配置全部 `反编译` 到当前项目，这样用户就可以完全取得 webpack 文件的控制权

https://blog.csdn.net/qq_36709020/article/details/80275602?utm_source=blogxgwz1

### 基础支持

`npm install --save react react-dom`

`npm i -D @types/react @types/react-dom`

### Eslint 支持

`npm i -D @chiyu-git/eslint-config-react`

```js
module.exports = {
    extends: [
        '@chiyu-git/eslint-config-react',
    ],
};
```

```json
"lint-eslint": "eslint -c .eslintrc.js --ext .ts,.tsx,.js src",

    "lint-staged": {
        "*.{ts,tsx,js}": [
            "eslint --config .eslintrc.js"
        ],
        "*.{css,less,scss}": [
            "stylelint --config .stylelintrc.js"
        ]
    },
```

### Babel 支持

`npm i -D @babel/preset-react`

```js
test: /\.(tsx|js)$/, // 支持 tsx ts 和 js
presets: [
    ['@babel/env', {
        useBuiltIns: 'usage',
        corejs: 3,
    }],
    '@babel/react',
    '@babel/typescript',
],
```

```js
    resolve: {
    	// 自动添加模块后缀名
        extensions: ['.tsx', '.ts', '.wasm', '.mjs', '.js', '.json', '.jsx'],
    },
```

### Typescript 支持

```json
    "jsx": "react",                           // 在 .tsx 文件里支持 JSX
```

```
npm install @types/react @types/react-dom -D
```

## 基本使用

### 使用 Router

```
npm install --save react-router-dom
```

一般来说，一个网站起码会有一个导航栏，用于提供各种链接，而不是让用户手动输入 URL 来实现页面的切换。此外，可能还会有一个公共的页脚，用于显示版权信息、友情链接或者备案信息等。

那么，这些文件应该怎么组织呢？显然，它们应该被放置在布局文件所在的 src/layouts 文件夹下。下面让我们来创建这些文件。

src/layouts 目录，添加两个文件——Frame.js 和 Nav.js：

先生成几个简单的路由入口组件，如 components 目录下的 Home.tsx 和 Detail.tsx

```react
import React, {Component } from 'react'

class Detail extends Component {
  render() {
    return (
      <div>Detail</div>
    )
  }
}

export default Detail
```

公共部分含有路由组件，比如底部导航，在 Rouer 下再包裹一层即可

```react
// src/layouts/Frame.js
import React, { PureComponent } from 'react';
import { Switch, Route, BrowserRouter } from 'react-router-dom';
import Home from '@components/Home';
import Detail from '@components/Detail';
import Nav from './Nav';

class Frame extends PureComponent {
    render() {
        return (
            <BrowserRouter>
                <div>
                    <Nav />
                    {/*需要紧紧跟着Router，中间插入了其他则不行*/}
                    <Switch>
                      	 {/* 默认路由，path='/' 必须加上exact，否则任何时候都是匹配的*/}
                        <Route exact path='/' component={Home} />
                        <Route path='/detail/:id' component={Detail} />
                        <Route path='/ome' component={Home} />
                    </Switch>
                </div>
            </BrowserRouter>
        );
    }
}

export default Frame;
```

### 启动应用

入口 js 文件：src/index.js

```react
import React from 'react'
import ReactDOM from 'react-dom'

import Frame from './layouts/Frame'

ReactDOM.render(
  (
      <Frame/>
  ), 
  document.getElementById('root'));
```

React 官方并不推荐将组件渲染到 document.body 上，因为这个节点很可能会被修改，比如动态添加一个 `<script>` 标签等，这将使 React 的 DOM diff 计算变得更加困难。

入口 html 文件：根目录下 index.html

只需含有 id 为 root 的一个元素即可

这两个文件的位置和名字，都已经在 webpack.config.js 中被定义

# San Init

`npm install --save san`

`npm install --save-dev san-loader`

```js
module.exports = {
  // ...
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: 'babel-loader',
      },
      {
        test: /\.san$/,
        use: 'san-loader',
      },
    ],
  },
}...
```

# 通用配置

## 请求

### enhanceFetch

```ts
/**
 * @file 对原生fetch进行包装 方便使用
 */

/**
 * 返回一个promise，外部可以通过then在获取到数据后再继续操作
 *
 * @param requestUrl
 * @param method
 * @param params
 * @returns Promise
 */
export async function enhanceFetch(
    requestUrl = '',
    method = 'GET',
    params = {},
) {
    console.log(method, params, requestUrl);

    let url = requestUrl;
    const result = null;
    let response: Response;

    // 无论是GET还是POST都需要拼接参数
    let query = '';
    for (const [key, value] of Object.entries(params)) {
        query += `${key}=${value}&`;
    }
    // 去除最后一个 &
    if (query) {
        query = query.slice(0, -1);
    }

    if (method === 'GET' && query) {
        url += `?${query}`;
    }

    // 不同的请求不同的fetch
    try {
        switch (method) {
            case 'GET':
                response = await fetch(url);
                break;
            case 'POST':
                response = await fetch(url, {
                    method,
                    headers: {
                        'Content-type': 'application/x-www-form-urlencoded; charset=UTF-8',
                    },
                    body: query,
                    mode: 'cors',
                });
                break;
            default:
        }
    }
    catch (error) {
        console.log('Request Error:', error);
    }

    return response!.json();
}
```

## Css Rest

public/reset.css

```html
<link rel="stylesheet" type="text/css" media="screen" href="./reset.css" />
```

再加上一个 border-box

## 移动端默认配置

### 移动端常见问题

移动端数字和邮箱会自动变成可点击的，并且点击后唤醒电话或邮箱 app

```html
<meta name="format-detection" content="telephone=no,email=no"/>
```

链接点击的时候，会有高亮的默认背景，可以通过 -webkit-tap-highlight-color 设置该值

```css
a{
	-webkit-tap-highlight-color:rgba(0,0,0,0);
}
```

按钮过圆的问题

```css
input{
  webkit-appearance:none;
  border-radius:5px;
}
```

Font Boosting 是 Webkit 给移动端浏览器提供的一个特性：当我们在手机上浏览网页时，很可能因为原始页面宽度较大，在手机屏幕上缩小后就看不清其中的文字了。而 Font Boosting 特性在这时会 **自动** 将其中的 **文字字体变大**，

但是文本内容不可能都指定宽高。不过还好，我们通过指定 max-height 就可以无副作用的禁掉 Font Boosting 特性。用类似 p { max-height: 999999px; } 的方式来处理

### 300ms 延迟

```html
    <script src="https://as.alipayobjects.com/g/component/fastclick/1.0.6/fastclick.js"></script>
    <script>
      if ('addEventListener' in document) {
        document.addEventListener('DOMContentLoaded', function() {
          FastClick.attach(document.body);
        }, false);
      }
      if(!window.Promise) {
        document.writeln('<script src="https://as.alipayobjects.com/g/component/es6-promise/3.2.2/es6-promise.min.js"'+'>'+'<'+'/'+'script>');
      }
    </script>
```

###


# Rest

## Webpack 搭配 Eslint

webpack 搭配 eslint：https://juejin.cn/post/6844903859488292871

eslint 为什么要和 webpack 搭配？没搞懂，是要确保打包之后的也符合规范？有点离谱

是为了在调试的时候，打包可以提示 esilnt error，感觉还不如编辑器的提示，没有必要融合 webpack

[eslint-import-resolver-webpack](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fbenmosher%2Feslint-plugin-import%23resolvers): 可以借助 webpack 的配置来辅助 eslint 解析，最有用的就是 alias，从而避免 unresolved 的错误

[eslint-import-resolver-typescript](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Falexgorbatchev%2Feslint-import-resolver-typescript)：和 eslint-import-resolver-webpack 类似，主要是为了解决 alias 的问题

## 生产环境配置

先配置项目基础，react 和 typescript 可插拔式添加配置即可

### 公共变量文件

拆分成：

1. path 的公共变量抽取
2. 环境变量的区分
3. 生产环境完善的 hash8

在上面简单的 webpack 配置中，我们发现有两个表示路径的语句：

```
path.resolve(__dirname, '../../src/app.js')
path.resolve(__dirname, '../../dist')
```

- `path.resolve` ：node 的官方 api，可以将路径或者路径片段解析成绝对路径。
- `__dirname` ：其总是指向被执行 js 文件的绝对路径，比如在我们 webpack 文件中访问了 `__dirname` ，那么它的值就是在电脑系统上的绝对路径，比如在我电脑上就是：

```
/Users/RMBP/Desktop/react-ts-quick-starter/scripts/config
```

所以我们上面的写法，大家可以简单理解为， `path.resolve` 把 **根据当前文件的执行路径下** 而找到的想要访问到的 **文件相对路径** 转换成了：**该文件在系统中的绝对路径！**

比如我的就是：

```
/Users/RMBP/Desktop/react-ts-quick-starter/src/app.js
```

但是大家也看出来了，这种写法需要不断的 `../../` ，这个在文件层级较深时，很容易出错且很不优雅。那我们就换个思路，都从根目录开始找所需的文件路径不久很简单了吗，相当于省略了 `../../` 这一步。

在 `scripts` 下新建一个 `constant.js` 文件，专门用于存放我们的公用变量（之后还会有其他的）：

```
scripts/
	config/
  	webpack.common.js
+ constant.js
```

在里面定义我们的变量：

```
const path = require('path')

const PROJECT_PATH = path.resolve(__dirname, '../')
const PROJECT_NAME = path.parse(PROJECT_PATH).name

module.exports = { 
  PROJECT_PATH,
  PROJECT_NAME
}
```

- `PROJECT_PATH` ：表示项目的根目录。
- `PROJECT_NAME` ：表示项目名，目前不用，但之后的配置会用到，我们就先定义好吧～

> 上面两个简单的 node api 大家可以自己简单了解一下，不想了解也可以，只要明白其有啥作用就行。

然后在 `webpack.common.js` 中引入，修改代码：

```
const { resolve } = require('path')
const { PROJECT_PATH } = require('../constants')

module.exports = {
  entry: {
    app: resolve(PROJECT_PATH, './src/app.js'),
  },
  output: {
    filename: 'js/[name].[hash:8].js',
    path: resolve(PROJECT_PATH, './dist'),
  },
}
```

好了，现在是不是看起来清爽多了，大家可以 `npm run build` 验证下自己代码是不是有写错或遗漏啥的～🐶

### Html-webpack-plugin

因为 `html-webpack-plugin` 在开发和生产环境我们都需要配置，于是我们打开 `webpck.common.js` 增加以下内容：

```js
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
  entry: {...},
  output: {...},
  plugins: [
  	new HtmlWebpackPlugin({
      template: resolve(PROJECT_PATH, './public/index.html'),
      filename: 'index.html',
      cache: fale, // 特别重要：防止之后使用v6版本 copy-webpack-plugin 时代码修改一刷新页面为空问题。
      minify: isDev ? false : {
        removeAttributeQuotes: true,
        collapseWhitespace: true,
        removeComments: true,
        collapseBooleanAttributes: true,
        collapseInlineTagWhitespace: true,
        removeRedundantAttributes: true,
        removeScriptTypeAttributes: true,
        removeStyleLinkTypeAttributes: true,
        minifyCSS: true,
        minifyJS: true,
        minifyURLs: true,
        useShortDoctype: true,
      },
    }),
  ]
}
```

### Devtool

`devtool` 中的一些设置，可以帮助我们将编译后的代码映射回原始源代码，即大家经常听到的 `source-map` ，这对于调试代码错误的时候特别重要，而不同的设置会明显影响到构建和重新构建的速度。所以选择一个适合自己的很重要。

它都有哪些值可以设置，[官方 devtool 说明](https://webpack.js.org/configuration/devtool/) 中说的很详细，我就不具体展开了，**在这里我非常非常无敌强烈建议大家故意写一些有错误的代码，然后使用每个设置都试试看！**在开发环境中，我个人比较能接受的是 `eval-source-map` ，所以我会在 `webpack.dev.js` 中添加以下代码：

```
module.exports = merge(common, {
  mode: 'development',
+ devtool: 'eval-source-map',
})
```

在生产环境中我直接设为 `none` ，不需要 `source-map` 功能，在 `webpack.prod.js` 中添加以下代码：

```
module.exports = merge(common, {
  mode: 'production',
+ devtool: 'none',
})
```

通过上面配置，我们本地进行开发时，代码出现了错误，控制台的错误日志就会精确地告诉你错误的代码文件、位置等信息。比如我们在 `src/app.js` 中第 `5` 行故意写个错误代码：

```
const root = document.querySelector('#root')
root.innerHTML = 'hello, webpack!'

const a = 5
a = 6
```

其错误日志提示我们：你的 `app.js` 文件中第 `5` 行出错了，具体错误原因为 `balabala....` ，赶紧看看吧～

### Loader sourceMap

于是，打开我们的 `webpack.common.js` ，写入以下代码：

```
module.exports = {
	// other...
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          'style-loader',
          {
            loader: 'css-loader',
            options: {
              modules: false, // 默认就是 false, 若要开启，可在官网具体查看可配置项
              sourceMap: isDev, // 开启后与 devtool 设置一致, 开发环境开启，生产环境关闭
              importLoaders: 0, // 指定在 CSS loader 处理前使用的 laoder 数量
            },
          },
        ],
      },
    ]
  },
}
```

## 图片和字体文件处理

我们可以使用 [file-loader](https://github.com/webpack-contrib/file-loader) 或者 [url-loader](https://github.com/webpack-contrib/url-loader) 来处理本地资源文件，比如图片、字体文件，而 `url-loader` 具有 `file-loader` 所有的功能，还能在图片大小限制范围内打包成 base64 图片插入到 js 文件中，这样做的好处是什么呢？别急，我们先安装所需要的包（后者依赖前者，所以都要安装）：

```
npm install file-loader url-loader -D
```

然后在 `webpack.common.js` 中继续在 `modules.rules` 中添加以下代码：

```js
module.exports = {
  // other...
  module: {
    rules: [
      // other...
      {
        test: [/\.bmp$/, /\.gif$/, /\.jpe?g$/, /\.png$/],
        use: [
          {
            loader: 'url-loader',
            options: {
              limit: 10 * 1024,
              name: '[name].[contenthash:8].[ext]',
              outputPath: 'assets/images',
            },
          },
        ],
      },
      {
        test: /\.(ttf|woff|woff2|eot|otf)$/,
        use: [
          {
            loader: 'url-loader',
            options: {
              name: '[name].[contenthash:8].[ext]',
              outputPath: 'assets/fonts',
            },
          },
        ],
      },
    ]
  },
  plugins: [//...],
}
```

- `[name].[contenthash:8].[ext]` 表示输出的文件名为 `原来的文件名.哈希值.后缀` ，有了这个 hash 值，可防止图片更换后导致的缓存问题。
- `outputPath` 是输出到 `dist` 目录下的路径，即图片目录 `dist/assets/images` 以及字体相关目录 `dist/assets/fonts` 下。
- `limit` 表示如果你这个图片文件大于 `10240b` ，即 `10kb` ，那我 `url-loader` 就不用，转而去使用 `file-loader` ，把图片正常打包成一个单独的图片文件到设置的目录下，若是小于了 `10kb` ，就将图片打包成 base64 的图片格式插入到打包之后的文件中，这样做的好处是，减少了 http 请求，但是如果文件过大，js 文件也会过大，得不偿失，这是为什么有 `limit` 的原因！

接下来大家引一下本地的图片并放到 img 标签中，或者去 [iconfont](https://www.iconfont.cn/) 下个字体图标试试吧～

不幸的是，当你尝试引入一张图片的时候，会有以下 ts 的报错（如果你安装了 ts 的话）：

这个时候在 `src/` 下新建以下文件 `typings/file.d.ts` ，输入以下内容即可：

```js
declare module '*.svg' {
  const path: string
  export default path
}

declare module '*.bmp' {
  const path: string
  export default path
}

declare module '*.gif' {
  const path: string
  export default path
}

declare module '*.jpg' {
  const path: string
  export default path
}

declare module '*.jpeg' {
  const path: string
  export default path
}

declare module '*.png' {
  const path: string
  export default path
}
```

其实看到现在已经很不容易了，不过我相信大家仔细跟到现在的话，也会收获不少的，上面的 webpack 基本配置只是配置了最基本的功能，接下来我们要达到支持 React，TypeScript 以及一堆的开发环境和生产环境的优化，大家加油噢～

## Css-minimizer-webpack-plugin

## Cross-env

虽然都分开了配置，但是在公共配置中，还是可能会出现某个配置的某个选项在开发环境和生产环境中采用不同的配置，这个时候我们有两种选择：

- 一是分别在 dev 和 prod 配置文件中写一遍，common 中就不写了。
- 二是设置某个环境变量，根据这个环境变量来判别不同环境。

显而易见，为了使代码最大的优雅，采用第二种。

[cross-env](https://www.npmjs.com/package/cross-env) 可跨平台设置和使用环境变量，不同操作系统设置环境变量的方式不一定相同，比如 Mac 电脑上使用 `export NODE_ENV=development` ，而 Windows 电脑上使用的是 `set NODE_ENV=development` ，有了这个利器，我们无需在考虑操作系统带来的差异性。

安装它：

```
npm install cross-env -D
```

然后在 `package.json` 中添加修改以下代码：

```
{
  "scripts": {
+   "start": "cross-env NODE_ENV=development webpack --config ./scripts/config/webpack.dev.js",
+   "build": "cross-env NODE_ENV=production webpack --config ./scripts/config/webpack.prod.js",
-   "build": "webpack --config ./scripts/config/webpack.common.js",
  },
}
```

修改 `srcipt/constants.js` 文件，增加一个公用布尔变量 `isDev` ：

```
const isDev = process.env.NODE_ENV !== 'production'

module.exports = {
  isDev,
	// other
}
```

我们现在就使用这个环境变量做点事吧！记得之前配的公共配置中，我们给出口文件的名字配了 `hash:8` ，原因是在生产环境中，即用户已经在访问我们的页面了，他第一次访问时，请求了比如 `app.js` 文件，根据浏览器的缓存策略会将这个文件缓存起来。然后我们开发代码完成了一版功能迭代，涉及到打包后的 `app.js` 发生了大变化，但是该用户继续访问我们的页面时，如果缓存时间没有超出或者没有人为清除缓存，那么他将继续得到的是已缓存的 `app.js` ，这就糟糕了。

于是，当我们文件加了 hash 后，根据入口文件内容的不同，这个 hash 值就会发生非常夸张的变化，当更新到线上，用户再次请求，因为缓存文件中找不到同名文件，就会向服务器拿最新的文件数据，这下就能保证用户使用到最新的功能。

不过，这个 hash 值在开发环境中并不需要，于是我们修改 `webpack.common.js` 文件：

```
- const { PROJECT_PATH } = require('../constants')
+ const { isDev, PROJECT_PATH } = require('../constants')

module.exports = {
	// other...
  output: {
-   filename: 'js/[name].[hash:8].js',
+   filename: `js/[name]${isDev ? '' : '.[hash:8]'}.js`,
    path: resolve(PROJECT_PATH, './dist'),
  },
}
```

### 5. Mode

在我们没有设置 `mode` 时，webpack 默认为我们设为了 `mode: 'prodution'` ，所以之前打包后的 js 文件代码都没法看，因为在 `production` 模式下，webpack 默认会丑化、压缩代码，还有其他一些默认开启的配置。

我们只要知道，不同模式下 webpack 为为其默认开启不同的配置，有不同的优化，详细可见 [webpack.mode](https://webpack.js.org/configuration/mode/#root)。

然后接下来大家可以分别执行以下命令，看看分别打的包有啥区别，主要感知下我们上面所说的：

```
# 开发环境打包
npm run start

# 生产环境打包
npm run build
```

因为 `html-webpack-plugin` 在开发和生产环境我们都需要配置，于是我们打开 `webpck.common.js` 增加以下内容：

```
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
  entry: {...},
  output: {...},
  plugins: [
  	new HtmlWebpackPlugin({
      template: resolve(PROJECT_PATH, './public/index.html'),
      filename: 'index.html',
      cache: fale, // 特别重要：防止之后使用v6版本 copy-webpack-plugin 时代码修改一刷新页面为空问题。
      minify: isDev ? false : {
        removeAttributeQuotes: true,
        collapseWhitespace: true,
        removeComments: true,
        collapseBooleanAttributes: true,
        collapseInlineTagWhitespace: true,
        removeRedundantAttributes: true,
        removeScriptTypeAttributes: true,
        removeStyleLinkTypeAttributes: true,
        minifyCSS: true,
        minifyJS: true,
        minifyURLs: true,
        useShortDoctype: true,
      },
    }),
  ]
}
```

可以看到，我们以 `public/index.html` 文件为模板，并且在生产环境中对生成的 `html` 文件进行了代码压缩，比如去除注释、去除空格等。

> plugin 是 webpack 的核心功能，它丰富了 webpack 本身，针对是 loader 结束后，webpack 打包的整个过程，它并不直接操作文件，而是基于事件机制工作，会监听 webpack 打包过程中的某些节点，执行广泛的任务。

## 缓存处理应该单独拆出来

# 单测

[eslint-config-jest-enzyme](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2FFormidableLabs%2Fenzyme-matchers%2Ftree%2Fmaster%2Fpackages%2Feslint-config-jest-enzyme): jest 和 enzyme 专用的校验规则，保证一些断言语法可以让 Eslint 识别而不会发出警告。

[eslint-plugin-jest](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fjest-community%2Feslint-plugin-jest): Jest 专用的 Eslint 规则校验插件.

# 项目支持 AB 实验
