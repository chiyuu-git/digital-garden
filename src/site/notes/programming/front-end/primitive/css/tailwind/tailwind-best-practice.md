---
{"aliases":["tailwind 最佳实践"],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2022-10-21-Fri, 2:11:18 pm","date-modified":"2023-04-29-Sat, 8:18:05 pm","permalink":"/programming/front-end/primitive/css/tailwind/tailwind-best-practice/","dgPassFrontmatter":true}
---


# 项目实践

tailwind 的合理使用涉及方面非常广, 本章节用于记录项目, 工程方面的最佳实践, 目的是让团队的每个成员都能高效, 合理地使用 tailwind 进行开发.

主要的理念就是: 创建代码库的时候, 配置好即可, 其他开发同学可以直接享受便利

在开发之前达成共识的

> 开发实践可以一边开发一边查阅, 由 linter 和 cr 保障

## Tailwind 智能提示

[vscode 插件](https://marketplace.visualstudio.com/items?itemName=bradlc.vscode-tailwindcss)

[webstorm 插件](https://www.jetbrains.com/help/webstorm/tailwind-css.html#ws_css_tailwind_edit_config)

> 需要安装 PostCSS Language Server 才能使用针对于 PostCSS 的补全。PostCSS 也是一种格式，扩展名为 `.css` `.pcss`

## Class Sorter / Linter

如何保证原子化的样式类名称有一个比较合理的顺序呢？比如你喜欢先写宽高然后写定位，但是你的同事跟你相反，如何制定一个规范呢？

其他还期望能自动处理的包括: 类名的重复, 冲突等

### Prettier Plugin

We maintain an official [Prettier plugin](https://github.com/tailwindlabs/prettier-plugin-tailwindcss) for Tailwind CSS that automatically sorts your classes following our [recommended class order](https://tailwindcss.com/blog/automatic-class-sorting-with-prettier#how-classes-are-sorted).

使用官网维护的 prettier 插件, 但是无法排序模板字符串内的 class 文本. https://github.com/tailwindlabs/tailwindcss/discussions/7558

新建一个配置文件 `.prettierrc.json` 键入 `{}` 根据你们项目的原有配置进行配置，如果是个人项目也可以根据你个人喜好进行配置，然后在 `vscode` 中安装 `esbenp.prettier-vscode` 这个插件，然后打开你的设置搜索 `format`，将 `Format On Paste` 和 `Format On Save` 都勾选上，就可以在保存完之后自动对你的样式类的顺序进行排序，排序的规则默认是根据 `css box model` 从外到内的规则进行排序的：

`margin` - `border` - `padding` - `content`

### Eslint Plugin

因为 eslint 只是检查 js 的, 无法检查 html 和 css 文件, 使用 eslint 插件始终是有局限性.

而且同样不支持模板字符串内的排序

### Headwind

vscode 插件, 专门用于处理 tailwind 的原子类排序, 可以理解为由社区维护的一个 prettier 的 tailwind 定制版本

支持模板字符串内的 class 排序, 但是 headwind 不支持前缀排序

## Figma to Code

支持从 figma 中生成 tailwind 样式, 仅支持 tailwind v2 , 而且不支持添加前缀, 所以仅供参考

![](/img/user/programming/front-end/primitive/css/tailwind/tailwind-best-practice/image-20221028170341701.png)

![](/img/user/programming/front-end/primitive/css/tailwind/tailwind-best-practice/image-20221028165708714.png)

![](/img/user/programming/front-end/primitive/css/tailwind/tailwind-best-practice/image-20221028170357050.png)

## 与其他样式库混用时的样式覆盖问题

有可能会覆盖 boostrap、santd 的样式, 也有可能是被三方库的样式覆盖.

tailwind 定义的样式均为原子类, 原子类覆盖是否有必要避免? 同样是 text-center 的原子类会导致样式出错么?

### 第三方库的样式覆盖 Tailwind 的样式

![important](tailwind-basic.md#important)

### Tailwind 的样式覆盖了第三方库的样式

给 tailwind 的样式增加前缀

> [!warning]
> 权衡: 增加前缀后目前无法使用 linter 工具

## Debug Screens Plugin

Add the class `debug-screens` to your `<body>` tag.

https://github.com/jorenvanhee/tailwindcss-debug-screens

> 已配置

他这个界面, 可以直接看到不同屏幕大小的切换按钮, 进行切换, 需求不强, 用浏览器切换大小也挺方便的

![](/img/user/programming/front-end/primitive/css/tailwind/tailwind-best-practice/image-20221025123627364.png)

## Using Postcss as Your Preprocessor

https://tailwindcss.com/docs/using-with-preprocessors#nesting

**you don’t need to use a preprocessor with Tailwind**

preprocessor's two benefit: build-time imports instead of browser @import , nested css can both handing with postcss plugin.

tailwind 官网的建议: 使用 tailwind 时, 无需使用 css 预处理器, 如 less, sass 等

1. 因为使用 tailwind 时并不会编写太多的 css, 去除了预处理, 项目构建也会更快速
2. tailwind 是一个 postcss 插件, 与预处理器在项目构建过程中有严格的先后顺序之分, 在 less 等预处理器的样式文件中是无法使用 tailwind 的特性的

官方的建议是使用 postcss 作为项目的 " 预处理器 ", 常见的预处理器带来的几个主要特性:

1. 构建时 import 以替代浏览器的 @import 指令, 可以通过 postcss 插件实现
2. 可嵌套的 css, 该特性也可以通过 postcss 插件实现
3. css 变量, 该特性推荐使用 css 原生变量, 现代浏览器的支持已经很好了

### Postcss-import

https://tailwindcss.com/docs/using-with-preprocessors#build-time-imports

use standrad @import instead of @tailwind

Then add it as the very first plugin in your PostCSS configuration:

```js
// postcss.config.js
module.exports = {
  plugins: {
    'postcss-import': {},
    tailwindcss: {},
    autoprefixer: {},
  }
}
```

参考作者的分享: https://www.youtube.com/watch?v=J_7_mnFSLDg

```css
@import "tailwindcss/base"; 
@import "./custom-base-styles.css"; 

@import "tailwindcss/components"; 
@import "./custom-components.css"; 

@import "tailwindcss/utilities"; 
@import "./custom-utilities.css";
```
{ #3yq8io}


### Nesting

https://tailwindcss.com/docs/using-with-preprocessors#nesting

postcss-nested / postcss-nesting

### 自定义变量

using css custom variable or tailwind theme config

You may also find that most of the things you’ve used variables for in the past can be replaced with Tailwind’s `theme()` function, which gives you access to all of your design tokens from your `tailwind.config.js` file directly in your CSS:

```css
.btn {
  background-color: theme('colors.blue.500');
  padding: theme('spacing.2') theme('spacing.4');
  /* ... */
}
```

Learn more about the `theme()` function in our [functions and directives documentation](https://tailwindcss.com/docs/functions-and-directives#theme);

### Vendor Prefixes

For automatically managing vendor prefixes in your CSS, you should use [Autoprefixer](https://github.com/postcss/autoprefixer).

自动插入浏览器前缀

需要确认支持的版本

### Using Preprocessor with Tailwind

The most important thing to understand about using Tailwind with a preprocessor is that **preprocessors like Sass, Less, and Stylus run separately, before Tailwind**. This means that you can’t feed output from Tailwind’s `theme()` function into a Sass color function for example, because the `theme()` function isn’t actually evaluated until your Sass has been compiled to CSS and fed into PostCSS.

Won't work, Sass is processed first, and tailwind is a postcss plugin

需要再次强调的是, tailwind 是一个 postcss 插件, 所有预处理器会在 tailwind 之前运行. 这意味无法在 sass 样式文件中使用 theme 函数

```sass
.alert {
  background-color: darken(theme('colors.red.500'), 10%);
}
```

#### Avoid Quirks

Because Tailwind adds some new non-standard keywords to CSS (like `@tailwind`, `@apply`, `theme()`, etc.), you often have to write your CSS in annoying, unintuitive ways to get a preprocessor to give you the expected output.

Aside from that, each preprocessor has its own quirk or two when used with Tailwind, which are outlined with workarounds below.

other quirks about different preprocessor see https://tailwindcss.com/docs/using-with-preprocessors#sass

不同的预处理器和 tailwind 一起使用的时候会有一些奇怪的 bug, 具体可以看官网的总结:https://tailwindcss.com/docs/using-with-preprocessors#sass

## 减小文件体积

For the smallest possible production build, we recommend minifying your CSS with a tool like [cssnano](https://cssnano.co/), and compressing your CSS with [Brotli](https://en.wikipedia.org/wiki/Brotli).

https://tailwindcss.com/docs/optimizing-for-production

cssnano: 删除重复的样式, 把样式转换为更加简短的形式

> 后置处理, 可以到达开发实践中, 使用更短的类名的效果. [equivalent default classes](tailwind-best-practice.md#equivalent%20default%20classes)

### Html 类名过长带来的体积问题

[gzip](https://www.zhihu.com/search?q=gzip&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A1752928891%7D) 的核心是 Deflate，而它使用了 LZ77 算法与 Huffman 编码来压缩文件，重复度越高的文件可压缩的空间就越大。

即使 HTML 因为类名过多造成体积增大，由于 class 高度相似，gzip 也将会得到一个很大的压缩比例。

## Preflight

https://tailwindcss.com/docs/preflight

项目里可以自行使用 rest.css 和 normailized.css

在使用 Tailwind / Windi 时，我遇到的另一个问题就是 [样式预检 (Preflight)](https://link.zhihu.com/?target=https%3A//tailwindcss.com/docs/preflight)。预检功能重置了原生元素，并为 CSS 变量提供了一些兜底方案，在开发一个只使用 Tailwind/Windi 的新应用时，效果很棒。但当你想让它们与其他 UI 框架一起工作，或者使用 Tailwind 编写一些共享组件时，预检往往会引入许多冲突，破坏你现有的 UI。

这也导致无法安全地使用原子化 CSS 作为组件库，而无需担心与用户的 CSS 发生冲突

## Plugin

provide a set of utility class

### Forms

The `@tailwindcss/forms` plugin adds an opinionated form reset layer that makes it easier to style form elements with utility classes.

### Line-clamp

The `@tailwindcss/line-clamp` plugin adds `line-clamp-{lines}` classes you can use to truncate text to a fixed number of lines.

## Theme 自定义配置

### Screen

![screen](tailwind-basic.md#screens)

### Other Filed

https://github.com/tailwindlabs/tailwindcss/blob/master/stubs/defaultConfig.stub.js

fontSize -> text-md But still config by fontSize not text

```js
// FIXME: 其实没有生效
text: {
	xxs: '0.625rem',
	sm: '0.875rem',
	base: '2rem',
	lg: '1.125rem',
	xl: '1.25rem',
	'3xl': '1.875rem',
},
```

## Typescript Types

![typeScript types](tailwind-basic.md#typeScript%20types)

# 开发实践

## 动态样式

![class detection in-depth](tailwind-basic.md#class%20detection%20in-depth)

## 样式优先级问题

以下 red 与 blue 两个样式哪个会生效？无法确定。

```html
<div class="red blue"> </div>
```

选择器特殊性相同的样式声明, 其优先级是由样式表中的顺序决定，而非在 class 中的先后顺序。这使得通过组件扩展样式时可能会遭遇以下问题，示例如下:

```jsx
function Input ({ textAlign }) {
  // 默认居中，提供外层扩展 class 的功能, 只有 text-right 能生效
  return <input className={`text-center ${textAlign}`} />
}
```

## 减少编写传统 Css

![相比传统 css 的优点](tailwind-basic.md#相比语义化%20css%20的优点)

一些不符合直觉的 case, 都可以在这里汇总, 减少编写传统 css

如果实在要使用, 应该多考虑使用 theme screen 等 tailwind 提供的函数

### Tailwind-modifier

https://tailwindcss.com/docs/hover-focus-and-other-states

建议大家过一遍原文, 很多不够符合直觉的写法, 都可以通过修饰符在模板处解决, 而无需编写传统 css

#### Group Modifier

![group-{modifier}](tailwind-basic.md#group-{modifier})

#### Important Modifier

![Important modifier](tailwind-basic.md#Important%20modifier)

### 自定义样式

[adding custom styles](tailwind-basic.md#adding%20custom%20styles)

也是建议大家读原文的一章节

#### 使用 JIT 任意值自定义样式

[using arbitrary values](tailwind-basic.md#using%20arbitrary%20values)

#### 使用 Css 自定义样式

自定义样式, 既可以使用 css 也可以使用 plugin 定义, 究竟使用哪种比较好呢?

使用 css 的话比较符合直觉

使用 plugin 的好处是可以提供给其他代码库使用

官方建议如下, 把自定义的 css 按照不同的 layer 集中管理， 不要采用语义化 css 分散到单个组件样式中

![tailwind-best-practice](tailwind-best-practice.md#^3yq8io)

需要先介绍一下 layer, 再介绍一下 multi-css 的问题

[using css and @layer](tailwind-basic.md#using%20css%20and%20@layer)

#### 使用 Plugin 自定义修饰符

自定义修饰符就只能使用 plugin 了

## Html 类名重复、过长

### 复用样式

官方文档提到了，一个方法是组件化，一个是用@apply 把一串类组起来, 并且推荐优先使用组件化作复用.

> 纯样式组件? 但是这也很容易催生一大批只有 1 个样式、1 个标签的组件。比起直接定义 css 的效率还低；

### Avoiding Premature Abstraction

> [!note]
> Adam Wathan (Tailwind’s creator), [said this in a tweet](https://twitter.com/adamwathan/status/1226511611592085504?lang=en):
> 
> > Confession: The `apply` feature in Tailwind only exists to trick people who are put off by long lists of classes into trying the framework. You should almost never use it. Reuse your utility-littered HTML instead.
> 
> In a nutshell, this can result in maintainability issues. I built several projects, and I seldom had to rely on them. So trust me, this is possible!
> 
> If you are using a framework like Vue.js or React (where you define everything as components), it will be simple to avoid using the `@apply` feature. I rarely (if ever) use it.

Whatever you do, **don’t use `@apply` just to make things look “cleaner”**. Yes, HTML templates littered with Tailwind classes are kind of ugly. Making changes in a project that has tons of custom CSS is worse.

If you start using `@apply` for everything, you are basically just writing CSS again and throwing away all of the workflow and maintainability advantages Tailwind gives you, for example:

- **You have to think up class names all the time** — nothing will slow you down or drain your energy like coming up with a class name for something that doesn’t deserve to be named.
- **You have to jump between multiple files to make changes** — which is a way bigger workflow killer than you’d think before co-locating everything together.
- **Changing styles is scarier** — CSS is global, are you _sure_ you can change the min-width value in that class without breaking something in another part of the site?
- **Your CSS bundle will be bigger** — oof.

If you’re going to use `@apply`, use it for very small, highly reusable things like buttons and form controls — and even then only if you’re not using a framework like React where a component would be a better choice.

过多的 html 类名确实会显得臃肿, 可读性差, 但是不要为了让 html 看起来更干净而过多的使用 `@apply`, 过多的使用该指令相当于是又使用了传统的 css 写法:

1. 必须为每个节点考虑 className
2. 在不同的文件中跳转来确认样式
3. 单个 class 样式的改动可能会影响多个节点
4. css 文件会越来越大

因此, 使用 `@apply` 时需要确认这个样式会被经常服用, 比如 button, 一些 form 表单控件. 而如果是在使用 react, vue 等视图层框架, 官网也是更推荐通过组件进行复用

### Equivalent Default Classes

使用更短的类名

检查一下有哪些@apply 的定义的重复类, 按理说已经内置了足够好用的

> 官方定义的大多是 utility class, layer utility, 而这里想要的是 component 级别的

尽量使用更短的类名, 举例:

+ 使用 `mx-2` 代替 `ml-2 mr-2`
+ 使用 `p-4 sm:pt-8 xl:pt-12`, 代替 `pt-4 sm: pt-8 md: pt-8 xl:pt-12 pr-4 pb-4 pl-4`

>这里也涉及到了项目实践中的 headwind, cssnano 等工具, 也需要注意 tailwind 默认的配置下是移动优先的

Utility classes allow you to write responsive web pages without CSS, but sometimes users use multiple utility classes. The best practice is to keep your code as short as possible with all the functionalities. Users should avoid using multiple utility classes.

```html
<div class="mx-10 md:mx-20 lg:mx-32 xl:mx-44 2xl: mx-64">
</div>
```

Here in the above code you can observe multiple utility classes are being used to narrow down content as per viewport. While this can be done using a screen size utility class in a single line.

```html
<div class="max-w-lg mx-auto">
</div>
```

如果您有多个子类，则不要为每个子类指定边距或间隙，而是为父类提供特定的实用程序类。

例如：

```html
<div>
  <div class="mb-2 mt-2"></div>
  <div class="mb-2 mt-2"></div>
  <div class="mb-2 mt-2"></div>
  <div class="mb-2 mt-2"></div>
  <div class="mb-2 mt-2"></div>
</div>
```

相反，您可以在此处使用空格/间隙实用程序类：

```html
<div class="space-y-4">
<div></div>
<div></div>
<div></div>
<div></div>
<div></div>
</div>
```

> [!warning]
> 这个问题可以通过 cssnano 等工具在生产环境解决, 所以开发时使用等效的短类名更多是为了便于维护.

### 查看样式是由哪个类名指定的

[查看样式是由哪个类名指定的](../../../../basic/common/programming-tools.md#查看样式是由哪个类名指定的)

## 覆盖第三方代码库的样式问题

如果要使用 css 样式进行覆盖了, 推荐使用原生 css 进行覆盖. 不要使用 tialwind 的@layer 指令, 因为 tailwind 需要通过特殊配置才能扫描第三方库的代码, 有可能会认为 @layer 中定义的自定义样式事没有使用的, 导致在 tree-shaking 阶段被剔除

[working with third-party libraries](tailwind-basic.md#working%20with%20third-party%20libraries)
