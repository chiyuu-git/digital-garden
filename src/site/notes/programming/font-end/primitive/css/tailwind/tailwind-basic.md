---
{"dg-publish":true,"permalink":"/programming/font-end/primitive/css/tailwind/tailwind-basic/"}
---


https://tailwindcss.com/

v2 chinese document https://www.tailwindcss.cn/docs/container

重新构想原子化 CSS https://zhuanlan.zhihu.com/p/425814828

# Tailwind-modifier

## 概述

tailwind 当中一些不够复合直觉的用法, 这些用法不查阅官方文档的话很难像到应该怎么通过 utility-class 解决, 容易导致开发者编写传统 css , 应该尽量避免

https://tailwindcss.com/docs/hover-focus-and-other-states

Tailwind includes modifiers for just about everything you’ll ever need, including:

- [Pseudo-classes](https://tailwindcss.com/docs/hover-focus-and-other-states#pseudo-classes), like `:hover`, `:focus`, `:first-child`, and `:required`
- [Pseudo-elements](https://tailwindcss.com/docs/hover-focus-and-other-states#pseudo-elements), like `::before`, `::after`, `::placeholder`, and `::selection`
- [Media queries](https://tailwindcss.com/docs/hover-focus-and-other-states#media-queries), like responsive breakpoints, dark mode, and `prefers-reduced-motion`
- [Attribute selectors](https://tailwindcss.com/docs/hover-focus-and-other-states#attribute-selectors), like `[dir="rtl"]` and `[open]`
<button class="dark:md:hover:bg-fuchsia-600 ..."> Save changes </button>

## Pseudo-class

### group-{modifier}

父节点状态控制子节点样式

```html
<a href="#" class="group hover:bg-sky-500 hover:ring-sky-500">
  <p class="text-slate-500 group-hover:text-white text-sm">group modifier</p>
</a>
```

This pattern works with every pseudo-class modifier, for example `group-focus`, `group-active`, or even `group-odd`.

#### Differentiating Nested Groups

When nesting groups, you can style something based on the state of a _specific_ parent group by giving that parent a unique group name using a `group/{name}` class, and including that name in modifiers using classes like `group-hover/{name}`

#### Arbitrary Groups

https://tailwindcss.com/docs/hover-focus-and-other-states#arbitrary-groups

任意组, 通过 group 的 className 变化控制子节点的状态, 相当于是自定义状态

You can create one-off `group-*` modifiers on the fly by providing your own selector as an [arbitrary value](https://tailwindcss.com/docs/adding-custom-styles#using-arbitrary-values) between square brackets:

```html
<div class="group is-published">
  <div class="hidden group-[.is-published]:block">
    Published
  </div>
</div>
```

```css
.group.is-published .group-\[\.is-published\]\:block {
  display: block;
}
```

### peer-{modifier}

control sibling state

## Pseudo-element

### Before and After

It’s worth noting that you don’t really need `::before` and `::after` pseudo-elements for most things in Tailwind projects — it’s usually simpler to just use a real HTML element.

Save `before` and `after` for situations where it’s important that the content of the pseudo-element is not actually in the DOM and can’t be selected by the user.

## Attribute Selectors

### Data Attributes

Use the `data-*` modifier to conditionally apply styles based on [data attributes](https://developer.mozilla.org/en-US/docs/Learn/HTML/Howto/Use_data_attributes).

Since there are no standard `data-*` attributes by definition, by default we only support arbitrary values out of the box, for example:

```html
<!-- Will apply -->
<div data-size="large" class="data-[size=large]:p-8">
  <!-- ... -->
</div>

<!-- Will not apply -->
<div data-size="medium" class="data-[size=large]:p-8">
  <!-- ... -->
</div>
```

You can configure shortcuts for common data attribute selectors you’re using in your project in the `theme.data` section of your `tailwind.config.js` file:

tailwind.config.js

```js
module.exports = {
  theme: {
    data: {
      checked: 'ui~="checked"',
    },
  },
}
```

You can then use these custom `data-*` modifiers in your project:

```html
<div data-ui="checked active" class="data-checked:underline">
  <!-- ... -->
</div>
```

## Custom Modifiers

### Using Arbitrary Variants

Just like [arbitrary values](https://tailwindcss.com/docs/adding-custom-styles#using-arbitrary-values) let you use custom values with your utility classes, arbitrary variants let you write custom selector modifiers directly in your HTML.

Arbitrary variants are just format strings that represent the selector, wrapped in square brackets. For example, this arbitrary modifier selects an element only when it is the third child:

```html
<ul role="list">
  {#each items as item}
    <li class="[&:nth-child(3)]:underline">{item}</li>
  {/each}
</ul>
```

```css
.\[\&\:nth-child\(3\)\]\:underline:nth-child(3) {
  text-decoration-style: underline
}
```

### Creating a Variant Plugin

![plugin](tailwind-basic.md#plugin)

## Ordering Stacked Modifiers

When stacking modifiers, they are applied from the inside-out, like nested function calls:

```js
// These modifiers:
'dark:group-hover:focus:opacity-100'

// ...are applied like this:
dark(groupHover(focus('opacity-100')))
```

For the most part this doesn’t actually matter, but there are a few situations where the order you use actually generates meaningfully different CSS.

## Important Modifier

Alternatively, you can make any utility important by adding a `!` character to the beginning:

```html
<p class="font-bold !font-medium">
  This will be medium even though bold comes later in the CSS.
</p>
```

The `!` always goes at the beginning of the utility name, after any variants, but before any prefix:

```html
<div class="sm:hover:!tw-font-bold">
```

This can be useful in rare situations where you need to increase specificity because you’re at war with some styles you don’t control.

# Adding Custom Styles

[Adding Custom Styles - Tailwind CSS](https://tailwindcss.com/docs/adding-custom-styles)

Best practices for adding your own custom styles to Tailwind.

Often the biggest challenge when working with a framework is figuring out what you’re supposed to do when there’s something you need that the framework doesn’t handle for you.

Tailwind has been designed from the ground up to be extensible and customizable, so that no matter what you’re building you never feel like you’re fighting the framework.

This guide covers topics like customizing your design tokens, how to break out of those constraints when necessary, adding your own custom CSS, and extending the framework with plugins.

自定义样式有 4 种方法:

1. 定义 theme
2. 使用 arbitrary values, JIT 生成
3. 传统 css & @layer
4. plugins

## Customizing Your Theme

Learn more about customizing your theme in the [Theme Configuration](https://tailwindcss.com/docs/theme) documentation.

## Using Arbitrary Values

square bracket notation

```html
<div class="top-[117px] lg:top-[344px]"> <!-- ... --> </div>
```

This is basically like inline styles, with the major benefit that you can combine it with interactive modifiers like `hover` and responsive modifiers like `lg`

It’s even possible to use the [`theme` function](https://tailwindcss.com/docs/functions-and-directives#theme) to reference the design tokens in your `tailwind.config.js` file:

```html
<div class="grid grid-cols-[fit-content(theme(spacing.32))]">
  <!-- ... -->
</div>
```

### Css Function

```css
h-[calc(100%-theme(space.24))]
```

### Arbitrary Properties and Css Variables

`<div class="[mask-type:luminance]"> <!-- ... --> </div>`

This can be useful for things like CSS variables as well, especially when they need to change under different conditions:

```html
<div class="[--scroll-offset:56px] lg:[--scroll-offset:44px]">
  <!-- ... -->
</div>
```

### Handling Whitespace

When an arbitrary value needs to contain a space, use an underscore (`_`) instead and Tailwind will automatically convert it to a space at build-time:

```html
<div class="grid grid-cols-[1fr_500px_2fr]">
  <!-- ... -->
</div>
```

In situations where underscores are common but spaces are invalid, Tailwind will preserve the underscore instead of converting it to a space, for example in URLs:

```html
<div class="bg-[url('/what_a_rush.png')]">
  <!-- ... -->
</div>
```

In the rare case that you actually need to use an underscore but it’s ambiguous because a space is valid as well, escape the underscore with a backslash and Tailwind won’t convert it to a space:

```html
<div class="before:content-['hello\_world']">
  <!-- ... -->
</div>
```

If you’re using something like JSX where the backslash is stripped from the rendered HTML, use [String.raw()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/raw) so the backslash isn’t treated as a JavaScript escape character:

```jsx
<div className={String.raw`before:content-['hello\_world']`}>
  <!-- ... -->
</div>
```

### Resolving Ambiguities

Many utilities in Tailwind share a common namespace but map to different CSS properties. For example `text-lg` and `text-black` both share the `text-` namespace, but one is for `font-size` and the other is for `color`.

When using arbitrary values, Tailwind can generally handle this ambiguity automatically based on the value you pass in:

```html
<!-- Will generate a font-size utility --> 
<div class="text-[22px]">...</div> 
<!-- Will generate a color utility --> 
<div class="text-[#bada55]">...</div>
```

## Using Css and @layer

When you need to add truly custom CSS rules to a Tailwind project, the easiest approach is to just add the custom CSS to your stylesheet:

```css
@tailwind base;
@tailwind components;
@tailwind utilities;

.my-custom-style {
  /* ... */
}
```

For more power, you can also use the `@layer` directive to add styles to Tailwind’s `base`, `components`, and `utilities` layers.

### Why Need @layer

![@layer](tailwind-basic.md#@layer)

### Adding Base Styles

### Adding Component Classes

Traditionally these would be classes like `card`, `btn`, `badge` — that kind of thing.

The `components` layer is also a good place to put custom styles for any third-party components you’re using

### Adding Custom Utilities

This can be useful when there’s a CSS feature you’d like to use in your project that Tailwind doesn’t include utilities for out of the box.

### Using Multiple CSS Files

If you are writing a lot of CSS and organizing it into multiple files, make sure those files are combined into a single stylesheet before processing them with Tailwind, or you’ll see errors about using `@layer` without the corresponding `@tailwind` directive.

The easiest way to do this is using the [postcss-import](https://github.com/postcss/postcss-import) plugin:

```diff-js
module.exports = {
  plugins: {
    'postcss-import': {},
    tailwindcss: {},
    autoprefixer: {},
  }
}
```

Learn more in our [build-time imports](https://tailwindcss.com/docs/using-with-preprocessors#build-time-imports) documentation.

```ad-warning
just defined custom css in a single file would be a good idea, just like post-import
```

![tailwind-best-practice](tailwind-best-practice.md#^3yq8io)

### Layers and Per-component CSS

Component frameworks like Vue and Svelte support adding per-component styles within a `<style>` block that lives in each component file.

While you can use features like `@apply` and `theme` inside component styles like this, the `@layer` directive will not work and you’ll see an error about `@layer` being used without a matching `@tailwind` directive:

This is because under-the-hood, frameworks like Vue and Svelte are processing every single `<style>` block independently, and running your PostCSS plugin chain against each one in isolation.

```ad-warning
react, san is also the same, because all the less file are process seperately
```

That means if you have 10 components that each have a `<style>` block, Tailwind is being run 10 separate times, and each run has zero knowledge about the other runs. Because of this, Tailwind can’t take the styles you define in a `@layer` and move them to the corresponding `@tailwind` directive, because as far as Tailwind can tell there is no `@tailwind` directive to move it to.

One solution to this is to simply _not_ use `@layer` inside your component styles: You lose the ability to control the precedence of your styles, but unfortunately that’s totally out of our control because of how these tools work.

Our recommendation is that you just don’t use component styles like this at all and instead use Tailwind the way it’s intended to be used — as a single global stylesheet where you use the classes directly in your HTML:

![tailwind-best-practice](tailwind-best-practice.md#^3yq8io)

### Writing Plugins

You can also add custom styles to your project using Tailwind’s plugin system instead of using a CSS file:

```js
const plugin = require('tailwindcss/plugin')

module.exports = {
  // ...
  plugins: [
    plugin(function ({ addBase, addComponents, addUtilities, theme }) {
      addBase({
        'h1': {
          fontSize: theme('fontSize.2xl'),
        },
        'h2': {
          fontSize: theme('fontSize.xl'),
        },
      })
      addComponents({
        '.card': {
          backgroundColor: theme('colors.white'),
          borderRadius: theme('borderRadius.lg'),
          padding: theme('spacing.6'),
          boxShadow: theme('boxShadow.xl'),
        }
      })
      addUtilities({
        '.content-auto': {
          contentVisibility: 'auto',
        }
      })
    })
  ]
}
```

Learn more about writing your own plugins in the [Plugins](https://tailwindcss.com/docs/plugins) documentation.

```ad-note
using plugins just a subtitude to css file, a trade-off
```

# Functions & Directive

## @config

Use the `@config` directive to specify which config file Tailwind should use when compiling that CSS file. This is useful for projects that need to use different configuration files for different CSS entry points.

```css
@config "./tailwind.site.config.js";

@tailwind base;
@tailwind components;
@tailwind utilities;
```

The path you provide to the `@config` directive is relative to that CSS file, and will take precedence over a path defined in your PostCSS configuration or in the Tailwind CLI.

```ad-note
would be useful showing diffierent config
```

## @layer

because css priority defined by declaration order not className order

To manage this, Tailwind organizes the styles it generates into three different “layers” — a concept popularized by [ITCSS](https://www.xfive.co/blog/itcss-scalable-maintainable-css-architecture/#what-is-itcss).

- The `base` layer is for things like reset rules or default styles applied to plain HTML elements.
- The `components` layer is for class-based styles that you want to be able to override with utilities.
- The `utilities` layer is for small, single-purpose classes that should always take precedence over any other styles.

The `@layer` directive helps you control declaration order by automatically relocating your styles to the corresponding `@tailwind` directive, and also enables features like [modifiers](https://tailwindcss.com/docs/adding-custom-styles#using-modifiers-with-custom-css) and [tree-shaking](https://tailwindcss.com/docs/adding-custom-styles#removing-unused-custom-css) for your own custom CSS.

因为相同优先级的 css 声明后定义的会覆盖先定义的, 为了更好的管理原子类, tailwind 将不同的样式分为三个层级:

1. base layer, 用于 css reset 或者 html 标签的默认样式
2. component layer, 在该层级声明的样式会被 utilities layer 的覆盖
3. utilities layer, 通常是简短、单一目的的 class, 总是在样式表的末尾定义

通过 @layer 指令可以使得自定义样式整合到 tialwind 的对应层级中, 并且通过 @layer 定义的自定义样式, 可以:

1. 使用修饰符, hover:my-custom-style
2. 会被 tailwind tree-shaking, 即只有真正使用了的自定义样式, 才会被打包

```css
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer utilities {
  .content-auto {
    content-visibility: auto;
  }
}
```

```html
<div class="lg:dark:content-auto">
  <!-- ... -->
</div>
```

## theme()

is use to access theme configuration, not the darkMode function

## screen()

The `screen` function allows you to create media queries that reference your breakpoints by name instead of duplicating their values in your own CSS.

# Configuration

override or extend

You’ll get a file that matches the [default configuration file](https://unpkg.com/browse/tailwindcss@latest/stubs/defaultConfig.stub.js) Tailwind uses internally.

## Content

### Pattern Recommendation

Paths are relative to your project root, _not_ your `tailwind.config.js` file, so if your `tailwind.config.js` file is in a custom location, you should still write your paths relative to the root of your project.

https://tailwindcss.com/docs/content-configuration#pattern-recommendations

To always resolve paths relative to the `tailwind.config.js` file, use the object notation for your `content` configuration and set the `relative` property to `true`

This will likely become the default behavior in the next major version of the framework.

### Class Detection In-depth

The way Tailwind scans your source code for classes is intentionally very simple — we don’t actually parse or execute any of your code in the language it’s written in, we just use regular expressions to extract every string that could possibly be a class name.

tailwind 扫描源码生成原子类的过程非常简单: tailwind 不会去解析或者执行代码, 仅仅是进行字符串匹配, 找出每一个可能是 utility class 的字符串

tailwind 不会限制开发者必须要把类名写在 html class 中, 比如 jsx 的 className , san template 中的 class 字符串, 也可以被 tailwind 扫描, 生成正确的样式

We don’t just limit our search to `class="..."` attributes because you could be using classes anywhere, like in some JavaScript for toggling a menu:

```html
<script>
  menuButton.addEventListener('click', function () {
    let classList = document.getElementById('nav').classList
    classList.toggle('hidden')
    classList.toggle('block')
  })
</script>
```

By using this very simple approach, Tailwind works extremely reliably with any programming language, like JSX for example:

### Dynamic Class Names

The most important implication of how Tailwind extracts class names is that it will only find classes that exist _as complete unbroken strings_ in your source files.

If you use string interpolation or concatenate partial class names together, Tailwind will not find them and therefore will not generate the corresponding CSS

```html
<div class="text-{{ error ? 'red' : 'green' }}-600"></div>
```

```html
<div class="{{ error ? 'text-red-600' : 'text-green-600' }}"></div>
```

As long as you always use complete class names in your code, Tailwind will

### Working with Third-party Libraries

If you’re working with any third-party libraries (for example [Select2](https://select2.org/)) and styling that library with your own custom CSS, we recommend writing those styles _without_ using Tailwind’s `@layer` feature:

This will ensure that Tailwind _always_ includes those styles in your CSS, which is a lot easier than configuring Tailwind to scan the source code of a third-party library.

If you’ve created your own reusable set of components that are styled with Tailwind and are importing them in multiple projects, make sure to configure Tailwind to scan those components for class names:

```js
module.exports = {
  content: [
    './components/**/*.{html,js}',
    './pages/**/*.{html,js}',
    './node_modules/@my-company/tailwind-components/**/*.js',
  ],
  // ...
}
```

This will make sure Tailwind generates all of the CSS needed for those components as well.

If you’re working in a monorepo with workspaces, you may need to use `require.resolve` to make sure Tailwind can see your content files:

```js
const path = require('path');

module.exports = {
  content: [
    './components/**/*.{html,js}',
    './pages/**/*.{html,js}',
    path.join(require.resolve('@my-company/tailwind-components'), '**/*.js'),
  ],
  // ...
}
```

```ad-warning
need to careful about this rule, especialy while extracting rich-text-editor.
```

我使用 tailwindcss 有一段时间了，但是我最近遇到一个问题。如果一个第三方组件是用 tailwindcss 写的，我该如何修改它的默认样式呢？对于用 BEM 等方式命名的第三方组件，我们可以在 Vue 中使用样式穿透覆盖默认的样式，但是在 tailwindcss 中我没有想到一个好的方法。

嗨你好，对于你提出的场景，我认为有两种情况。

1. 你使用的组件是组件库提供的。以 react 为例，组件库会提供一个的组件来提供一个修改默认样式的方式。是组件内部通过 context 方式进行注入的。
2. 第三方库没有提供修改默认样式的方式，但是暴露出了通过 className、style 的方式修改组件样式。这种场景，以 react 举例子，可以对组件进行再包装，来实现样式的注入。
 3. 第三方库没有提供修改默认样式的方式，也没有暴露出通过 className、style 的方式修改组件样式。对于这种场景，既然类选择器无法使用了，需要考虑是否能通过标签选择器、标签选择器来进行选择。例如 angular 中组件使用的自定义标签。

### Using Relative Paths

By default Tailwind resolves non-absolute content paths relative to the **current working directory**, not the `tailwind.config.js` file. This can lead to unexpected results if you run Tailwind from a different directory.

To always resolve paths relative to the `tailwind.config.js` file, use the object notation for your `content` configuration and set the `relative` property to `true`:

```js
module.exports = {
  content: {
    relative: true,
    files: [
      './pages/**/*.{html,js}',
      './components/**/*.{html,js}',
    ],
  },
  // ...
}
```

This will likely become the default behavior in the next major version of the framework.

### Safelisting Classes

For the smallest file size and best development experience, we highly recommend relying on your `content` configuration to tell Tailwind which classes to generate as much as possible.

Safelisting is a last-resort, and should only be used in situations where it’s impossible to scan certain content for class names. These situations are rare, and you should almost never need this feature.

If you need to make sure Tailwind generates certain class names that don’t exist in your content files, use the `safelist` option:

tailwind.config.js

```js
module.exports = {
  content: [
    './pages/**/*.{html,js}'
    './components/**/*.{html,js}',
  ],
  safelist: [
    'bg-red-500',
    'text-3xl',
    'lg:text-4xl',
  ]
  // ...
}
```

One example of where this can be useful is if your site displays user-generated content and you want users to be able to use a constrained set of Tailwind classes in their content that might not exist in your own site’s source files.

### Troubleshooting

## Theme

[theme config](tailwind-basic.md#theme%20config)

## Preset

you can creating a preset for reusing them in other project

## Prefix

The dash modifier for negative values should be added before your prefix, so `-mt-8` would become `-tw-mt-8` if you’ve configured `tw-` as your prefix:

```html
<div class="-tw-mt-8">
  <!-- -->
</div>
```

That means if you add your own custom utility like this:

```css
@layer utilities {
  .bg-brand-gradient { /* ... */ }
}
```

…the generated variants will not have your configured prefix:

```css
.bg-brand-gradient { /* ... */ }
.hover\:bg-brand-gradient:hover { /* ... */ }
```

If you’d like to prefix your own utilities as well, just add the prefix to the class definition:

```css
@layer utilities {
  .tw-bg-brand-gradient { /* ... */ }
}
```

## Important

add important

### Selector Strategy

add \#app

When using the selector strategy, be sure that the template file that includes your root selector is included in your [content configuration](https://tailwindcss.com/docs/optimizing-for-production#basic-usage), otherwise all of your CSS will be removed when building for production.

也可以使用选择器提高优先级, 传入一个实际存在的节点 id ,通常为根节点 id, tailwind 生成的样式中都会加上这个 id 选择器, 以提高选择器优先级

```JS
module.exports={
 important:'#app',
}
```

![important modifier](tailwind-basic.md#important%20modifier)

## Referencing in JavaScript

It can often be useful to reference your configuration values in your own client-side JavaScript — for example to access some of your theme values when dynamically applying inline styles in a React or Vue component.

To make this easy, Tailwind provides a `resolveConfig` helper you can use to generate a fully merged version of your configuration object:

```js
import resolveConfig from 'tailwindcss/resolveConfig'
import tailwindConfig from './tailwind.config.js'

const fullConfig = resolveConfig(tailwindConfig)

fullConfig.theme.width[4]
// => '1rem'

fullConfig.theme.screens.md
// => '768px'

fullConfig.theme.boxShadow['2xl']
// => '0 25px 50px -12px rgba(0, 0, 0, 0.25)'
```

Note that this will transitively pull in a lot of our build-time dependencies, resulting in bigger client-side bundle size. To avoid this, we recommend using a tool like [babel-plugin-preval](https://github.com/kentcdodds/babel-plugin-preval) to generate a static version of your configuration at build-time.

## typeScript Types

We ship first-party TypeScript types for the `tailwind.config.js` file which give you all sorts of useful IDE support, and makes it a lot easier to make changes to your configuration without referencing the documentation quite as much.

Configuration files generated with Tailwind CLI include the necessary type annotation by default, but to configure TypeScript types manually, just add the type annotation above your configuration object:

tailwind.config.js

```js
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    // ...
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

# Theme Config

config your your project’s color palette, type scale, fonts, breakpoints, border radius values, and more.

We provide a sensible [default theme](https://github.com/tailwindlabs/tailwindcss/blob/master/stubs/defaultConfig.stub.js) with a very generous set of values to get you started, but don’t be afraid to change it or extend it; you’re encouraged to customize it as much as you need to fit the goals of your design.

```ad-note
project-init need full communication with ue
```

## Override or Extend

override: in one section any keys you do note provide will be eliminated

## Referencing Other Values

## Screens

You define your project’s breakpoints in the `theme.screens` section of your `tailwind.config.js` file. The keys become your [responsive modifiers](https://tailwindcss.com/docs/responsive-design) (like `md:text-center`), and the values are the `min-width` where that breakpoint should start.

The default breakpoints are inspired by common device resolutions:

在 `theme.screens` 中定义媒体属性宽度的断点, 属性名将会作为响应式修饰符, (如 md:text-center), 属性值则会作为媒体查询的 **最小宽度**

默认的断点如下所示:

```js
module.exports = {
  theme: {
    screens: {
      'sm': '640px',
      // => @media (min-width: 640px) { ... }

      'md': '768px',
      // => @media (min-width: 768px) { ... }

      'lg': '1024px',
      // => @media (min-width: 1024px) { ... }

      'xl': '1280px',
      // => @media (min-width: 1280px) { ... }

      '2xl': '1536px',
      // => @media (min-width: 1536px) { ... }
    }
  }
}
```

> Feel free to have as few or as many screens as you want, naming them in whatever way you’d prefer for your project.

### Range Breakpoint

要实现区间内生效可以使用 max-sm 这种修饰符, 感觉没必要改每个档位的范围

https://tailwindcss.com/docs/responsive-design#targeting-a-breakpoint-range

### Addding Smaller Breakpoints

If you want to add an additional small breakpoint, you can’t use `extend` because the small breakpoint would be added to the end of the breakpoint list, and breakpoints need to be sorted from smallest to largest in order to work as expected with a min-width breakpoint system.

![媒体查询的声明优先级](../layout.md#媒体查询的声明优先级)

Instead, override the entire `screens` key, re-specifying the default breakpoints

We expose the default theme at `tailwindcss/defaultTheme` so you don’t have to maintain the list of default breakpoints yourself.

```js
const defaultTheme = require('tailwindcss/defaultTheme')

module.exports = {
  theme: {
    screens: {
      'xs': '475px',
      ...defaultTheme.screens,
    },
  },
  plugins: [],
}
```

官方暴露了默认的 theme config, 可以像这样添加

### Working Mobile-first

![媒体查询的声明优先级](../layout.md#媒体查询的声明优先级)

媒体查询中样式的声明顺序, 会和 theme.screens 中的字段顺序保持一致, 这意味着:

`text-left sm:text-center lg:text-right`

+ viewport width 375px -> text-left
+ viewport width 640px -> text-center
+ viewport width 768px -> text-center
+ viewport width 1024px -> text-right

在 tailwind 默认的 screens 配置中, 项目开发应该遵循先开发移动端页面, 再对更大的屏幕做响应式的适配.

#### 如果我们使用默认配置, 先开发 PC 端页面, 再去适配移动端, 会发生什么

如果我们想着的是先开发 PC 端页面, 意味着 PC 端页面的样式都没有使用响应式修饰符, 此时使用默认的 screens 配置适配移动端就很麻烦了.

> 以上面的例子来看, pc 端页面和 viewport width < 640px 的移动设备会应用 text-left

```ad-warning
关于每个档位的定义, 团队需要在项目启动阶段达成共识
```

### Max Width Breakpoints

tailwind 的默认配置形式为最小宽度断点, 适用于移动优先, 大屏作适配

与之相对的就是最大宽度断点, 适用于先开发大屏, 再适配移动端

同样需要注意字段的顺序

Make sure to list max-width breakpoints in descending order so that they override each other as expected.

```js
module.exports = {
  theme: {
    screens: {
      '2xl': {'max': '1535px'},
      // => @media (max-width: 1535px) { ... }

      'xl': {'max': '1279px'},
      // => @media (max-width: 1279px) { ... }

      'lg': {'max': '1023px'},
      // => @media (max-width: 1023px) { ... }

      'md': {'max': '767px'},
      // => @media (max-width: 767px) { ... }

      'sm': {'max': '639px'},
      // => @media (max-width: 639px) { ... }
    }
  }
}
```

## Colors

color use default for simpler className

如果设计同学提供了项目中的主题色，并且有语义化的名称，比如类似 `success`，`info`，`warning` 这种语义化的颜色，我们就可以基于这些来配置我们的颜色，包括但不限于字体、背景、边框、阴影颜色 (配置完之后直接可以使用类似 `text-success` 的类来设置颜色)，可以替换 `css` 预处理器的变量功能

```js
// tailwindcss v3
const colors = {
  'success': '#654321'，
  'info': '#123456',
  'warning': '#666666',
  // ...
}
module.exports = {
  content: ['./index.html', './src/**/*.{vue,js,ts,jsx,tsx}'],
  theme: {
    // ...
    colors,
  },
  plugins: [],
}
```

### 多主题

https://tailwindcss.com/docs/customizing-colors#using-css-variables

可能你维护的是一个需要支持多主题的项目，不同的情况下有多种配色方案，在 `tailwindcss` 中配合 `css var` 来实现多主题配色会简单到让你窒息：

在你的全局 `css` 文件中配置主题，假设我们有 `success`、`info`、`warning` 这三种不同的主题配色

```text
/* global base css */
@tailwind base;
@tailwind components;
@tailwind utilities;
// 默认主题
:root {
  --success: 5 193 174;
  --info: 51 163 238;
  --warning: 237 214 18;
}
// 主题1的配色
.theme-1 {
  --success: 36 195 102;
  --info: 54 143 255;
  --warning: 234 209 27;
}
// 主题2的配色
.theme-2 {
  --success: 57 209 121;
  --info: 0 186 255;
  --warning: 234 209 27;
}
```

然后到我们的 `tailwind.config.js` 中改变我们的颜色配置

```text
// 让我们的颜色支持透明度设置
function withOpacityValue(variable) {
  return ({ opacityValue }) => {
    return opacityValue === undefined
      ? `rgb(var(${variable}))`
      : `rgb(var(${variable}) / ${opacityValue})`
  }
}

const colors = {
  success: withOpacityValue('--success'),
  info: withOpacityValue('--info'),
  warning: withOpacityValue('--warning'),
  // ...
}
module.exports = {
  content: ['./index.html', './src/**/*.{vue,js,ts,jsx,tsx}'],
  theme: {
    // ...
    colors,
  },
  plugins: [require('@tailwindcss/line-clamp')],
}
```

最后根据不同的情况设置你的主题，对要设置的主题的顶级元素设置对应的 `class` 即可，内部的所有颜色样式都会对应特定的主题而改变！

```text
<!-- 默认主题 -->
<div>
  <!-- ... -->
</div>
<!-- 主题1 -->
<div class="theme-1">
  <!-- ... -->
</div>
<!-- 主题2 -->
<div class="theme-2">
  <!-- ... -->
</div>
```

## Spacing

1rem = 16px = 浏览器默认字体大小 = space.4

# Plugin

add style , should use custom css

adding variants modifier use plugin

If you find yourself using the same arbitrary modifier multiple times in your project, it might be worth extracting it to a plugin using the `addVariant` API:

```js
let plugin = require('tailwindcss/plugin')

module.exports = {
  // ...
  plugins: [
    plugin(function ({ addVariant }) {
      // Add a `third` variant, ie. `third:pb-0`
      addVariant('third', '&:nth-child(3)')
      addVariant('optional', '&:optional') 
      addVariant('hocus', ['&:hover', '&:focus']) 
      addVariant('supports-grid', '@supports (display: grid)')
    })
  ]
}
```

The first argument is the modifier name that users will use in their HTML, so the above example would make it possible to write classes like these:

```html
<form class="flex supports-grid:grid ...">
  <input class="optional:border-gray-300 ..." />
  <button class="bg-blue-500 hocus:bg-blue-600">...</button>
</form>
```

# Preflight

# Tailwind 优缺点

tailwind = 原子化 css + 设计系统约束 + 响应式设计 (以及各种各样的修饰符) + 自定义能力强

## 优点

### 相比语义化 Css 的优点

不用起名, 改样式不用在文件中跳来跳去

**Making changes feels safer**. CSS is global and you never know what you’re breaking when you make a change.

不用担心改了一个样式影响其他节点

> &- 语法, 有时无法通过代码看出究竟哪些节点受到了该样式的影响

With CSS files this small, you don’t have to worry about complex solutions like code-splitting your CSS for each page, and can instead just ship a single small CSS file that’s downloaded once and cached until you redeploy your site.

不用拆分 css ,不需要 css module 了, 各个页面都共用同一份 css 即可, tailwind 产出的样式文件只是有限原子类的集合, 文件很小

真正的组件化, 三剑客合而为一

### 相比行内样式的优点

But using utility classes has a few important advantages over inline styles:

- **Designing with constraints**. Using inline styles, every value is a magic number. With utilities, you’re choosing styles from a predefined [design system](https://tailwindcss.com/docs/theme), which makes it much easier to build visually consistent UIs.
- **Responsive design**. You can’t use media queries in inline styles, but you can use Tailwind’s [responsive utilities](https://tailwindcss.com/docs/responsive-design) to build fully responsive interfaces easily.
- **Hover, focus, and other states**. Inline styles can’t target states like hover or focus, but Tailwind’s [state variants](https://tailwindcss.com/docs/hover-focus-and-other-states) make it easy to style those states with utility classes.

相比行内样式的优点:

1. 设计语言的约束, 可以减少魔法数字, 颜色等的使用
2. 修饰符, 响应式修饰符, hover、focus 等状态修饰符

### 相比其他样式库的优点

boostrap 同样自带了很多样式

TailwindCSS 不仅是内置了很多样式，也支持通过配置文件去配置，覆盖掉内置的样式，或者扩展自己的样式

1. 设计语言的约束, 可以减少魔法数字的使用
2. 修饰符, 响应式修饰符, hover、focus 等状态修饰符

## 缺点

html 类名太长

没有提供 component 级别的内置样式, 经常需要组合, 典型的 flex 系列, 垂直水平居中这种非常常用的也没有提供

tailwind css 覆盖组件库的样式

jian

如果使用 tailwind css 构建组件库, 也可能反过来被开发者的冲突样式覆盖

```ad-note
https://dev.to/jaredcwhite/why-tailwind-isn-t-for-me-5c90

以现在的眼光来看, 这篇文章完全是过时的, 用 js 变量并没有什么不好的, 论兼容性比 css 变量高到不知道哪里去了
```

## 思考

「Atomic/Utility-First CSS」，是和「Semantic CSS 」(语义化 CSS ) 相对的一种 CSS 规范。不管你是否听说过「Semantic CSS 」这个名词，实际项目开发中，它都是我们最常用、也是最传统的 CSS 规范。

现在看来， 「Atomic/Utility-First CSS」比 「Semantic CSS 」的可维护性高很多。但是有一件事情需要注意：「Atomic/Utility-First CSS」并不是 Tailwind CSS 提出的，实际上，「Atomic/Utility-First CSS」的诞生远远早于 Tailwind CSS ：basscss（2013 年）、tachyons（2014 年），当然还有很多其他类似的 CSS 库。

那么，为什么在这么长的时间里，「Atomic/Utility-First CSS」并没有真正流行起来呢？

因为，只有 「Atomic/Utility-First CSS」是不够的，前端开发需要一套完整的设计系统（Design System）。

1. html 是最先出现的技术, 起初的目的是用来加强纯文本的展示, 后来才有的 css 和 js, 其他后续出现的 GUI 技术比如 java swing 和 flutter 视图层, 交互层, 逻辑层都是是紧密结合的
2. react, vue 前端组件化带来的架构分层的变化, 从 html + css + js 的三剑客, 变成以组件化为基础. 在 jQuery 时代, class 还是获取元素的重要参考, 现代组件化开发中 class 的重要性越来越低

[不合时宜的 CSS：Tailwind CSS 引发的思考](https://zhuanlan.zhihu.com/p/425134873)

让大家自己去看吧, 我就不洗稿了, 简单总结一下就是:

### 相比其他 GUI 技术的优势

css 可以随意覆盖第三方库的样式, 如果是 GUI 语言提供的组件, 要怎么覆盖样式?

css 作为单独的文件, 可以单独提供给外部使用, 比如 bootstrap, tailwind

tailwind 的形式, 应该说是与 flutter 相似的, 声明式 UI + 行内样式

声明式 UI, 布局,样式, 逻辑, 三者不分离, 技术趋同是永远的趋势

# Tailwind 响应式

tailwind 是移动优先的, 所以 config 会有很多移动端视角的配置, 如果视角没有转换过来的话, 会造成理解上的困难.

响应式方案的核心就是媒体查询, 媒体查询就是响应式布局

另一个关键就是理解 flexbox 和 viewport 两种方案的差异, 以及两种方案在 tailwind 上的表现差异

我需要思考和解决以下几个问题:

1. PC 端布局和移动端布局的差异
2. 响应式布局是为了解决什么问题
3. 响应式布局有哪些常用的方案, 不同的方案是如何结合在一起的
4. 响应式布局会有哪些常见的问题, tailwind 针对这些问题要如何解决

因为 tailwind 只是提供了响应式的支持, 并不是一款为了解决响应式问题而生的框架

> Tailwind 使用更直观的类似 {screen}: 的命名前缀，这样可以很容易地注意到哪些是 [响应式类](https://www.zhihu.com/search?q=%E5%93%8D%E5%BA%94%E5%BC%8F%E7%B1%BB&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A2373533356%7D)（class），同时还能保持原始类名（class name）的可识别性和完整性。

```text
<div class="justify-start sm:justify-center md:justify-end lg:justify-between xl:justify-around ...">
  <!-- ... -->
</div>
```

常见响应式布局的 tailwind 写法, 比如经典的大屏左右布局, 小屏上下布局,

https://tailwindcss.com/docs/responsive-design

## Rem , Px 单位需要自己配置

因为 `tailwindcss` 默认长度相关的配置是基于 `rem` 的，而 `PC` 端的项目大多数时候我们都是固定一个宽度，左右留白，所以大多数情况下，设计稿都会在固定一个宽度内，元素的大小宽高边距单位都是 `px`，所以我们需要对默认的做一些特定配置来适配我们的项目

```text
const spacing = {
  0: 0,
  4: '4px',
  8: '8px',
  12: '12px',
  // ... 项目中常用的都可以配置
}
module.exports = {
  content: ['./index.html', './src/**/*.{vue,js,ts,jsx,tsx}'],
  theme: {
    // v3 & v2
    spacing,
    lineHeight: spacing,
    borderWidth: spacing,
    borderRadius: spacing,
  },
  plugins: [],
}
```

### Js 定义 Css 复合类

Set some settings for your container in your configuration file and it will automatically be applied to all `.container` classes.

```js
// tailwind.config.js
module.exports = {
  theme: {
    container: {
      center: true,
      padding: "1.5rem",
    },
  },
};
```

So, there is no need to do this: `<div class="container p-6 mx-auto"></div>

Now, you can just do this: `<div class="container"></div>`

## 移动端适配

可能对于移动端适配，现在流行的就是 `viewport` 方案了，也可能有的项目还在用 `flexable` 方案，但是我们又不想手动换算 `px` 到 `rem` 或 `vw`，虽然社区也有类似 `pxtorem` 或者 `pxtovw` 这种 `postcss` 的插件，但解决问题的方法还是不够优雅，可能是因为插件的维护的不积极，可能是插件不好用，不兼容 `postcss8`(`pxtovw` 说的就是你 )，既然我们都有 `tailwindcss` 了，那就让这些配置变的更简单一些吧！如果你们设计同学提供了常用的间距方案，比如 `4px` 的倍数或者 `6px` 的倍数，现在假设设计同学的设计稿都是 `750px` 的，我们就可以基于此来写两个函数方法来处理 `pxtorem` 和 `pxtovw` 的任务，如果你们是 `flexable` 方案就用 `pxToRem`，如果是 `viewport` 的适配方案就用 `pxToVmin`。

```text
function pxToRem(variable) {
  return `${variable / 75}rem`
}

function pxToVmin(variable) {
  return `${variable / 7.5}vmin`
}
// flexable
const fontSize = {
  12: pxToRem(12),
  14: pxToRem(14),
  16: pxToRem(16),
  ...
}, spacing = {
  0: 0,
  4: pxToRem(4),
  8: pxToRem(8),
  12: pxToRem(12),
  ...
}
// viewport
const fontSize = {
  12: pxToVmin(12),
  14: pxToVmin(14),
  16: pxToVmin(16),
  ...
}, spacing = {
  0: 0,
  4: pxToVmin(4),
  8: pxToVmin(8),
  12: pxToVmin(12),
  ...
}
module.exports = {
  content: ['./index.html', './src/**/*.{vue,js,ts,jsx,tsx}'],
  theme: {
    // ...
    fontSize,
    spacing
  },
  plugins: [],
}
```

当然圆角大小，边框宽度等都可以这么配置，是不是比使用插件优雅多了

# Faq

#faq/ui

tailwind 有动画类么? 有的

我们的项目有设置 purge 么? 不需要了, v3 已经是 JIT 引擎了, 不需要做删除

base component utility 三个层级, 都有哪些样式? 默认主要是 base 和 utility

## 使用前缀 or Headwind?

原子类其实不怕被覆盖, 是否真的需要 prefix

## 纯样式 Diff 组件

## 设计稿的尺寸和 rem,px 这些配置, 我还是没有搞明白

设计稿大小, viewport?

## Monorepo Config

多包如何配置? 既要公用一份配置, 又要不过多的扫描, 生产多余的类

包扫描配置, 包路径配置, 参考 content

## Css to Tailwind

根据 css 生成合适的工具类, 尽量使用更短的工具类

单个属性可以直接在官网查询, 但是复合属性的工具类呢? 比如最常用的 flex + center + center 是否会有一个工具类?

官方几乎没有提供这种复合类, 都是原子类

## Naview 样式解析性能思考

如果转换成只支持原子类, 那不就是和 java 那种写法直接写 attribute 一样了么, 难道前端这么多年来都走错路了,

> 甚至 windicss 就支持直接写 attribute, 感觉前端二十年都在走弯路

想起了当年美团的 填鸭方案

## 不加前缀的话, 很多功能都能利用起来

咨询为什么需要前缀, 考虑前缀其他替代方案

有可能 tailwind 定义的原子类和 antd 的原子类是相同的, 难道相同的原子类会具有不同的功能吗? 那有怎么叫原子类呢?

尽量思考不加前缀的方案

# TODO

## 使用 Svg Tag or Iconfont

参考作者的分享: https://www.youtube.com/watch?v=J_7_mnFSLDg

svg 的 fill 属性, 可以指定为 currentColor, 自动跟随 color 属性, 可以很方便的切换颜色, 不用设置过多的逻辑
