---
{"aliases":["新电脑初始化"],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2022-11-25-Fri, 2:39:14 pm","date-modified":"2024-07-01-Mon, 11:03:45 am","permalink":"/programming/basic/common/key-map/","dgPassFrontmatter":true}
---


所有软件的 key map 快捷键总结文档, 总的来说是以 vim 语义为基础, 结合浏览器, vscode 的常用快捷键所形成的一套自定义快捷

借助键盘物理映射, 软件映射 (mac karabiner, win hotkey toy)

# Software

同时打开两个浏览器, 如果切换两个焦点? 在 mac 上可以使用 cmd + backquote

# Tab

```vim
" Emulate Tab Switching https://vimhelp.org/tabpage.txt.html#gt
" requires Cycle Through Panes Plugins https://obsidian.md/plugins?id=cycle-through-panes
exmap tabnext obcommand cycle-through-panes:cycle-through-panes
nmap ]b :tabnext
exmap tabprev obcommand cycle-through-panes:cycle-through-panes-reverse
nmap [b :tabprev
" Go back and forward with Ctrl+O and Ctrl+I
" (make sure to remove default Obsidian shortcuts for these to work)
exmap back obcommand app:go-back
nmap [l :back
exmap forward obcommand app:go-forward
nmap ]l :forward
```

# Split

![](/img/user/programming/basic/common/key-map/image-20221125153330284.png)

通过命令拆分窗口, split down, 而不是使用快捷键, 因为拆分窗口其实是一个低频操作.

拆分之后的焦点切换, 反而很高频

+ 在 vscode 中, 通过 `<c+1>` 可以随意切换焦点
+ 在 ob 中, 通过 `<s+arrow` 切换焦点, `<c+1>` 用于 heading
+ 在浏览器中, 不需要分屏操作, `<c+1>` 被用于快速跳转 tab

# Init New Computer

设置软件开机自动启动

快速在新电脑使用需要的环境

双拼设置, input source add

## 实现 Caps 单击为 esc，长按为 Ctrl

### Mac Karabiner

![](/img/user/programming/basic/common/key-map/image-20231023150912965.png)

不用设置 ctrl 和 command 反转了, 之前的做法是设置了反转然后又 map caps_lock to ctrl， 现在感觉这样做太绕了

绕一下的好处就是可以使用大拇指按着 command 实为 ctrl 控制屏幕的位置, 这样 mac 和 win 都是按着**系统键**控制. 感觉还是可以设置一下的.

![](/img/user/programming/basic/common/key-map/image-20231024105017215.png)

![|500](/img/user/programming/basic/common/key-map/image-20231024105051815.png)

### Win

[将CapsLock键在单击和长按时分别映射为Esc和Ctrl | OURONGXING (orxing.top)](https://orxing.top/post/d3c3145e.html#windows)

[Release v1.0.1 · oblitum/Interception · GitHub](https://github.com/oblitum/interception/releases/tag/v1.0.1)

## 方向键映射

win powertoy 然后搜索 键盘管理器即可设置

[重映射方向键 (Windows 篇) - 知乎](https://zhuanlan.zhihu.com/p/412082309)

## Else

scroll-reverse

obsidian

微云

rescureTime

sizeUp: 平替: [README.md](https://github.com/S1ngS1ng/HammerSpoon/blob/master/README.md)

magnet 破解版, 无广告

![](key-map/Magnet.zip)

![](/img/user/programming/basic/common/key-map/image-20231024111403130.png)

mac catchMouse / win double monitor tools

iterm2

homebrew

zsh highlight config

+ download oh-my-zsh[Oh My Zsh, 『 安装 & 配置 』 - 知乎](https://zhuanlan.zhihu.com/p/35283688)
+ [解决Mac下修改/etc/sudoers文件的问题\_mac sudoers\_CharlesLeeps的博客-CSDN博客](https://blog.csdn.net/sinat_36652514/article/details/91358520)
+ 安装 plugin: https://zhuanlan.zhihu.com/p/524453008

git ssh. 公钥 配置

vscode

+ 添加 vscode 到右键快捷启动
+ [Mac下将Open With VSCode添加到"服务" - 简书](https://www.jianshu.com/p/97d802c9ce8b)
+ solved press and hold [visual studio code - How do I press and hold a key and have it repeat in VSCode? - Stack Overflow](https://stackoverflow.com/questions/39972335/how-do-i-press-and-hold-a-key-and-have-it-repeat-in-vscode)

node nvm nrm [安装 nvm](programming/back-end/node.md#切换%20Node%20版本)

obsidian config soft link [programming-tools](programming-tools.md#软链接和硬链接)

# Vim

![vim-config](programming/basic/common/vim/vim-config.md#Key%20Map)

# Windows 激活

[win10（win11、win8.1)老提示需要激活？真正永久激活的方法在这儿 - 知乎](https://zhuanlan.zhihu.com/p/401418243?utm_id=0)

## 如何让 Parsec、Zerotier 等软件开启启动？

1. 按 Windows 徽标键  + R，键入“shell:startup”，然后选择“确定”。这将打开“启动”文件夹。
2. 将想要开启启动软件的 快捷方式 拖入到 启动文件夹即可。

# Adobe 激活

https://www.cybermania.ws/apps/adobe-genp/ 作者：我爱学习 23333 https://www.bilibili.com/read/cv27584335/?jump_opus=1 出处：bilibili

总之就是下载一个最新版的 Adobe GenP

https://www.cgzy.net/30541.html

# Chrome 实用插件

oneTap

fireShot 捕捉屏幕, 专治各种疑难杂症

vimium c

youtube 中英双语
