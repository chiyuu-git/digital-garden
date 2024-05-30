---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2022-10-30-Sun, 9:37:48 pm","date-modified":"2023-04-29-Sat, 8:17:48 pm","permalink":"/programming/front-end/primitive/css/tailwind/tailwind-advanced/","dgPassFrontmatter":true}
---


# 原理

如果打开 tailwindcss 的 [github仓库](https://link.zhihu.com/?target=https%3A//github.com/tailwindcss/tailwindcss/)，大概会一脸蒙，怎么 css 框架一堆 js ，而且 css 文件也是奇怪的 atrule?可以说，tailwindcss 对 postcss 的使用真的上了一个层次，之前我也没想到可以这么玩！通过 js 配置，[postcss-js](https://www.zhihu.com/search?q=postcss-js&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A1262065503%7D) 和 postcss-nested 去解析我们的配置，生成 node，然后最后再用 postcss 去生成 css，甚至还可以使用 puregecss 去裁剪生成的 css ，或者用比较熟悉的话术说，就是可以 tree shaking 我们的 css，听上去很高级的样子。这里不会细致，大概挑几个比较有特色的讲一下。

## 经典的 tailwind.css

[tailwind.css](https://www.zhihu.com/search?q=tailwind.css&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A1262065503%7D)

首先看下面三个比较经典的 [atrule](https://www.zhihu.com/search?q=atrule&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A1262065503%7D) ，`atrule` 是 postcss 里面 ast 的一个节点，代表 css 中的@节点，其中后面跟随的是这个节点的 params ，具体可以去 [astexplorer](https://link.zhihu.com/?target=https%3A//astexplorer.net/) 以及结合 postcss 的 [api文档](https://link.zhihu.com/?target=http%3A//api.postcss.org/) 去理解

```text
@tailwind base;

@tailwind components;

@tailwind utilities;
```

在 tailwindcss 里面，这样经典的节点，会起两个作用，拿 `@tailwind base;` 这个来说，postcss 插件遇到这个 atrule 的时候,识别 params 是 base ，会把 pluginBase 中的节点内容生成 css ，同时会修复 source ，这样 [sourcemap](https://www.zhihu.com/search?q=sourcemap&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A1262065503%7D) 就不会出错了，具体可以看下面,可以看到，现在 atRule 前面插入内容，移除这个 atRule

```text
css.walkAtRules('tailwind', atRule => {
      if (atRule.params === 'preflight') {
        // prettier-ignore
        throw atRule.error("`@tailwind preflight` is not a valid at-rule in Tailwind v1.0, use `@tailwind base` instead.", { word: 'preflight' })
      }

      if (atRule.params === 'base') {
        atRule.before(updateSource(pluginBase, atRule.source))
        atRule.remove()
      }

      if (atRule.params === 'components') {
        atRule.before(postcss.comment({ text: 'tailwind start components' }))
        atRule.before(updateSource(pluginComponents, atRule.source))
        atRule.after(postcss.comment({ text: 'tailwind end components' }))
        atRule.remove()
      }

      if (atRule.params === 'utilities') {
        atRule.before(postcss.comment({ text: 'tailwind start utilities' }))
        atRule.before(updateSource(pluginUtilities, atRule.source))
        atRule.after(postcss.comment({ text: 'tailwind end utilities' }))
        atRule.remove()
      }

      if (atRule.params === 'screens') {
        includesScreensExplicitly = true
        atRule.before(postcss.comment({ text: 'tailwind start screens' }))
        atRule.after(postcss.comment({ text: 'tailwind end screens' }))
      }
    })

    if (!includesScreensExplicitly) {
      css.append([
        postcss.comment({ text: 'tailwind start screens' }),
        postcss.atRule({ name: 'tailwind', params: 'screens' }),
        postcss.comment({ text: 'tailwind end screens' }),
      ])
    }
```

至于修复 sourcemap 指向，可以看下面这段函数，让新生成的所有节点都指向了原来 atRule 的 source，完美~

```text
function updateSource(nodes, source) {
  return _.tap(Array.isArray(nodes) ? postcss.root({ nodes }) : nodes, tree => {
    tree.walk(node => (node.source = source))
  })
}
```

## 再讲一个 Screen atRule

> Instead of writing a raw media query that duplicates that value like this:

```text
@media (min-width: 640px) {
  /* ... */
}
```

> you can use the @screen directive and reference the breakpoint by name:

```text
@screen sm {
  /* ... */
}
```

上面这个英文，是从文档里面拷贝出来的， 就是常见的 [媒体查询](https://www.zhihu.com/search?q=%E5%AA%92%E4%BD%93%E6%9F%A5%E8%AF%A2&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A1262065503%7D)，我们可以写死到 css，但是如果过段时间又要改呢？这里抽离出来到 js 配置里面，我们通过读取配置文件，默认是 tailwind.config.js，里面的 theme.screens 对应 atRule 中的 params 的值，生成我们具体的 css，简直不要太方便啊

```text
import _ from 'lodash'
import buildMediaQuery from '../util/buildMediaQuery'

export default function({ theme }) {
  return function(css) {
    css.walkAtRules('screen', atRule => {
      const screen = atRule.params

      if (!_.has(theme.screens, screen)) {
        throw atRule.error(`No \`${screen}\` screen found.`)
      }

      atRule.name = 'media'
      atRule.params = buildMediaQuery(theme.screens[screen])
    })
  }
}
```

## 关于 Tailwindcss 插件的写法

tailwindcss 插件的写法，具体怎么写，这里不具体展开，这里讲讲原理。插件其实主要的作用就是把我们写的 js css 配置生成一个初步的 css，然后再根据配置文件，进行一个二次处理，最后生成实际的 css，拿一个内置的 zIndex 的插件当例子，主要是这个插件也写了测试用例，结合文档，介绍起来简直不要太方便

首先我们看看这个插件是怎么写的

```text
import _ from 'lodash'
import prefixNegativeModifiers from '../util/prefixNegativeModifiers'

