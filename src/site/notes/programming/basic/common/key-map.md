---
{"dg-publish":true,"permalink":"/programming/basic/common/key-map/"}
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

# Vim

![vim-config](programming/basic/common/vim/vim-config.md#Key%20Map)

# Init New Computer

设置软件开机自动启动

快速在新电脑使用需要的环境

iterm2

zsh highlight config

+ download oh-my-zsh
+ [解决Mac下修改/etc/sudoers文件的问题\_mac sudoers\_CharlesLeeps的博客-CSDN博客](https://blog.csdn.net/sinat_36652514/article/details/91358520)
+ https://zhuanlan.zhihu.com/p/524453008

scroll-reverse

karabiner

+ 改变 mac 到 commad 为 ctrl

rescureTime

sizeUp

catchMouse

obsidian

微云

vscode

+ 添加 vscode 到右键快捷启动
+ [Mac下将Open With VSCode添加到"服务" - 简书](https://www.jianshu.com/p/97d802c9ce8b)
+ solved press and hold [visual studio code - How do I press and hold a key and have it repeat in VSCode? - Stack Overflow](https://stackoverflow.com/questions/39972335/how-do-i-press-and-hold-a-key-and-have-it-repeat-in-vscode)

node nvm nrm

git ssh. 公钥 配置

homebrew

vim config

obsidian config soft link
