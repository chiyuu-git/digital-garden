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

![Key Map](vim/vim-basic.md#Key%20Map)
