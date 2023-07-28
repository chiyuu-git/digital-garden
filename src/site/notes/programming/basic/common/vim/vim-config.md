---
{"dg-publish":true,"permalink":"/programming/basic/common/vim/vim-config/"}
---


# Vscodevim

VSCodeVim is a Vim emulator for [Visual Studio Code](https://code.visualstudio.com/).

For a full list of supported Vim features, please refer to our [roadmap](https://github.com/VSCodeVim/Vim/blob/HEAD/ROADMAP.md).

## 配置 Settings 映射 Vs Code Action

in vscode vim use: `gh`

for further infomation you can define your own settings:

```json
"vim.normalModeKeyBindingsNonRecursive": [
  {
    "before": ["g", "H"],
    "commands": [{ "command": "editor.action.showDefinitionPreviewHover" }]
  }
]
```

[vscode + vim 全键盘操作高效搭配方案 - 知乎](https://zhuanlan.zhihu.com/p/430603620)

## Vim Easy Motion

查找单个字符的感觉不是很实用

[GitHub - VSCodeVim/Vim: Vim for Visual Studio Code](https://github.com/VSCodeVim/Vim#vim-easymotion)

| Motion Command                      | Description                                                  |
| ----------------------------------- | ------------------------------------------------------------ |
| `<leader><leader> s <char>`         | Search character                                             |
| `<leader><leader> w`                | Start of word forwards                                       |
| `<leader><leader> b`                | Start of word backwards                                      |
| `<leader><leader> l`                | Matches beginning & ending of word, camelCase, after `_`, and after `#` forwards |
| `<leader><leader> h`                | Matches beginning & ending of word, camelCase, after `_`, and after `#` backwards |
| `<leader><leader> e`                | End of word forwards                                         |
| `<leader><leader> ge`               | End of word backwards                                        |
| `<leader><leader> / <char>... <CR>` | Search n-character                                           |

## Vim Surround

`t` or `<` as `<desired>` or `<existing>` will enter tag entry mode. Using `<CR>` instead of `>` to finish changing a tag will preserve any existing attributes.

| Surround Command           | Description                                              |
| -------------------------- | -------------------------------------------------------- |
| `y s <motion> <desired>`   | Add `desired` surround around text defined by `<motion>` |
| `d s <existing>`           | Delete `existing` surround                               |
| `c s <existing> <desired>` | Change `existing` surround to `desired`                  |
| `S <desired>`              | Surround when in visual modes (surrounds full selection) |

Some examples:

- `"test"` with cursor inside quotes type `cs"'` to end up with `'test'`
- `"test"` with cursor inside quotes type `ds"` to end up with `test`
- `"test"` with cursor inside quotes type `cs"t` and enter `123>` to end up with `<123>test</123>`

## Vim-commentary

Similar to [vim-commentary](https://github.com/tpope/vim-commentary), but uses the VS Code native _Toggle Line Comment_ and _Toggle Block Comment_ features.

Usage examples:

- `gc` - toggles line comment. For example `gcc` to toggle line comment for current line and `gc2j` to toggle line comments for the current line and the next two lines.
- `gC` - toggles block comment. For example `gCi)` to comment out everything within parentheses.

## CamelCaseMotion

Based on [CamelCaseMotion](https://github.com/bkad/CamelCaseMotion), though not an exact emulation. This plugin provides an easier way to move through camelCase and snake_case words.

| Setting                    | Description                    | Type    | Default Value |
| -------------------------- | ------------------------------ | ------- | ------------- |
| vim.camelCaseMotion.enable | Enable/disable CamelCaseMotion | Boolean | false         |

Once CamelCaseMotion is enabled, the following motions are available:

| Motion Command         | Description                                                  |
| ---------------------- | ------------------------------------------------------------ |
| `<leader>w`            | Move forward to the start of the next camelCase or snake_case word segment |
| `<leader>e`            | Move forward to the next end of a camelCase or snake_case word segment |
| `<leader>b`            | Move back to the prior beginning of a camelCase or snake_case word segment |
| `<operator>i<leader>w` | Select/change/delete/etc. the current camelCase or snake_case word segment |

By default, `<leader>` is mapped to `\`, so for example, `d2i\w` would delete the current and next camelCase word segment.

## Vim-textobj-arguments

Similar to the argument text object in [targets.vim](https://github.com/wellle/targets.vim). It is an easy way to deal with arguments inside functions in most programming languages.

| Motion Command | Description                        |
| -------------- | ---------------------------------- |
| `<operator>ia` | The argument excluding separators. |
| `<operator>aa` | The argument including separators. |

Usage examples:

- `cia` - change the argument under the cursor while preserving separators like comma `,`.
- `daa` - will delete the whole argument under the cursor and the separators if applicable.

| Setting                             | Description                  | Type        | Default Value |
| ----------------------------------- | ---------------------------- | ----------- | ------------- |
| vim.argumentObjectOpeningDelimiters | A list of opening delimiters | String list | `["(", "["]`    |
| vim.argumentObjectClosingDelimiters | A list of closing delimiters | String list | `[")", "]"]`    |
| vim.argumentObjectSeparators        | A list of object separators  | String list | `[","]`         |

## VSCodeVim Tricks!

VS Code has a lot of nifty tricks and we try to preserve some of them:

- `gd` - jump to definition.
- `gq` - on a visual selection reflow and wordwrap blocks of text, preserving commenting style. Great for formatting documentation comments.
- `gb` - adds another cursor on the next word it finds which is the same as the word under the cursor.
- `af` - visual mode command which selects increasingly large blocks of text. For example, if you had "blah (foo [bar 'ba|z'])" then it would select 'baz' first. If you pressed `af` again, it'd then select [bar 'baz'], and if you did it a third time it would select "(foo [bar 'baz'])".
- `gh` - equivalent to hovering your mouse over wherever the cursor is. Handy for seeing types and error messages without reaching for the mouse!

# Ideavim

官网的介绍：<https://www.jetbrains.com/help/idea/using-product-as-the-vim-editor.html>

## 配置 Vimrc 映射 Idea Action

<https://github.com/JetBrains/ideavim#executing-ide-actions>

# Vimium

[vimium-官方文档](https://link.zhihu.com/?target=https%3A//link.juejin.im/%3Ftarget%3Dhttps%3A%2F%2Fgithub.com%2Fphilc%2Fvimium)

[让你用 Chrome 上网快到想哭：Vimium](https://link.zhihu.com/?target=https%3A//link.juejin.im/%3Ftarget%3Dhttps%3A%2F%2Fsspai.com%2Fpost%2F27723)

[如何优雅地使用 Vimium？](https://link.zhihu.com/?target=https%3A//link.juejin.im/%3Ftarget%3Dhttps%3A%2F%2Fwww.zhihu.com%2Fquestion%2F23483616)

[神器vimium：比同级程序员成长更快，我主要靠它](https://link.zhihu.com/?target=https%3A//link.juejin.im/%3Ftarget%3Dhttps%3A%2F%2Fzhuanlan.zhihu.com%2Fp%2F38179086)

文本三巨头的配置 <https://www.zybuluo.com/runzhliu/note/1003402>

## 标记（锚点）

```text
ma      设置本地标记 a
mA      设置全局标记 A 
`a      跳转到本地标记 a
`A      跳转到q全局标记 a
``      跳回到跳转之前的位置 (也就是说，在执行gg，G，n，N，或/ a 之前的位置）
```

m 可以跟任何字母，做多个锚点

## 预览模式（visual mode）

预览模式跟 vim 很类似

```text
先用 / 定位，找到想要选择的字符，按 enter 然后通过 n N 选择想要的字符
    再按 v ,进入模式
    然后使用
        j：向下一行
        k：向上一行
        h：向左一个字符或标点（数字+h，可以移动多个字符）
        l：向右一个字符或标点（数字+l，可以移动多个字符）
        w：下一个标点符号后位置，包括看不见的换行符
        e：下一个标点符号前位置
        b：取消选中上一个字符，字符和标点算一个字符
```

In visual mode, `o` swaps the anchor and the focus, so you can also move the "other end" of the selection.

## Caret Mode

Caret mode is used for changing the starting point for selecting text on the page

- use `c` to enter caret mode from visual mode (or `v` followed by `c` from normal mode)
- If there is no existing selection when entering visual mode, then Vimium first enters *caret mode*. In caret mode, you can position the caret before entering visual-mode proper with `v`.
- `v` and `c` can be used to move back and forward between visual and caret modes.

Exiting visual mode:

- `Esc` - just exit visual mode
- `y` - yank the selected text to the clipboard
- `p`/`P` - send the selected text to the default search engine

## Find Mode

A good way to find the starting point for your selection is to to use [Find mode](https://github.com/philc/vimium/wiki/Find-Mode) to jump to your desired starting location on the page.

## Faq

进入了一个 input 如何退出聚焦？在聚焦状态下所有的快捷键都变成输入了，直接按 esc 退出就行

虽然 vim 有很多快捷键，但是应用默认的快捷键也还是可以使用的

如何在子节点滚动？ 比如 直播的弹幕如何翻查

# 环境配置

Vim 自带一个交互式的教程，内含你需要了解的最基础的信息，你可以通过终端运行以下命令打开教程：

```shell
vimtutor
```

不要因为这个看上去很无聊而跳过，按照此教程多练习。你以前用的 IDE 或者其他编辑器很少是有“模式”概念的，因此一开始你会很难适应模式切换。但是你 Vim 使用的越多，[肌肉记忆](https://en.wikipedia.org/wiki/Muscle_memory) 将越容易形成。

Vim 基于一个 [vi](https://en.wikipedia.org/wiki/Vi) 克隆，叫做 [Stevie](https://en.wikipedia.org/wiki/Stevie_(text_editor))，支持两种运行模式："compatible" 和 "nocompatible"。在兼容模式下运行 Vim 意味着使用 vi 的默认设置，而不是 Vim 的默认设置。除非你新建一个用户的 `vimrc` 或者使用 `vim -N` 命令启动 Vim，否则就是在兼容模式下运行 Vim！请大家不要在兼容模式下运行 Vim。

下一步

1. 创建你自己的 [vimrc](https://github.com/wsdjeg/vim-galore-zh_cn#精简的-vimrc)。
2. 在第一周准备 [备忘录](https://github.com/wsdjeg/vim-galore-zh_cn#备忘录)。
3. 通读 [基础](https://github.com/wsdjeg/vim-galore-zh_cn#基础-1) 章节了解 Vim 还有哪些功能。
4. 按需学习！Vim 是学不完的。如果你遇到了问题，先上网寻找解决方案，你的问题可能已经被解决了。Vim 拥有大量的参考文档，知道如何利用这些参考文档很有必要：[获取离线帮助](https://github.com/wsdjeg/vim-galore-zh_cn#获取离线帮助)。
5. 浏览 [附加资源](https://github.com/wsdjeg/vim-galore-zh_cn#附加资源)。

最后一个建议：使用 [插件](https://github.com/wsdjeg/vim-galore-zh_cn#插件管理) 之前，请先掌握 Vim 的基本操作。很多插件都只是对 Vim 自带功能的封装。

# Vimrc

## :Version

查看 vim 版本

以及 vimrc 的搜索顺序

使用 `:version` 命令将向你展示当前正在运行的 Vim 的所有相关信息，包括它是如何编译的。

第一行告诉你这个二进制文件的编译时间和版本号，比如：7.4。接下来的一行呈现 `Included patches: 1-1051`，这是补丁版本包。因此你 Vim 确切的版本号是 7.4.1051。

另一行显示着一些像 `Tiny version without GUI` 或者 `Huge version with GUI` 的信息。很显然这些信息告诉你当前的 Vim 是否支持 GUI，例如：从终端中运行 `gvim` 或者从终端模拟器中的 Vim 内运行 `:gui` 命令。另一个重要的信息是 `Tiny` 和 `Huge`。Vim 的特性集区分被叫做 `tiny`，`small`，`normal`，`big` and `huge`，所有的都实现不同的功能子集。

`:version` 主要的输出内容是特性列表。`+clipboard` 意味这剪贴板功能被编译支持了，`-clipboard` 意味着剪贴板特性没有被编译支持。

一些功能特性需要编译支持才能正常工作。例如：为了让 `:prof` 工作，你需要使用 `huge` 模式编译的 Vim，因为那种模式启用了 `+profile` 特性。

如果你的输出情况并不是那样，并且你是从包管理器安装 Vim 的，确保你安装了 `vim-x`，`vim-x11`，`vim-gtk`，`vim-gnome` 这些包或者相似的，因为这些包通常都是 `huge` 模式编译的。

### 查看 Vimrc 的位置

:echo $VIM

:echo $HOME

:echo $VIMRUNTIME

## 基本设置

vim ~/.vimrc

Vim 的功能特性要比 Vi 多得多，但其中大部分都没有缺省启用。为了使用更多的特性，您得创建一个 vimrc 文件。

1. 开始编辑 vimrc 文件，具体命令取决于您所使用的操作系统：

   ```vim
       :edit ~/.vimrc          这是 Unix 系统所使用的命令
       :edit $VIM/_vimrc       这是 MS-Windows 系统所使用的命令
   ```

2. 接着读取 vimrc 示例文件的内容：`:r $VIMRUNTIME/vimrc_example.vim`
3. 保存文件，命令为：`:write` 下次您启动 Vim 时，编辑器就会有了语法高亮的功能。您可以把您喜欢的各种设置添加到这个 vimrc 文件中。

要了解更多信息请输入 :help vimrc-intro

## 自定义命令 :h Map-operator

TimPope 的 commentary.vim 插件提供了一个很好的例子，此插件为 Vim 所支持的编程语言增添了注释及取消注释的命令。

注释命令以 \\{motion} 触发，它会切换指定行的注释状态。它是一个操作符命令，因此可以把它和所有动作命令结合在一起。\\ap 将切换当前段落的注释状态，\\G 会把从当前行到文件结尾间的所有内容注释掉，而 \\\ 则注释当前行。

## 自定义文本对象 :h Map-info

Vim 缺省的动作命令集已经相当全面了，但是我们还是可以定义新的动作命令及文本对象来进一步增强它。

Kana Natsuno 的 textobj-entire 插件是一个很好的例子，它为 Vim 增加了两种新的文本对象 ie 和 ae，它们作用于整个文件。

如果想用 = 命令自动缩进整个文件，我们可以执行 gg=G （就是说，先用 gg 跳到文件开头，然后用 =G 自动缩进从光标位置到文件结尾的所有内容）。

但是如果我们安装了 textobj-entire 插件的话，简单地执行 =ae 就可以了。运行这条命令时光标在哪儿并不重要，因为它总是作用于整个文件。

注意：如果我们同时安装了 commentary 和 textobj-entire 插件，就可以把它们放在一起使用。例如，执行 \\ae 会切换整个文件的注释状态。

## 一些值得注意的配置

如果你经常使用八进制，Vim 的缺省行为或许会适合你。如果不是这样，那么你可能想把下面这行加入你的 vimrc 里：set nrformats=这会让 Vim 把所有数字都当成十进制，不管它们是不是以 0 开头的。

## 插件参考

TimPope 的 commentary.vim 插件

Kana Natsuno 的 textobj-entire 插件

# Key Map

有了 vim 之后，很多原生的 keymap 应该都是用不上的了

## 配置同步策略

尽量只使用原生的 vim 配置，所以在不使用 vim 插件的前提下，基本上用不上插件管理器

> vim 就用 vim-plug neovim 就用 packer

原生配置的同步的关键就是 vimrc 文件，最好的方法就是通过 微云同步，然后软链到 vim:version 的源位置

将.vimrc 和.gvimrc 上传到名为 Vim 的目录中的在线代码存储库。使用文件名 _vimrc 和 _gvimrc 以使它们不会被隐藏，并且为了与 Windows.

Checkout 兼容目标系统上的存储库 Mac /_gvimrc 上的

创建符号链接：

~ $ ln -s my_repository/Vim/_vimrc $HOME/.vimrc

~ $ ln -s my_repository/Vim/_gvimrc $HOME/.gvimrc

On Windows 将 Vim 文件夹签出，而不是 Program Files 目录中的文件夹。SVN 会抱怨已经存在的文件，但你可以将这些文件添加到忽略列表中。

window:

```
c:\Users\Administrator\_vimrc
```

macos

```
$root\.vimrc
```

## 基本设置

### Vim 基本环境

[vim/vim: The official Vim repository (github.com)](https://github.com/vim/vim)

### 输入法

中文状态下也统一使用英文符号, 方便 f 查找

### 实现 Caps 单击为 esc，长按为 Ctrl

mac karabiner :

+ use eventViewer to check keymap
+ modified key set provide the most of key binding

win powertoy 然后搜索 键盘管理器即可设置

[将CapsLock键在单击和长按时分别映射为Esc和Ctrl | OURONGXING (orxing.top)](https://orxing.top/post/d3c3145e.html#windows)

### Vim 中英文模式切换

<https://yzlnew.com/2019/01/vim-imselect/>

<https://somenzz.cn/20210910/vim-auto-im-select/#macos>

当切换插入模式时，自动切换中英文<https://www.zhihu.com/question/303850876>

macOS 实现默认中文输入法 shift 切换中英文<https://hackers-delight.com/2019/11/11/macos-shift-to-capslock/>

Change caps_lock to control if pressed with other keys, to escape if pressed alone.

### 模拟方向键

通过 键盘方向键实现 / powertoy

[重映射方向键 (Windows 篇) - 知乎](https://zhuanlan.zhihu.com/p/412082309)

通过模式方向键, 在任何地方都可以使用 hjkl 概念的命令进行移动, 比如 `<tab-j>`

![](/img/user/programming/basic/common/vim/vim-config/image-20221120174824352.png)

### 设置搜索模式

`<C-r><C-w>` map to `<C-;><C-'>`

![智能查找](vim-search.md#智能查找)

### 设置历史命令缓存上限

Vim 缺省会记录最后 20 条命令，对内存越发便宜的现代计算机来说，保存更多历史命令只是小菜一碟，因此我们可以修改 ‘history’ 选项，以提高其保存的上限。你可以试着把下面这行内容加入 vimrc 文件 `:set history=200`

### 设置缩进

`:set shiftwidth = 4 softtabstop = 4 expandtab`

### Relative Line Number
