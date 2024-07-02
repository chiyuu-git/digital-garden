---
{"aliases":["编程软件"],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2022-10-03-Mon, 9:38:23 pm","date-modified":"2024-07-02-Tue, 10:31:40 am","permalink":"/programming/basic/common/programming-tools/","dgPassFrontmatter":true}
---


4/22

# 数据分析网站

市场份额分析：<http://gs.statcounter.com/platform-market-share/desktop-mobile-tablet/china/#monthly-201805-201905>

# Emmet 语法

<https://github.com/paddingme/Learning-HTML-CSS/issues/17>

## Html 标签

console.log(‘hello’); hello.log→

div.box$*3

生 成分 组的 标签 : `()` 例如输入 `ul>(li>a)*5` 则生成

```html
  <ul>
      <li><a href=""></a></li>
      <li><a href=""></a></li>
      <li><a href=""></a></li>
      <li><a href=""></a></li>
      <li><a href=""></a></li>
  </ul>
```

注意和 `ul>li>a*5` 生成是不一样的

```html
  <ul>
      <li>
          <a href=""></a>
          <a href=""></a>
          <a href=""></a>
          <a href=""></a>
          <a href=""></a>
      </li>
  </
```

生成递增的属性标签 等: `$` 例如输入 `ul>li.item$*5` 则生成

  ```html
    <ul>
        <li class="item1"></li>
        <li class="item2"></li>
        <li class="item3"></li>
        <li class="item4"></li>
        <li class="item5"></li>
    </ul>
  ```

生成多位递增的呢 ：`$ $$` 例如输入 `ul>li.item$$$*5` 则生成

  ```html
    <ul>
        <li class="item001"></li>
        <li class="item002"></li>
        <li class="item003"></li>
        <li class="item004"></li>
        <li class="item005"></li>
    </ul>
  ```

想生成几位输入几个 `$`

## Css 属性

```plainText
h200→height:200px;
h200p→height:200%;
r0→right:0;
posa
pose
```

# LaTex 语法

[MarkDown 和LaTex语法学习（基于软件Typora）_只想摸鱼的博客-CSDN博客_markdown和latex](https://blog.csdn.net/qq_43191451/article/details/112609975)

# 浏览器

按住 Shift，可以改变颜色的显示形式

![1553414202003](/img/user/programming/basic/common/programming-tools/1553414202003.png)

![1553414209231](/img/user/programming/basic/common/programming-tools/1553414209231.png)

控制器按下 esc，用来模拟网络环境

## 查看渲染图层

![1553414138954](/img/user/programming/basic/common/programming-tools/1553414138954.png)

layer 查看图层

rebdering 查看渲染

控制台可以直接输入控制语句然后看到结果

sources 可以进行断点查看

## 查看事件监听数量

![1558251504875](/img/user/programming/basic/common/programming-tools/1558251504875.png)

## 调整网速

![1557456467870](/img/user/programming/basic/common/programming-tools/1557456467870.png)

## 维持 Hover

![1553414170259](/img/user/programming/basic/common/programming-tools/1553414170259.png)

## 查看样式是由哪个类名指定的

即使实在有过多的 CSS Class，也可以 **通过 `Computed` 面板中的小箭头跳转过去找到相对应的 class**

![](/img/user/programming/basic/common/programming-tools/image-20221021185628548.png)

## 安卓模拟器更多的机种

![1553414236960](/img/user/programming/basic/common/programming-tools/1553414236960.png)

可以控制选择默认显示机种，比如添加 ip6

## 查看 Cookies

![1553414258942](/img/user/programming/basic/common/programming-tools/1553414258942.png)

## 查看页面加载的瀑布流

O1:naview 性能渲染 okr 第一个 性能上最重要的手段

O3 有个 talos 的

年度 okr 占了两条

# Window

直接在文档目录路径下输入 cmd，可以调出当前目录的 cmd

## 多显示器快速切换

好在，Windows 系统自带 **WIN + Shift + 方向左右** 快捷键，可以将活动窗口移动到其它屏幕。

[(99+ 封私信 / 81 条消息) 请问win10接双显示器（扩展模式）怎么把鼠标游标切换到第二台显示器上？ - 知乎 (zhihu.com)](https://www.zhihu.com/question/50002939)

Windows 键 + 向上箭头 – 最大化活动窗口，使其占据整个桌面。

Windows 键 + 向下箭头 – 如果活动窗口最大化，则将其设置为较小的尺寸。如果窗口未最大化，则将窗口隐藏到任务栏中。

## 截图

使用 Windows 附件中的截图工具 Snipping Tool

默认的快捷键是 win + \<S-s>

可以把这个截图工具设置到键盘驱动上？没啥必要，改的越多，切换的成本就越大，还是用原生的最好。

## 生成目录结构

利用 window 的 tree 命令

tree /f > list.txt

忽略文件夹

```ja
tree -I "node_modules"
```

## 软链接和硬链接

mklink h

### 为目录创建软链接，修改双向同步

```
mklink /j ".\source_link" "..\source"
```

### 为文件创建硬链接，修改双向同步

硬链接的优点：

+ 兼容性好
+ 盘符被更改也不会受影响
+ 无需管理员的权限

硬链接的缺点：

+ 必须在同一个分区
+ 仅支持本地驱动器
+ 不支持为文件夹创建硬链接

# Mac

## 多显示器操作

https://www.irradiatedsoftware.com/sizeup/

mouse move : catch mouse

> 因为 mac 预设的快捷键与 win 的多窗口操作是冲突的, 正好都改成 vim 风格的快捷键, 研究一下 win 要怎么改就行了，win 的貌似不好改，还是想想 mac 怎么改吧

再改一下开机启动即可

## 基本配置

启动台：收缩拇指和其他三指

command+c 复制

command+option+v 剪切

独立设置触控板和鼠标的方向

设置 control 和 command 互换

![img](/img/user/programming/basic/common/programming-tools/ac995ad4c5eede8451302fb41fe6e01a.png)

## 文件与目录

### Mac 新建文件

我已经习惯呼出一个命令行窗口然后 touch 1.txt 了 23333

open 命令打开文件

### 显示隐藏文件

#### 方法 1

进入 Finder（访达）后，按住

`Command(⌘) + Shift(⇧) + .` 三个按键即可可以显示隐藏文件、文件夹;

当再按一次时，恢复隐藏；

#### 方法 2

在终端（Terminal）输入如下命令，即可显示隐藏文件和文件夹

`defaults write com.apple.finder AppleShowAllFiles -boolean true ; killall Finder`

如需再次隐藏原本隐藏的文件和文件夹，可以输入如下命令

`defaults write com.apple.finder AppleShowAllFiles -boolean false ; killall Finder`

### 打印目录结构

<https://www.jianshu.com/p/9411d60950bf>

### 软链接和硬链接

概念上与 windows 的链接相同，只是执行的命令不同。

> Finder 可以创建别名，但它们不会像符号链接那样工作。 别名就像 Windows 上的桌面快捷方式。 它们不被视为真实，透明的符号链接。

以下列形式运行 `ln` 命令。 您可以指定目录或文件的路径：

```bash
  ln -s / path / to / original / path / to / link 
```

> 需要注意的是，为文件创建链接时可以使用相对路径，为目录创建链接时只能使用绝对路径

```bash
ln -s ./remote-note/.obsidian.vimrc ./baidu-note/.obsidian.vimrc

ln -s /Users/pangjing02/note/remote-note/.obsidian /Users/pangjing02/note/baidu-note/.obsidian
```

`-s` 这里告诉 ln 命令创建一个符号链接。 如果你想创建一个硬链接，你可以省略 `-s` 。 大多数时间的符号链接是更好的选择，所以不要创建一个硬链接，除非你有这样做的具体原因。

如果您的文件路径包含空格或其他特殊字符，则需要将其括在引号中

您可以像任何其他类型的文件一样删除符号链接。 例如，要删除 Finder 中的符号链接，请 Ctrl + 单击或右键单击，然后选择“移至垃圾箱”。

## 权限

因为我们服务器使用的是 Linux 系统，默认的目录权限没有全部开启的，造成执行创建文件的时候报错；

所以解决 mkdir() permission denied 的问题最直接的方式，把 runtime 权限放开，让所有用户都可以创建它。

chmod -R 777 runtime（开放权限的目录）

同样的功能可以通过图形化的操作实现：

1. 右键 显示简介
2. 最下面可以修改读写权限
3. 点击 settings，将修改应用到所包含的所有文件夹即可

<https://learnku.com/laravel/t/5340/linux-solution-to-modify-the-file-permissions-caused-by-the-git-record-file-changes>

修改权限对 git 造成了影响，需要设置 git 不跟踪文件权限

## 进程

### 查看端口被哪个程序占用

sudo lsof -i tcp:port

 如：`sudo lsof -i tcp:8080`

### 看到进程的 PID，可以将进程杀死

sudo kill -9 PID

 如：`sudo kill -9 750`

mocha -t 240000 ctsAutoCase/case/swan-components/formComponent/input.js --device-type ios --reporter mochawesome

## 删除 Xcode 的模拟器

<https://juejin.cn/post/6857308979187023880>

## 环境变量

### Bash 终端环境变量

```
vi ~/bash_profile
```

**方式一**

1. 为在弹出的.bash_profile 文件内进行编辑
2. 更新配置过的环境变量 输入 source .bash_profile

**方式二**

在.bash_profile 文件为锁定状态的时候，第一种方式是不可行的，那么用第二种方式来编辑.bash_profile 文件

 第二种方式，在 Terminal 终端通过指令来对.bash_profile 文件进行编辑

输入 vim .bash_profile

输入 i 进行编辑模式

然后把需要编辑的内容键入，编辑完之后直接按 esc 退出编辑模式，

输入:w 进行文件的保存，:wq 为保存并退出指令

备注：在实际开发过程中，遇到了没有写入权限的问题

参考：<http://blog.sina.com.cn/s/blog_8444778b0102x68e.html>

### 刷新终端环境变量

source .bash_profile

### 新版 Mac

默认终端是 zsh, 得改 .zshrc

## 改 Host

```
sudo vi /etc/hosts

sudo killall -HUP mDNSResponder
```

### Win

[Win11如何修改hosts文件？Win11修改hosts文件的方法 - 系统之家](https://www.xitongzhijia.net/xtjc/20220526/245151.html)

```
ipconfig /flushdns
```

# Iterm 2

## 恢复窗口

记录当前窗口的打开的所有 tab 的目录位置，之后可以重新打开

<https://apple.stackexchange.com/questions/22445/how-can-i-save-tabs-in-iterm-2-so-they-restore-the-next-time-the-app-is-run>

window > save window arrangement > restore

# Vscode

## 快捷键

+ 侧边栏同步滚动：关闭所有文件，在第一个文件右键：选择以进行比较，第二个文件：与已选项目进行比较
+ 更改工具栏显示的名称：<https://code.visualstudio.com/updates/v1_10#_configurable-window-title>

    <https://xbuba.com/questions/38483687>

+ 按住 alt，光标可以选择多处，同时进行编辑，更多请参考：<https://www.jb51.net/softjc/609020.html>

### 快速提取函数

选中一段代码，右键，选择重构即可

会自动生成函数依赖的变量以及类型

### 跳转/切换焦点

+ `<C - \>` 新增侧边栏
+ `<C - w>` 调出窗口面板，输入窗口的文件名快速跳转
+ `<C - ~>` 顺序切换多个编辑器
+ `<C - number>` 编辑器的切换，c 2 可以跳到侧边栏
+ `<Cmd - number>` 单个编辑器内文件的跳转

## 创建一个纯净的 Vscdoe 环境

我想要同步别人的 vim 设置，但是我害怕污染，只能以工作区来做区别，不好处理

关键是工作区是否需要包含在 vscode 打开的项目中？

## 同步 Vscode 设置

使用 vscode 自带的同步即可，打开齿轮，登录 github 同步

## 扩展&配置

### 打开配置文件

`<C-S-p>` 搜索 setting

### 调整目录，文件 Import 路径自动更新

需要在根目录加一个 jsconfig.json，让 vscode 知道是 js 代码库，应该就可以更新了

```
    "javascript.updateImportsOnFileMove.enabled": "prompt",
    "typescript.updateImportsOnFileMove.enabled": "prompt",
```

<https://www.youtube.com/watch?v=SRN2J4P8O60>

### **[Regex Previewer](https://link.zhihu.com/?target=https%3A//marketplace.visualstudio.com/items%3FitemName%3Dchrmarti.regex)**

### Paste JSON as Code

将 JSON 解析成类型

在线地址：<https://app.quicktype.io/>

vscode 插件

### Hover Preview

hover 的时候按下 control 可以查看 interface 的详细结构

### 自动给英文中文之间加空格的插件

github pangu 盘古项目

如何把 pangu 运用到所有的文本编辑器呢？包括 typora vscode chrome

# Vscode 调试

为什么不调试 chrome 项目? 因为 chrome 还可以看 element 等等, 比 vscode 更加实用.

如果是 node 项目, 使用 vscode 调试倒是很实用.

## 无法查找到到 Node 命令

因为 nvm 把全局变量里的 node 删除了, node 被移动到 nvm 目录下了

查看 node 的真实 path

```zsh
which node
```

## TS

![ts-config](../../front-end/framework/typescript/ts-config.md#调试)

## Express 类型

### 特点

单文件作为启动入口

### 编写 launch.json

VsCode 左侧第四个按钮是调试按钮，默认是『没有配置』。点击右侧的齿轮状图标，选择 Node.js 会在项目根目录下创建 .vscode 的文件夹及 launch.json 文件。launch.json 内容如下：

```json
{
    // 使用 IntelliSense 了解相关属性。 
    // 悬停以查看现有属性的描述。
    // 欲了解更多信息，请访问: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "type": "node",
            "request": "launch",
            "name": "启动程序",
            "program": "${workspaceFolder}/server/index.js"
        }
    ]
}
```

默认会访问 server 下的 index.js 文件，但是这个项目的入口文件是 app.js，因此需要将 `index.js` 改为 `app.js`。

**在 launch.json 中会使用到一些预定变量，这里说明一下：**

+ `${workspaceRoot}`：VSCode 中打开文件夹的路径
+ `${workspaceRootFolderName}`：VSCode 中打开文件夹的路径, 但不包含 "/"
+ `${file}`：当前打开的文件
+ `${fileBasename}`： 当前打开文件的文件名, 不含扩展名
+ `${fileDirname}`： 当前打开文件的目录名
+ `${fileExtname}` 当前打开文件的扩展名
+ `${cwd}`：当前执行目录

#### skipFiles

当我们在单步调试程序的时候，会进入 node_modules 里面，通常情况下我们并不需要去关心里面的逻辑实现，只关心项目本身的代码。在这个时候，我们就需要使用 skipFiles：

```json
"skipFiles": [
  "${workspaceRoot}/node_modules/**/*.js",
  "<node_internals>/**/*.js"
]
```

#### 搭配 Nodemon

全局安装 nodemon 即可

```json
"runtimeExecutable": "nodemon", // 或者 node-dev
"restart": true,
"console": "integratedTerminal",
```

这里新增了三个字段，分别是：

+ runtimeExecutable：用什么命令执行 app.js，这里设置为 nodemon，默认是 node
+ restart：在终止 Node.js 后重启会话
+ console：启动调试目标的位置，这里选择在 vscode 的集成终端输出信息

#### 完整 Json

```json
{
  // 使用 IntelliSense 了解相关属性。
  // 悬停以查看现有属性的描述。
  // 欲了解更多信息，请访问: https://go.microsoft.com/fwlink/?linkid=830387
  "version": "0.2.0",
  "configurations": [{
    "type": "node",
    "request": "launch",
    "name": "启动程序",
    "program": "${workspaceFolder}/bin/www",
    "runtimeExecutable": "${workspaceRoot}/node_modules/nodemon/bin/nodemon", // 或者 node-dev
    "restart": true,
    "console": "integratedTerminal",
    "skipFiles": [
      "${workspaceRoot}/node_modules/**/*.js",
      "<node_internals>/**/*.js"
    ]
  }]
}
```

### 调试细节

Command + shift + L 可以快捷打印 console

在断点调试的时候，可以在调试控制台输出想要的内容

### 注意

![img](/img/user/programming/basic/common/programming-tools/1171464-20200605103422456-784330914.png)

## Jest

```json
{
	"type": "node",
	"request": "launch",
	"protocol": "inspector",
	"name": "Jest Debug",
	"program": "${workspaceRoot}/node_modules/jest/bin/jest",
	"stopOnEntry": false,
	"args": ["--runInBand", "--env=jsdom", "${fileBasename}"],
	"runtimeArgs": [
		"--inspect-brk"
	],
	"cwd": "${workspaceRoot}",
	"sourceMaps": true,
	"console": "integratedTerminal"
}
```

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Debug Jest Tests",
            "type": "node",
            "request": "launch",
            "runtimeArgs": [
                "--inspect-brk",
                "${workspaceRoot}/node_modules/.bin/jest",
                "--runInBand"
            ],
            "console": "integratedTerminal",
            "internalConsoleOptions": "neverOpen",
            "port": "9229"
        }
    ]
}
```

## Nest 类型

### 特点

启动由 nest 命令行控制，不存在类似于 express 那样的单文件入口

### Attach to Process

`npm run start:debug`

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Attach to node",
            "type": "node",
            "request": "attach",
            "restart": true,
			// 如果环境 PickProcess 命令执行失败, 可以查找到进程 id 直接填进去就好了
            "processId": "${command:PickProcess}"
        }
    ]
}
```

然后选择入口文件所在的进程即可

# Chrome 调试

![](/img/user/programming/basic/common/programming-tools/image-20231127211604150.png)

## 调试 Hover Focus 的节点

### 开启 Emulate a Focused Page

此时下拉就出来了。

![](/img/user/programming/basic/common/programming-tools/image-20231127211621206.png)

[【Chrome】控制台查看hover元素样式调试 - 掘金](https://juejin.cn/post/7164702020623368228)

## Source-panel F8

## Source-panel 监听 Keydown 事件断点

随便一个按钮就可以断住

![](/img/user/programming/basic/common/programming-tools/image-20240327145505620.png)

## Element Panel

![](/img/user/programming/basic/common/programming-tools/image-20240327145429631.png)

## 隐藏堆栈中的第三方框架

使用 [Ignore List](https://developer.chrome.com/docs/devtools/settings/ignore-list/#skip-third-party) 忽略一些第三方框架的堆栈信息，可以精简堆栈内容，方便一眼抓🐛。

在控制台设置 > Ignore List 中增加 Custom exclusion rules:

![](/img/user/programming/basic/common/programming-tools/image-20240311191355581.png)

## 覆盖混淆后的文件

大白话地说，就是在线上环境通过 「chrome devtool」-「source」- 「overrides」功能，实现修改源代码 ➕ 刷新后持续生效的调试效果

## 你不知道的 Devtool 技巧

![](programming-tools/你可能不知道的%20chrome%20devTools%20技巧.pdf)

# Safe Refactoring

ide 提供了丰富的 refactor 选项，不要再复制粘贴然后手动修改 class method error 了

使用 ide 提供的 refactor 选项即可

## Suggect Anction

`cmd/ctrl + .` show suggest action

## Refactor Action

`ctrl + shift + R` show suggest refactor action

![image-20220512165756816](/img/user/programming/basic/common/programming-tools/image-20220512165756816.png)

## Generate Code

code - generate

ctrl + enter

![image-20220512172108048](/img/user/programming/basic/common/programming-tools/image-20220512172108048.png)

# Charles

## 初始化配置

[Mac端Charles+手机端配置\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV1xV4y1M7Vb/?spm_id_from=333.337.search-card.all.click&vd_source=db8a4b4129af2e1d7a3e3f6357bb4d45)

注意关闭电脑的其他代理: proxy omega 关闭, 度管家也要退出

# Whistle

[Whistle 开发调试最佳实践 - 知乎](https://zhuanlan.zhihu.com/p/483975945)
