---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2023-02-01-Wed, 11:24:54 am","date-modified":"2023-10-24-Tue, 7:49:12 pm","permalink":"/programming/front-end/framework/engineering/pnpm/","dgPassFrontmatter":true}
---


 node_modules 黑洞的解决方案, 新时代的 npm

# Pnpm

官网: [pnpm add  | pnpm](https://pnpm.io/zh/cli/add)

一个比较好的介绍：[Pnpm: 最先进的包管理工具](https://zhuanlan.zhihu.com/p/404784010)

一个比较好的基础教程：<https://zhuanlan.zhihu.com/p/422740629>

[2022年了，你还没用pnpm吗？ - 掘金](https://juejin.cn/post/7124142007659790372)

## Pnpm Link

和 npm 的 link 不太一样, 直接通过本地包文件地址建立连接比较实用

## Peer

pnpm config set auto-install-peers true

设置自动安装 peer

## 脱离 Nodejs

具体可以参考 [https://github.com/pnpm/pnpm/discussions/3434](https://link.zhihu.com/?target=https%3A//github.com/pnpm/pnpm/discussions/3434)

- 安装 pnpm 的， 可以基本上脱离掉 nodejs 这个 runtime 去进行安装使用。
- 可以通过 pnpm 来使用不同版本的 nodejs 来去做依赖安装，类似于 nvm 提供的功能。

目前该特性其实已经到了 beta 版本，可以参考 [https://www.npmjs.com/package/@pnpm/beta](https://link.zhihu.com/?target=https%3A//www.npmjs.com/package/%40pnpm/beta) 这个包。管理不同版本的 nodejs 功能可以参考 env 这个子命令: [https://pnpm.io/cli/env](https://link.zhihu.com/?target=https%3A//pnpm.io/cli/env)

# 现在包管理实践

[Monorepo 下的模块包设计实践 - 知乎](https://zhuanlan.zhihu.com/p/456483953)

[使用vite、tsup、github actions打造丝滑的npm package开发模版 - 知乎](https://zhuanlan.zhihu.com/p/545816127)

[如何管理企业工具库在 pnpm monorepo 多仓库模式下的版本与发包 - 掘金](https://juejin.cn/post/7168277813223981063)

# 特点

## 空间占用小

pnpm 使用内容可寻址文件系统，避免了重复存储相同的包和依赖项。即使是同一个包的不同版本，pnpm 也足够智能，可以最大程度下重用代码。

如果一个包版本 1 有 500 个文件而版本 2 只有一个新文件，那么 pnpm 不会为版本 2 写入 501 个文件；相反，它将创建到原始 500 个文件的硬链接并只写入新文件。但如果是 npm ，那么版本 2 也将加载并复制原始的 500 个文件。

## 快速

pnpm 的符号链接机制是通过 hardlink 在全局里面用 **.pnpm-store** 目录来存储 node_modules 依赖里面的 hard link 地址，然后在引用依赖的时候则是通过 symlink 去找到对应虚拟磁盘目录下 (**.pnpm** 目录) 的依赖地址。通过这种方式，pnpm 可以在不同的项目之间共享依赖项，从而提高依赖项的重复使用率和安装效率。

**.pnpm-store**: pnpm 用来存储所有通过 pnpm 安装的包的地方。它是一个全局的、可共享的存储库，可以在多个项目之间共享。每个安装的包都会放在这个文件夹中，以其版本号作为文件夹名称。这个文件夹通常位于用户目录下的.pnpm-store 文件夹中，但也可以通过设置 PNPM_STORE_DIR 环境变量来指定其他位置。

**.pnpm：** pnpm 在每个项目根目录中创建的一个隐藏文件夹，用于存储关于项目的本地元数据和缓存。这个文件夹通常包含一些项目特定的文件和文件夹，例如.pnpm-lock.yaml 文件、.shrinkwrap.yaml 文件、.cache 文件夹等。.pnpm 文件夹也包含了对存储在.pnpm-store 中的依赖项的软链接。