export default function() {
  return function({ addUtilities, e, theme, variants }) {
    const utilities = _.fromPairs(
      _.map(theme('zIndex'), (value, modifier) => {
        return [
          `.${e(prefixNegativeModifiers('z', modifier))}`,
          {
            'z-index': value,
          },
        ]
      })
    )

    addUtilities(utilities, variants('zIndex'))
  }
}
```

其实我们要关心的就是 `addUtilities(utilities, variants('zIndex'))` 这块到底干嘛了，简单地说，就是 zIndex 塞到 pluginUtilities 里面，也就是说，最后是对应 `@tailwind utilities;` ，可以看看测试用例中生成的是什么，如下，就是这个样子，其中 utilities 还好理解，variants 是什么？variants 的使用可以看文档的时候，说白了，就是会有 `@variants responsive` 把我们 utilities 中生成的代码包住，这样就初步生成我们的 css，然后 substituteResponsiveAtRules 这个会二次处理这个 atRule，生成最终的 css

```text
expect(addedUtilities).toEqual([
    {
      utilities: {
        '.-z-20': { 'z-index': '-20' },
        '.-z-10': { 'z-index': '-10' },
        '.z-10': { 'z-index': '10' },
        '.z-20': { 'z-index': '20' },
      },
      variants: ['responsive'],
    },
  ])
```

## 最后说一句

`tailwindcss` 还有很多玩法，具体可以去文档挖掘，这里只是简单说下它实现的原理，它给我带来的震撼是，没想到 postcss 或者说 css 还可以这么玩，脑洞太大了, ps: 为了了解这货，基本把主要依赖库的文档瞄了一遍，: 逃）

# Unocss 的其他优化

## Windicss 属性化模式

windicss 是 tailwindcss 的竞品, 号称是 Next generation utility-first CSS framework.

[属性化模式 (Attributify Mode)](https://link.zhihu.com/?target=https%3A//windicss.org/posts/v30.html%23attributify-mode) 是 Windi CSS 最受欢迎的特性之一。它能帮助你通过使用属性更好地组织和分组你的实用工具类。

它会把你的冗长的 Tailwind 代码（难以阅读与编辑）：

```html
<button class="bg-blue-400 hover:bg-blue-500 text-sm text-white font-mono font-light py-2 px-4 rounded border-2 border-blue-200 dark:bg-blue-500 dark:hover:bg-blue-600">
  Button
</button>
```

变成：

```html
<button 
  bg="blue-400 hover:blue-500 dark:blue-500 dark:hover:blue-600"
  text="sm white"
  font="mono light"
  p="y-2 x-4"
  border="2 rounded blue-200"
>
  Button
</button>
```

在更好的按类型进行组织的同时，也节省了重复输入相同前缀的时间。

## Uno 的无值属性支持

除了 Windi CSS 的属性化模式，仅需改动几行代码，我们还实现了无值的属性的支持：

```html
<div class="m-2 rounded text-teal-400" />
```

现在变为：

```html
<div m-2 rounded text-teal-400 />
```

整个属性化模式是通过 `[@unocss/preset-attributify](https://link.zhihu.com/?target=https%3A//github.com/antfu/unocss/blob/main/packages/preset-attributify)` 预设提供的，详细的使用方法请参考其文档。

属性化支持的问题是容易和组件的属性混淆

## CSS 作用域

在使用 Tailwind / Windi 时，我遇到的另一个问题就是 [样式预检 (Preflight)](https://link.zhihu.com/?target=https%3A//tailwindcss.com/docs/preflight)。预检功能重置了原生元素，并为 CSS 变量提供了一些兜底方案，在开发一个只使用 Tailwind/Windi 的新应用时，效果很棒。但当你想让它们与其他 UI 框架一起工作，或者使用 Tailwind 编写一些共享组件时，预检往往会引入许多冲突，破坏你现有的 UI。

因此，UnoCSS 采取了另一个霸气的操作，**不支持预检**。相反，它将 CSS 重置的控制权完全留给了用户 (或 UnoCSS 上层框架)，让他们使用适合自己的方案 - Normalize.css，Reset.css 或者 UI 框架自带的 CSS 重置等。

这也使得 UnoCSS 在 CSS 作用域上有了更多可能性。例如，我们在 Vite 插件上有一个实验性的 `scoped-vue` 模式，可以为每个组件生成作用域样式，你可以安全地使用原子化 CSS 作为组件库，而无需担心与用户的 CSS 发生冲突。比如：

```html
<template>
  <div class="m-2 rounded"><slot></div>
<template>

<!-- 以下内容将被注入 bundler 中 -->
<style scoped>
.m-2{margin:0.5rem;}
.rounded{border-radius:0.25rem;}
</style>
```

我们还在尝试更多的可能性，比如支持 Web Component，MPA 情况下的 CSS 代码分割，以及模块级别的 CSS 作用域等。

## 跳过解析，不使用 AST

从内部实现上看，Tailwind 依赖于 PostCSS 的 AST 进行修改，而 Windi 则是编写了一个自定义解析器和 AST。考虑到在开发过程中，这些工具 CSS 的并不经常变化，UnoCSS 通过非常高效的字符串拼接来直接生成对应的 CSS 而非引入整个编译过程。同时，UnoCSS 对类名和生成的 CSS 字符串进行了缓存，当再次遇到相同的实用工具类时，它可以绕过整个匹配和生成的过程。

## 单次迭代

正如前文所述，Windi CSS 和 Tailwind JIT 都依赖于对文件系统的预扫描，并使用文件系统监听器来实现 HMR。文件 I/O 不可避免地会引入开销，而你的构建工具实际上需要再次加载它们。那么我们为什么不直接利用已经被工具读取过的内容呢？

除了独立的生成器核心以外，UnoCSS 有意只提供了 Vite 插件（以后可能考虑其他的集成），这使得它能够专注于与 Vite 的最佳集成。

在 Vite 中，`transform` 的钩子将与所有的文件及其内容一起被迭代。因此，我们可以写一个插件来收集它们，比如：

```js
export default {
  plugins: [
    {
      name: 'unocss',
      transform(code, id) {
        // 过滤掉无需扫描的文件
        if (!filter(id)) return

        // 扫描代码（同时也可以处理开发中的无效内容）
        scan(code, id)

        // 我们只需要内容，所以不需要对代码进行转换
        return null
      },
      resolveId(id) {
        return id === VIRTUAL_CSS_ID ? id : null
      },
      async load(id) {
        // 生成的 css 会作为一个虚拟模块供后续使用
        if (id === VIRTUAL_CSS_ID) {
          return { code: await generate() }
        }
      }
    }
  ]
}
```

由于 Vite 也会处理 HMR，并在文件变化时再次执行 `transform` 钩子，这使得 UnoCSS 可以在一次加载中就完成所有的工作，没有重复的文件 I/O 和文件系统监听器。此外，通过这种方式，扫描会依赖于模块图而非文件 glob。这意味着只有构建在你应用程序中的模块才会影响生成的 CSS，而并非你文件夹下的任何文件。

我们还做了一些小技巧来提高性能。我可能会在以后再写一篇关于它们的文章，但在此之前，你可以通过阅读代码来弄清它们 :)
