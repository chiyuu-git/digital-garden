---
{"dg-publish":true,"permalink":"/programming/basic/common/git/"}
---


https://github.com/geeeeeeeeek/git-recipes/wiki

https://www.cnblogs.com/qianqiannian/p/6010219.html

github 官方文档：https://docs.github.com/cn/github/getting-started-with-github/quickstart/set-up-git

# 访问 Github

## Github 代理

设置公司的 github 代理：http://wiki.baidu.com/pages/viewpage.action?pageId=1421617147 保持稳定访问

```
# export http_proxy=http://agent.baidu.com:8118
# export https_proxy=https://agent.baidu.com:8118
git config --global http.proxy http://agent.baidu.com:8118
git config --global https.proxy https://agent.baidu.com:8118
```

设置了代理可以访问 github，但是不能访问 gi tlab，以及 iterm2 也更新不了

取消代理

```
git config --global --unset http.proxy 
git config --global --unset https.proxy
```

http://pac.internal.baidu.com/bdnew.pac

## Github Host 解决 443 问题

```
fatal: 无法访问 'https://github.com/nvm-sh/nvm.git/'：Failed to connect to github.com port 443: Operation timed out
```

取消代理之后设置 host 可以解决 443 问题 https://juejin.cn/post/6844904193170341896

## LibreSSL SSL_connect: SSL_ERROR_SYSCALL

## 通过镜像资源解决 443 问题

https://segmentfault.com/a/1190000037474308

# Git 设置

## Git Config

```
git config --global user.name "username" //配置用户名

git config --global user.password "xx@gmail.com" //配置邮箱
```

检查用户名

```
git config --global user.name
```

设置本地的把 --global 参数去掉即可

### 设置邮箱

```
git config --global user.email gitaccount@example.com
```

### 设置 Commit 模板

```
git config --global commit.template ./ci_template
```

## 使用 Ssh

参考 icode 与 overflow 共存：https://stackoverflow.com/questions/3225862/multiple-github-accounts-ssh-config

参考 github 官方文档：https://docs.github.com/cn/github/authenticating-to-github/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent

配置文件地址：

```
~/.ssh/config
```

## .gitignore

在项目开发过程中个，一般都会添加 .gitignore 文件，规则很简单，但有时会发现，规则不生效。

原因是 .gitignore 只能忽略那些原来没有被 track 的文件，如果某些文件已经被纳入了版本管理中，则修改.gitignore 是无效的。

那么解决方法就是先把本地缓存删除（改变成未 track 状态），然后再提交。

```
git rm -r --cached .

git add .

git commit -m 'update .gitignore'
```

# Github 功能

## Fork

在他人的项目点击 fork，即可 fork 到自己的仓库，

## Pull Request

可以将自己的 fork 仓库的的变更合并到源仓库

![1570763777395](/img/user/programming/basic/common/git/1570763777395.png)

# 基本使用

## 工作流

你的本地仓库由 git 维护的三棵“树”组成。第一个是你的 **工作目录**，它持有实际文件；第二个是 **缓存区（Index）**，它像个缓存区域，临时保存你的改动；最后是 `HEAD`，指向你最近一次提交后的结果。

> 事实上，第三个阶段是 commit history 的图。HEAD 一般是指向最新一次 commit 的引用。现在暂时不必究其细节。

# 通过克隆初始化

## Git Clone

git clone url //将远程仓库克隆下载到本地：

+ `git clone username@host:/path/to/repository` （通过 SSH）
+ `git clone https:/path/to/repository.git` （通过 https）

通过克隆初始化自动连接了远程仓库，因此直接做修改和提交即可

# 本地项目初始化

## 生成本地仓库

git init 初始化生成一个本地仓库

git add *

git commit –m “init gshop” 提交到本地仓库

## 关联远程仓库

关联代码：git remote add origin https://github.com/chiyougithub/imitate_eleme.git

> 这里 origin 是 < server > 的别名，取什么名字都可以，你也可以在 push 时将 < server > 替换为 origin。

先关联再推送

将本地仓库推送到远程仓库：git push origin master

> 可以把 master 换成你想要推送的任何分支。

## 删除本地库

直接删除.git 文件即可

## 解除远程仓库

删除与远程仓库的关联就比较简单了，直接输入以下命令：

git remote rm origin

git remote rm GitHub

## 所有的 Git 命令都可以添加 -h 参数查看具体的功能

git branch -h

git rebase -h

# 创建代码仓库

## Git Init

`git init` 命令创建一个新的 Git 仓库。

运行 `git init` 命令会在你项目的根目录下创建一个新的 `.git` 目录，其中包含了你项目必需的所有元数据。除了 `.git` 目录之外，已经存在的项目不会被改变

### 用法

```
git init
```

```
git init <directory>
```

在指定目录创建一个空的 Git 仓库。运行这个命令会创建一个名为 `directory`，只包含 `.git` 子目录的空目录。

```
git init --bare <directory>
```

初始化一个裸的 Git 仓库，但是忽略工作目录。共享的仓库应该总是用 `--bare` 标记创建（见下面的讨论）。一般来说，用 `--bare` 标记初始化的仓库以 `.git` 结尾。比如，一个叫 `my-project` 的仓库，它的空版本应该保存在 `my-project.git` 目录下。

### 裸仓库

`-—bare` 标记创建了一个没有工作目录的仓库。中央仓库应该总是创建成裸仓库，因为向非裸仓库推送分支有可能会覆盖已有的代码变动。将 `-—bare` 看成是用来将仓库标记为储存设施，而不是一个开发环境。也就是说，对于所有的 Git 工作流，中央仓库是裸仓库，开发者的本地仓库是非裸仓库。

## Git Clone

`git clone` 命令拷贝整个 Git 仓库。是一个完备的 Git 仓库——它包含自己的历史，管理自己的文件，以及环境和原仓库完全隔离。

为了方便起见，`clone` 自动创建了一个名为 `origin` 的远程连接，指向原有仓库。这让和中央仓库之间的交互更加简单。

### 用法

```
git clone <repo>
```

将位于 `<repo>` 的仓库克隆到本地机器。原仓库可以在本地文件系统中，或是通过 HTTP 或 SSH 连接的远程机器。

```
git clone <repo> <directory>
```

将位于 `<repo>` 的仓库克隆到本地机器上的 `<directory>` 目录。

## Git Config

`git config` 命令允许你在命令行中配置你的 Git 安装（或是一个独立仓库）。这个命令定义了所有配置，从用户信息到仓库行为等等。一些常见的配置命令如下所列。

### 用法

```
git config user.name <name>
```

定义当前仓库所有提交使用的作者姓名。通常来说，你希望使用 `--global` 标记设置当前用户的配置项。

```
git config --global user.name <name>
```

定义当前用户所有提交使用的作者姓名。

```
git config --global user.email <email>
```

定义当前用户所有提交使用的作者邮箱。

```
git config --global alias.<alias-name> <git-command>
```

为 Git 命令创建一个快捷方式（别名）。

```
git config --system core.editor <editor>
```

定义当前机器所有用户使用命令时用到的文本编辑器，如 `git commit`。`<editor>` 参数用编辑器的启动命令（如 vi）替代。

### 手动编辑

```
git config --global --edit
```

用文本编辑器打开全局配置文件，手动编辑。

所有配置项都储存在纯文本文件中，所以 `git config` 命令其实只是一个提供便捷的命令行接口。通常，你只需要在新机器上配置一次 Git 安装，以及，你通常会想要使用 `--global` 标记。

Git 将配置项保存在三个单独的文件中，允许你分别对单个仓库、用户和整个系统设置。

- /.git/config – 特定仓库的设置。
- ~/.gitconfig – 特定用户的设置。这也是 `--global` 标记的设置项存放的位置。
- $(prefix)/etc/gitconfig – 系统层面的设置。

当这些文件中的配置项冲突时，本地仓库设置覆盖用户设置，用户设置覆盖系统设置。如果你打开期中一份文件，你会看到下面这些：

```
[user]

name = John Smith

email = john@example.com

[alias]

st = status

co = checkout

br = branch

up = rebase

ci = commit

[core]

editor = vim
```

你可以用 `git config` 手动编辑这些值。

# 保存代码更改

## Git Add

`git add` 命令将工作目录中的变化添加到缓存区。它告诉 Git 你希望下一个提交中包含这个文件的更新。

不过，`git add` 不会实际上并不会改变你的仓库。直到你运行 `git commit` ，才会真正的更改文件

## Git Commit

 git commit 主要是将暂存区里的改动给提交到本地的版本库。

每次使用 git commit 命令我们都会在本地版本库生成一个 40 位的哈希值，这个哈希值也叫 commit-id，

commit-id 在版本回退的时候是非常有用的，它相当于一个快照,可以在未来的任何时候通过与 `git reset` 的组合命令回到这里.

现在，你的改动已经提交到了 HEAD，本地仓库，但是还没到你的远端仓库。

> 在开发时，良好的习惯是根据工作进度及时 commit，并务必注意附上有意义的 commit message。创建完项目目录后，第一次提交的 commit message 一般为「Initial commit.」。

### -m

用于添加 commit 信息

```js
git commit -m “message”
```

### -a

```
git commit -a -m “message”
```

加的 -a 参数可以将所有已跟踪文件中的执行修改或删除操作的文件都提交到本地仓库，即使它们没有经过 git add 添加到暂存区

建议一般不要使用 -a 参数，正常的提交还是使用 git add 先将要改动的文件添加到暂存区，再用 git commit 提交到本地版本库

### Commit Message

终止提交：只要 message 是空的就不会提交，在信息前面哦加上一个#即可

```bash
npm install -g commitizen cz-conventional-changelog
```

主要, 全局模式下, 需要 ~/.czrc 配置文件, 为 commitizen 指定 Adapter.

可以通过 git config template 配置，参考 swan 库的 commit-msg-setting.sh

```
git config commit.template ci_template
# 导致非swan库查找不到commit.template，取消用户全局级别的commit.template
git config --global --unset commit.template
git config --global core.editor vim

if [ -f .git/hooks/prepare-commit-msg ];then
rm .git/hooks/prepare-commit-msg
fi

# commit 阶段不需要检查 commit 格式有效性，只要保证 push 时的 commit 有效即可
# 加上这个会导致 --amend 每次都创建新的
# if [ -f .git/hooks/commit-msg ];then
# rm .git/hooks/commit-msg
# fi
```

在非 insert 模式下，先按数字，再按 dd，即可删除光标后的数字行

## 工作原理

`git add` 和 `git commit` 这两个命令组成了最基本的 Git 工作流。它们的作用是将项目的诸多版本记录到仓库历史中。不管使用哪种团队协作模型，每个 Git 用户都必须理解这两个命令。

一个项目的编写离不开这个基本模式：编辑、缓存和提交。首先，你在工作目录下编辑你的文件。当你想要备份当前的项目状态时，你使用 `git add` 缓存更改。当你觉得这个被缓存的副本已经就绪，你使用 `git commit` 将它提交到项目历史。`git reset` 命令用于撤销提交或被缓存的快照。

除了 `git add` 和 `git commit` 之外，`git push` 也是完整的 Git 协作流程中的重要一环。`git push` 用于将提交的更改发送到远端仓库。随后，其它团队成员也可以看到这些更改。

### 缓存区

`git add` 命令最主要的作用是将工作目录中的更改添加到 Git 的缓存区

注意：新加的文件（即没有被 git 系统管理的文件）是不能被提交到本地仓库的。

缓存允许你把紧密相关的几份更改合并成一份快照，而不是直接提交所有新的更改。也就是说你可以同时进行多个无关的更改，最后分成几次将相关更改添加到缓存区并提交。对于任何版本控制系统来说，保持提交的原子性非常重要，这有利于追踪 bug 以及用最小的代价撤销更改。

## Git Diff

用于比较两次修改的差异

### 用法

**比较工作区与暂存区**

```
git diff 
```

不加参数即默认比较工作区与暂存区

在 vscode 中直接点击缓存区的文件即是这个效果

**比较暂存区与最新本地版本库（本地库中最近一次 commit 的内容）**

```
git diff --cached  [<path>...] 
```

**比较工作区与最新本地版本库**

```
git diff HEAD [<path>...]  
```

如果 HEAD 指向的是 master 分支，那么 HEAD 还可以换成 master

**比较工作区与指定 commit-id 的差异**

```
git diff commit-id [<path>...] 
```

**比较暂存区与指定 commit-id 的差异**

```
git diff --cached [<commit-id>] [<path>...] 
```

**比较两个 commit-id 之间的差异**

```
git diff [<commit-id>] [<commit-id>]
```

### **git Diff 打补丁**

```
git diff > patch 
```

patch 的命名是随意的，不加其他参数时作用是：当我们希望将我们本仓库工作区的修改拷贝一份到其他机器上使用，但是修改的文件比较多，拷贝量比较大，

此时我们可以将修改的代码做成补丁，之后在其他机器上 **对应目录下** 使用 git apply patch 将补丁打上即可

```
git diff --cached > patch //是将我们暂存区与版本库的差异做成补丁

git diff --HEAD > patch //是将工作区与版本库的差异做成补丁

git diff Testfile > patch//将单个文件做成一个单独的补丁
```

拓展：git apply patch 应用补丁，应用补丁之前我们可以先检验一下补丁能否应用，git apply --check patch 如果没有任何输出，那么表示可以顺利接受这个补丁

另外可以使用 git apply --reject patch 将能打的补丁先打上，有冲突的会生成.rej 文件，此时可以找到这些文件进行手动打补丁　

## Git History Diff

点击右上角的 git 图标，即可查看，还可以进行 revert reset 等操作

先 select，再 compare，显示在 compare commits

## Git Stash

git stash 这个命令可以将当前的工作状态保存到 git 栈，在需要的时候再恢复

**对应 vscode 中的储藏功能**

因为功能没有 commit 之前切换分支是没有意义的，stash 就可以把当前的修改储藏起来，方便去修改其他分支。

stash 功能是独立于分支的，在哪个分支恢复，就会把代码改动应用到那个分支

### 用法

**git stash**

保存当前的工作区与暂存区的状态，把当前的工作隐藏起来，等以后需要的时候再恢复，git stash 这个命令可以多次使用，每次使用都会新加一个 stash@{num}，num 是编号

**git stash pop**

默认恢复 git 栈中最新的一个 stash@{num}，建议在 git 栈中只有一条的时候使用，以免混乱

**git stash list**

查看所有被隐藏的文件列表

**git stash apply**

恢复被隐藏的文件，但是 git 栈中的这个不删除，用法：git stash apply stash@{0}，如果我们在 git stash apply 的时候工作目录下的文件一部分已经加入了暂存区，部分文件没有，

当我们执行 git stash apply 之后发现所有的文件都变成了未暂存的，如果想维持原来的样子，即暂存过的依旧是暂存状态，那么可以使用 git stash apply --index

**git stash drop**

删除指定的一个进度，默认删除最新的进度，使用方法如 git stash drop stash@{0}

**git stash clear**

删除所有存储的进度

**git stash show**

显示 stash 的内容具体是什么，使用方法如 git stash show stash@{0}

**查看帮助**

git stash --help

## Git Cheryy Pick

`git cherry-pick` 命令的作用，就是将指定的提交（commit）应用于其他分支。

```js
git cherry-pick <commitHash>
```

上面命令就会将指定的提交 `commitHash`，应用于当前分支。这会在当前分支产生一个新的提交，当然它们的哈希值会不一样。

### 转移多个提交

```
git cherry-pick <HashA> <HashB>
```

上面的命令将 A 和 B 两个提交应用到当前分支。这会在当前分支生成两个对应的新提交。

如果想要转移一系列的连续提交，可以使用下面的简便语法。

```
git cherry-pick A..B 
```

上面的命令可以转移从 A 到 B 的所有提交。它们必须按照正确的顺序放置：提交 A 必须早于提交 B，否则命令将失败，但不会报错。

注意，使用上面的命令，提交 A 将不会包含在 Cherry pick 中。如果要包含提交 A，可以使用下面的语法。

```
git cherry-pick A^..B 
```

ae2b5b4e56b528687a4f7cbc9f6328a65237b072

95519ceaf89d410b4cdfecb7ac1fd9a413f403b8

e2ecebdcdbc04e66fa55ef377acde5470ab90138

08cae718bc152f62a104785c649153f6c6eba510

2b3005fd4da08b285d332c994ddb5f6dc9fed2c7

### 配置项

`git cherry-pick` 命令的常用配置项如下。

**（1）`-e`，`--edit`**

打开外部编辑器，编辑提交信息。

**（2）`-n`，`--no-commit`**

只更新工作区和暂存区，不产生新的提交。

**（3）`-x`**

在提交信息的末尾追加一行 `(cherry picked from commit ...)`，方便以后查到这个提交是如何产生的。

**（4）`-s`，`--signoff`**

在提交信息的末尾追加一行操作者的签名，表示是谁进行了这个操作。

**（5）`-m parent-number`，`--mainline parent-number`**

如果原始提交是一个合并节点，来自于两个分支的合并，那么 Cherry pick 默认将失败，因为它不知道应该采用哪个分支的代码变动。

`-m` 配置项告诉 Git，应该采用哪个分支的变动。它的参数 `parent-number` 是一个从 `1` 开始的整数，代表原始提交的父分支编号。

```
$ git cherry-pick -m 1 <commitHash>
```

上面命令表示，Cherry pick 采用提交 `commitHash` 来自编号 1 的父分支的变动。

一般来说，1 号父分支是接受变动的分支（the branch being merged into），2 号父分支是作为变动来源的分支（the branch being merged from）。

### 冲突解决

如果操作过程中发生代码冲突，Cherry pick 会停下来，让用户决定如何继续操作。

**（1）`--continue`**

用户解决代码冲突后，第一步将修改的文件重新加入暂存区（`git add .`），第二步使用下面的命令，让 Cherry pick 过程继续执行。

```
$ git cherry-pick --continue
```

**（2）`--abort`**

发生代码冲突后，放弃合并，回到操作前的样子。

**（3）`--quit`**

发生代码冲突后，退出 Cherry pick，但是不回到操作前的样子。

### 转移到另一个代码库

http://www.ruanyifeng.com/blog/2020/04/git-cherry-pick.html

## .gitignore

在 git 操作的根目录 (即最高一级可以使用 git 命令的仓库) 建立一个名为.gitignore 的隐藏文件

将希望忽略的文件/文件夹加入到.gitignore 文件中，使用 vi/vim 都可以，但是开头记得不能有空格

# 检查仓库状态

## Git Status

`git status` 命令显示工作目录和缓存区的状态。你可以看到哪些更改被缓存了，哪些还没有，以及哪些还未被 Git 追踪。status 的输出 *不会* 告诉你任何已提交到项目历史的信息。如果你想看的话，应该使用 `git log` 命令。

```
git status
```

列出已缓存、未缓存、未追踪的文件。

## Git Log

`git log` 命令显示已提交的快照。你可以列出项目历史，筛选，以及搜索特定更改。`git status` 允许你查看工作目录和缓存区，而 `git log` 只作用于提交的项目历史。

log 输出可以有很多种自定义的方式，从简单地筛选提交，到用完全自定义的格式显示。其中一些最常用的 `git log` 配置如下所示。

### 用法

```
git log
```

使用默认格式显示完整地项目历史。如果输出超过一屏，你可以用 **空格键** 来滚动，按 `q` 退出。

```
git log -n <limit>
```

用 `<limit>` 限制提交的数量。比如 `git log -n 3` 只会显示 3 个提交。

```
git log --oneline
```

将每个提交压缩到一行。当你需要查看项目历史的上层情况时这会很有用。

```
git log --stat
```

除了 `git log` 信息之外，包含哪些文件被更改了，以及每个文件相对的增删行数。

```
git log -p
```

显示代表每个提交的一堆信息。显示每个提交全部的差异（diff），这也是项目历史中最详细的视图。

```
git log --author="<pattern>"
```

搜索特定作者的提交。`<pattern>` 可以是字符串或正则表达式。

```
git log --grep="<pattern>"
```

搜索提交信息匹配特定 `<pattern>` 的提交。`<pattern>` 可以是字符串或正则表达式。

```
git log <since>..<until>
```

只显示发生在 `<since>` 和 `<until>` 之间的提交。两个参数可以是提交 ID、分支名、`HEAD` 或是任何一种引用。

```
git log <file>
```

只显示包含特定文件的提交。查找特定文件的历史这样做会很方便。

```
git log --graph --decorate --oneline
```

还有一些有用的选项。`--graph` 标记会绘制一幅字符组成的图形，左边是提交，右边是提交信息。`--decorate` 标记会加上提交所在的分支名称和标签。`--oneline` 标记将提交信息显示在同一行，一目了然。

你可以将很多选项用在同一个命令中：

```
git log --author="John Smith" -p hello.py
```

这个命令会显示 `John Smith` 作者对 `hello.py` 文件所做的所有更改的差异比较（diff）。

### 示例

`git log` 命令是 Git 查看项目历史的基本工具。当你要寻找项目特定的一个版本或者弄明白合并功能分支时引入了哪些变化，你就会用到这个命令。

```
commit 3157ee3718e180a9476bf2e5cab8e3f1e78a73b7
Author: John Smith
```

大多数时候都很简单直接。但是，第一行需要解释下。`commit` 后面 40 个字的字符串是提交内容的 SHA-1 校验总和（checksum）。它有两个作用。一是保证提交的正确性——如果它被损坏了，提交会生成一个不同的校验总和。第二，它是提交唯一的标识 ID。

这个 ID 可以用于 `git log` 这样的命令中来引用具体的提交。比如，`git log 3157e..5ab91` 会显示所有 ID 在 `3157e` 和 `5ab91` 之间的提交。除了校验总和之外，分支名、HEAD 关键字也是常用的引用提交的方法。`HEAD` 总是指向当前的提交，无论是分支还是特定提交也好。

字符用于表示提交的父节点的相对引用。比如，`3157e~1` 指向 `3157e` 前一个提交,`HEAD~3` 是当前提交的回溯 3 个节点的提交。

所有这些标识方法的背后都是为了让你对特定提交进行操作。`git log` 命令一般是这些交互的起点，因为它让你找到你想要的提交。

### ..句法

..句法是比较分支很有用的工具。下面的栗子显示了在 `some-feature` 分支而不在 `master` 分支的所有提交的概览。

```
git log --oneline master..some-feature
```

## Git Checkout

`git checkout` 这个命令有三个不同的作用：检出文件、检出提交和检出分支。在这一章中，我们只关心前两种用法。

检出提交会使工作目录和这个提交完全匹配。你可以用它来查看项目之前的状态，而不改变当前的状态。检出文件使你能够查看某个特定文件的旧版本，而工作目录中剩下的文件不变。

**注意：**清空缓存区之后才可以 checkout

### 用法

```
git checkout master
```

回到 master 分支。分支会在下一节中讲到，而现在，你只需要将它视为回到项目「当前」状态的一种方式。

```
git checkout <commit> <file>
```

查看文件之前的版本。它将工作目录中的 `<file>` 文件变成 `<commit>` 中那个文件的拷贝，并将它加入缓存区。

```
git checkout <commit>
```

更新工作目录中的所有文件，使得和某个特定提交中的文件一致。你可以将提交的哈希字串，或是标签作为 `<commit>` 参数。这会使你处在分离 HEAD 的状态。

### 检出提交

检出之前的提交是一个只读操作。在查看旧版本的时候绝不会损坏你的仓库。你项目「当前」的状态在 `master` 上不会变化。在开发的正常阶段，`HEAD` 一般指向 master 或是其他的本地分支，但当你检出之前提交的时候，`HEAD` 就不再指向一个分支了——它直接指向一个提交。这被称为「分离 `HEAD`」状态 。

### 检出文件

在另一方面，检出旧文件不影响你仓库的当前状态。你可以在新的快照中像其他文件一样重新提交旧版本。所以，在效果上，`git checkout` 的这个用法可以用来将单个文件回滚到旧版本 。

```
git checkout a1e8fb5 hello.py
```

**注意：**和检出提交不同，这里 *确实* 会影响你项目的当前状态。旧的文件版本会显示为「需要提交的更改」，允许你回滚到文件之前的版本。如果你不想保留旧的版本，你可以用下面的命令检出到最近的版本：

```
git checkout HEAD hello.py
```

## Git Show

show 的形式是 patch 的形式

# 回滚错误修改

## Git Checkout

用于辅助

## Git Revert

到底了做啥，根本看不懂，被 revert 的 commit，消失了，所做的修改不知道去哪里了

不知道指南那里在干什么，最后远程还是产生了多个 commit，一个 revert 的 commit，一个重新提交的新的 commit

`git revert` 命令用来撤销一个已经提交的快照。但是，它是通过搞清楚如何撤销这个提交引入的更改，然后在最后加上一个撤销了更改的 **新提交**，而不是从项目历史中移除这个提交。这避免了 Git 丢失项目历史，这一点对于你的版本历史和协作的可靠性来说是很重要的。

```
git revert <commit>
```

生成一个撤消了 `<commit>` 引入的修改的新提交，然后应用到当前分支。

![image-20200806090157997](/img/user/programming/basic/common/git/image-20200806090157997.png)

### 逆操作

可以通过恢复到之前 commit，重新引入被 revert 的提交

## Git Reset

如果说 `git revert` 是一个撤销更改安全的方式，你可以将 `git reset` 看做一个 *危险* 的方式。当你用 `git reset` 来重设更改时我们无法获得原来的样子——这个撤销是永远的。因为提交不再被任何日志所引用，这个 commit 从 history 上消失了

它应该只被用于 本地 修改——你永远不应该重设和其他开发者共享的快照。

### 用法

```
git reset <file>
```

从缓存区移除特定文件，但不改变工作目录。它会取消这个文件的缓存，而不覆盖任何更改。

**相当于是 vscode 的减号**

```
git reset
```

重设缓冲区，匹配最近的一次提交，但工作目录不变。它会取消 *所有* 文件的缓存，而不会覆盖任何修改，给你了一个重设缓存快照的机会。

**仅仅是把缓存区的全部减号了而已**

```
git reset --hard
```

重设缓冲区和工作目录，匹配最近的一次提交。除了取消缓存之外，`--hard` 标记告诉 Git 还要重写所有工作目录中的更改。换句话说：它清除了所有未提交的更改，所以在使用前确定你想扔掉你所有本地的开发。

**相当于是修改都撤销了，vscode 常用操作**

```
git reset <commit>
```

将当前分支的末端移到 `<commit>`，将缓存区重设到这个提交，但不改变工作目录。所有 `<commit>` 之后的更改会保留在工作目录中，这允许你用更干净、原子性的快照重新提交项目历史。

```
git reset --hard <commit>
```

将当前分支的末端移到 `<commit>`，将缓存区和工作目录都重设到这个提交。它不仅清除了未提交的更改，同时还清除了 `<commit>` 之后的所有提交。

## Revert 和 Reset 对比

撤销 (revert) 被设计为 **撤销公开的提交** 的安全方式，`git reset` 被设计为 **重设本地更改**。

`git revert` 回滚了「单独一个提交」，它没有移除后面的提交，然后回到项目之前的状态。在 Git 中，后者实际上被称为 `reset`，而不是 `revert`。

当有 `<commit>` 之后的提交被推送到公共仓库后，你绝不应该使用 `git reset`。发布一个提交之后，你必须假设其他开发者会依赖于它。

撤销和重设相比有两个重要的优点。首先，它不会改变项目历史，对那些已经发布到共享仓库的提交来说这是一个安全的操作。

其次，`git revert` 可以针对历史中任何一个提交，而 `git reset` 只能从当前提交向前回溯。比如，你想用 `git reset` 重设一个旧的提交，你不得不移除那个提交后的所有提交，再移除那个提交，然后重新提交后面的所有提交。不用说，这并不是一个优雅的回滚方案。

## 使用 [git reflog](git.md#git%20reflog) 修复 Amend 历史

amend 的提交无法通过 reset 修复，可以通过 git reflog 处理

## Git Clean

`git clean` 命令将未跟踪的文件从你的工作目录中移除。它只是提供了一条捷径，因为用 `git status` 查看哪些文件还未跟踪然后手动移除它们也很方便。和一般的 `rm` 命令一样，`git clean` 是无法撤消的，所以在删除未跟踪的文件之前想清楚，你是否真的要这么做。

`git clean` 命令经常和 `git reset --hard` 一起使用。记住，reset 只影响被跟踪的文件，所以还需要一个单独的命令来清理未被跟踪的文件。这个两个命令相结合，你就可以将工作目录回到之前特定提交时的状态。

### 用法

```
git clean -n
```

执行一次 git clean 的『演习』。它会告诉你那些文件在命令执行后会被移除，而不是真的删除它。

```
git clean -f
```

移除当前目录下未被跟踪的文件。`-f`（强制）标记是必需的，除非 `clean.requireForce` 配置项被设为了 `false`（默认为 `true`）。它 *不会* 删除 `.gitignore` 中指定的未跟踪的文件。

```
git clean -f <path>
```

移除未跟踪的文件，但限制在某个路径下。

```
git clean -df
```

移除未跟踪的文件，以及目录。

```
git clean -xf
```

移除当前目录下未跟踪的文件，以及 Git 一般忽略的文件。

### 讨论

如果你在本地仓库中作死之后想要毁尸灭迹，`git reset --hard` 和 `git clean -f` 是你最好的选择。运行这两个命令使工作目录和最近的提交相匹配，让你在干净的状态下继续工作。

`git clean` 命令对于 build 后清理工作目录十分有用。比如，它可以轻易地删除 C 编译器生成的 `.o` 和 `.exe` 二进制文件。这通常是打包发布前需要的一步。`-x` 命令在这种情况下特别方便。

请牢记，和 `git reset` 一样， `git clean` 是仅有的几个可以永久删除提交的命令之一，所以要小心使用。事实上，它太容易丢掉重要的修改了，以至于 Git 厂商 *强制* 你用 `-f` 标志来进行最基本的操作。这可以避免你用一个 `git clean` 就不小心删除了所有东西。

### 栗子

下面的栗子清除了工作目录中的所有更改，包括新建还没加入缓存的文件。它假设你已经提交了一些快照，准备开始一些新的实验。

```
# 编辑了一些文件
# 新增了一些文件
# 『糟糕』

# 将跟踪的文件回滚回去
git reset --hard

# 移除未跟踪的文件
git clean -df
```

在执行了 reset/clean 的流程之后，工作目录和缓存区和最近一次提交看上去一模一样，而 `git status` 会认为这是一个干净的工作目录。你可以重新来过了。

注意，不像 `git reset` 的第二个栗子，新的文件没有被加入到仓库中。因此，它们不会受到 `git reset --hard` 的影响，需要 `git clean` 来删除它们。

# 使用分支

分支：当前工作目录的代目的一个副本

使用分支，可以让我们从开发主线上分离，避免新的不稳定代码对主线的影响

## 注意

在 develop 分支做出的修改，如果没有 commit，切换到 master 分支一样可以查看到这些修改

提交 commit 之后，再切换到 master 分支就看不到了

# 分支类型

## 主分支

当我们第一次向 git 提交更新记录时，会自动产生一个分支，这个就是主分支 master

## 开发分支

等功能开发完成，通过测试之后，再合并到主分支

![image-20200629114713747](/img/user/programming/basic/common/git/image-20200629114713747.png)

## 功能分支

基于开发分支的创建，用于开发具体的功能

## 修复分支

## 版本分支

# 分支命令

```
git branch
```

查看分支

```
git branch <branchname>
```

基于所处的分支，创建新的分支

```
git checkout <branchname>
```

切换分支

```
git merch <branchname>
```

合并分支

```
git branch -d <branchname>
git branch -D <branchname>
```

删除分支，分支被合并后才允许删除

-D，强制删除分支

# 合并分支

先切换到主分支

```
git merger develop
```

合并分支会产生 commit id 吗？确实，产生了新的 commit id

合并远程分支：先 pull 到本地，再 merge

## Git Merge --no-ff

默认是 ff 模式，允许 fast forward

--no-ff 意思就是不允许 fast forward，会强制产生一个 merge commit

# 整理项目历史

## Git Commit --amend

```
git commit --amend
```

> amend 修改

`git commit --amend` 命令是修复最新提交的便捷方式。它允许你将缓存的修改和之前的提交合并到一起，而不是提交一个全新的快照。它还可以用来简单地编辑上一次提交的信息而不改变快照。

仓促的提交在你日常开发过程中时常会发生。很容易就忘记了缓存一个文件或者弄错了提交信息的格式。`--amend` 标记是修复这些小意外的便捷方式。

但是，amend 不只是修改了最新的提交——它进行了一次替换。对于 Git 来说，这看上去像一个全新的提交，产生了新的 commit id

在公共仓库工作时一定要牢记这一点。

### 不要修复公共提交

在 [`git reset`](https://github.com/geeeeeeeeek/git-recipes/wiki/2.6-回滚错误的修改#git-reset) 这节中，我们说过永远不要重设和其他开发者共享的提交。对于修复也是一样：永远不要修复一个已经推送到公共仓库中的提交。

修复过的提交事实上是全新的提交，之前的提交会被移除出项目历史。这和重设公共快照的后果是一样的。如果你修复了其他开发者在之后继续开发的一个提交，看上去他们的工作基础从项目历史中消失了一样。对于在这上面的开发者来说这是很困惑的，而且很难恢复。

## Git Merge

### 情况一

子分支基于主分支新建，然后主分支没有提交了，仅有子分支在开发

此时 merge 可以保留子分支的所有 commit 历史，并且很自然的也是线性的，这种情况就是 **快速向前合并**

![image-20200806102615800](/img/user/programming/basic/common/git/image-20200806102615800.png)

### 情况二

子分支基于主分支新建，然后主分支也有提交，子分支也有提交

![image-20200806103301851](/img/user/programming/basic/common/git/image-20200806103301851.png)

不论合并是否发生冲突，都会产生一个新的 merge commit，两个分支的 commit 会按照时间先后排序

如果都有提交，就算没有冲突，merge 的时候也会产生多路合并，以及一个合并的 commit

> 其实快速向前合并其实也是不推荐的，开发分支上多余的 commit 历史对于主干来说是没有必要的，丢了就丢了

**缺点**

+ 子分支上会有自己的 commit 记录，而主分支上生成的 merge message，是没有意义的
+ 开发分支上多次的 commit，对于整个工程来说也是没有意义的，只需要一个能概括所有工作的 commit 即可
+ 不应该修改 merge 的那条历史，应为不光是你的修改

    ![image-20200806100108986](/img/user/programming/basic/common/git/image-20200806100108986.png)

## Git Rebase

### 用法

变基：将当前分支的 commit 移动到选定的基 `<base>` 上提交

```js
git rebase <base>
```

> base 可以是任何类型的提交引用：ID、分支名、标签，或是 `HEAD` 的相对引用

**内部机制：Git 是通过在选定的基上 `<base>` 创建新提交来完成这件事的——它事实上重写了当前分支的项目历史**，理解这一点很重要

rebase 分支，rebase 前

![image-20200806161116580](/img/user/programming/basic/common/git/image-20200806161116580.png)

master 分支，rebase 前

![image-20200806161006271](/img/user/programming/basic/common/git/image-20200806161006271.png)

rebase 分支，rebase 后，commit 时间虽然没变，但是 commitid 发生了变化，证明这是一个新的提交

![image-20200806161223218](/img/user/programming/basic/common/git/image-20200806161223218.png)

### 针对情况二使用 Rebase

rebase 的主要目的是为了保持一个线性的项目历史。比如说，当你在 feature 分支工作时 master 分支取得了一些进展：

要将你的 feature 分支整合进 `master` 分支，你有两个选择：

1. 直接 merge，这会产生一个三路合并（3-way merge）和一个合并提交
2. 先 rebase 后 merge，这是一个快速向前的合并以及完美的线性历史

现在主分支已经有很多的修改了，领先了 faster-reabse，在子分支 reabse 主分支，产生了冲突

解决冲突之后，使用 git add 标记 / vscode 加号，之后还要执行 rebase --continue

> 无冲突的变基，是不需要 --continue 的

![image-20200806105100237](/img/user/programming/basic/common/git/image-20200806105100237.png)

然后 master 在 merge 我们的 faster-rebase，就可以产生一个清晰的 commit history

![image-20200806110047136](/img/user/programming/basic/common/git/image-20200806110047136.png)

> 如果用红姐的方法，1fix 那个 commit 也会变成是我们的 commit，问题挺大的，还是应该用 rebase 待测试
>
> ok 的，其实 reset 的只有 pull origin/devlops 的操作，reset 到 master 最新的即可，此图只需要 reset 到 1fix 即可
>
> 只需要 reset 掉 merge 的操作即可，其实还挺方便的

### 总结

**rebase 是将上游更改合并进本地仓库的通常方法。你每次想查看上游进展时，用 `git merge` 拉取上游更新会导致一个多余的合并提交。作为对比，rebase 就好像是说「我想将我的更改建立在其他人的进展之上」。**

### 不要 Rebase 公共历史

和我们讨论过的 `git commit --amend` 和 `git reset` 一样，你永远不应该 rebase 那些已经推送到公共仓库的提交。rebase 会用新的提交替换旧的提交，你的项目历史会像突然消失了一样。

重写 commit history 是危险的操作

## Git Rebase -i

用 `-i` 标记运行 `git rebase` 开始交互式 rebase。交互式 rebase 给你在过程中修改单个提交的机会，而不是盲目地将所有提交都移到新的基上。你可以移除、分割提交，更改提交的顺序。它就像是打了鸡血的 `git commit --amend` 一样。

### 用法

```
git rebase -i <base>
```

将当前分支 rebase 到 `base`，但使用可交互的形式。它会打开一个编辑器，你可以为每个将要 rebase 的提交输入命令（见后文）。这些命令决定了每个提交将会怎样被转移到新的基上去。你还可以对这些提交进行排序。

```
git rebase -i HEAD~6
```

重写包括 head 在内的 6 个提交，即是最近的 6 个提交

### 描述

交互式 rebase 给你了控制项目历史的完全掌控。它给了开发人员很大的自由，因为他们可以提交一个「混乱」的历史而只需专注于写代码，然后在此时恢复干净。

大多数开发者喜欢在并入主代码库之前用交互式 rebase 来完善他们的 feature 分支。他们可以将不重要的提交合在一起，删除不需要的，确保所有东西在提交到「正式」的项目历史前都是整齐的。对其他人来说，这个功能的开发看上去是由一系列精心安排的提交组成的。

### 栗子

![image-20200806144340762](/img/user/programming/basic/common/git/image-20200806144340762.png)

你可以更改每个提交前的 pick 命令来决定在 rebase 时提交移动的方式。在我们的例子中，我们只需要用 squash 命令把两个提交并在一起就可以了：

```
pick 32618c4 Start developing a feature
squash 62eed47 Fix something from the previous commit
```

保存并关闭编辑器以开始 rebase。另一个编辑器会打开，询问你合并后的快照的提交信息。在定义了提交信息之后，rebase 就完成了，你可以在 `git log` 输出中看到那个提交。

![image-20200806145723354](/img/user/programming/basic/common/git/image-20200806145723354.png)

注意缩并的提交和原来的两个提交的 ID 都不一样，告诉我们这确实是个新的提交。

最后，你可以执行一个快速向前的合并，来将完善的 feature 分支整合进主代码库：

```
git checkout master
git merge new-feature
```

交互式 rebase 强大的能力可以从整合后的 master 分支看出——额外的 `62eed47` 提交找不到了。对其他人来说，你就像是一个天才，用完美数量的提交完成了 `new-feature`。这就是交互式提交如何保持项目历史干净和合理。

## Git Reflog

Git 用引用日志这种机制来记录分支顶端的更新。它允许你回到那些不被任何分支或标签引用的更改。在重写历史后，引用日志包含了分支旧状态的信息，有需要的话你可以回到这个状态。

### 用法

```
git reflog
```

显示本地仓库的引用日志。

```
git reflog --relative-date
```

用相对的日期显示引用日志。(如 2 周前）。

### 讨论

每次当前的 HEAD 更新时（如切换分支、拉取新更改、重写历史或只是添加新的提交），引用日志都会添加一个新条目。

### 栗子

为了理解 `git reflog`，我们来看一个🌰。

```
0a2e358 HEAD@{0}: reset: moving to HEAD~2
0254ea7 HEAD@{1}: checkout: moving from 2.2 to master
c10f740 HEAD@{2}: checkout: moving from master to 2.2
```

上面的引用日志显示了 master 和 2.2 的 branch 之间的相互切换。还有对一个更老的提交的强制重设。最近的活动用 `HEAD@{0}` 标记在上方显示。

如果事实上你是不小心切换回去的，引用日志包含了你意外地丢掉两个提交之前 master 指向的提交 0254ea7。

```
git reset --hard 0254ea7
```

使用 [`git reset`](https://github.com/geeeeeeeeek/git-recipes/wiki/2.6-回滚错误的修改#git-reset)，就有可能能将 master 变回之前的那个提交。它提供了一张安全网，以防历史发生意外更改。

务必记住，引用日志提供的安全网只对提交到本地仓库的更改有效，而且只有移动操作会被记录。

### 撤销 Amend 的内容

https://www.jianshu.com/p/97341ed9d89e

git reset HEAD@{1}

## 重写远程项目的历史

这是一个错误的命题，已经提交到远程的历史就不应该修改，用 revert 就好了

通过 `git rebase -i ` 交互式修改历史后，使用 `git push -f` 强制提交到远程即可

```
git rebase -i HEAD~6
```

重写包括 head 在内的 6 个提交，即是最近的 6 个提交

修改完之后会落后与远程，此时不要 pull，直接 push -f 即可

edit 可以修改指定的提交，之后可以使用 git commit --amend 进行修改

# 保持同步

## Git Remote

`git remote` 命令允许你创建、查看和删除和其它仓库之间的连接。

### 用法

```
git remote
```

列出你和其他仓库之间的远程连接。

```
git remote -v
```

和上个命令相同，但同时显示每个连接的 URL。

```
git remote add <name> <url>
```

创建一个新的远程仓库连接。在添加之后，你可以将 `<name>` 作为 `<url>` 便捷的别名在其他 Git 命令中使用。

```
git remote rm <name>
```

移除名为的远程仓库的连接

```
git remote rename <old-name> <new-name>
```

将远程连接从 `<old-name>` 重命名为 `<new-name>`

## Git Fetch

`git fetch` 命令将提交从远程仓库导入到你的本地仓库。拉取下来的提交储存为远程分支，而不是我们一直使用的普通的本地分支。你因此可以在整合进你的项目副本之前查看更改。

### 用法

```
git fetch <remote>
```

拉取仓库中所有的分支。同时会从另一个仓库中下载所有需要的提交和文件。

```
git fetch <remote> <branch>
```

和上一个命令相同，但只拉取指定的分支。

## Git Push

git push -u origin master

这里的 master 其实是本地的 master

## Git Pull

拉取最新版本，与本地仓库进行比较，所以要先有本地仓库

如果远程仓库的版本高于本地仓库的版本，本地仓库是不能进行提交的，必须先拉取

**git pull origin dev(远程分支名称)**

## 指针头分离状态

注意：正在切换到 'FETCH_HEAD'。

您正处于分离头指针状态。您可以查看、做试验性的修改及提交，并且您可以在切换

回一个分支时，丢弃在此状态下所做的提交而不对分支造成影响。

如果您想要通过创建分支来保留在此状态下所做的提交，您可以通过在 switch 命令

中添加参数 -c 来实现（现在或稍后）。例如：

  git switch -c <新分支名>

或者撤销此操作：

  git switch -

通过将配置变量 advice.detachedHead 设置为 false 来关闭此建议

HEAD 目前位于 13f3978e refactor: 使用 props 生成 computed & 去除 computed 中只依赖 privateKey 的项 & style[] 统一改为字符串 (xiaochengxu00-27478)

## Github 多人协作开发流程

1. 克隆远程仓库，在本地进行开发
2. 将本地仓库开发的内容推送到远程仓库
3. 在之后的开发中，不断的拉取远程仓库的代码，再推送新的内容即可

### 成为贡献者

不然谁都可以提交，就没有办法管理了

邀请成为开发者，github-settings-collaborators

发送邀请了链接

### Pull Request

先 fork，克隆到本地修改，push 到 fork 仓库，在 fork 仓库发起 pull request

### 解决冲突

两个人修改了同一个文件的同一个地方就会发生冲突，只有先提交的人可以提交

后提交的人无法提交

只能拉取，然后 git 会标注冲突，冲突只能人为解决

把内容修改就可以了，保存成我们希望看到的，包括直接删除他人的内容

## Ssh 免登录

使用 http 访问仓库，win10 会帮我们记住用户名和密码，所以不用每次都输入

ssh 通过公私钥，公钥放在 GitHub 账户中，私钥保留在开发者的电脑中

头像 -settings-SSH and GPG keys

通过仓库的 clone or download 查找 ssh 地址

# Vscode 中使用 Git

在 vscode 中使用 git

<http://wiki.jikexueyuan.com/project/github-pages-basics/jekyll-page.html>

<https://keysaim.github.io/post/blog/2017-08-15-how-to-setup-your-github-io-blog/>

http://huangxuan.me/

<https://segmentfault.com/q/1010000013434786/a-1020000013444198>

<https://github.com/vuejs-templates/webpack/issues/446>

## 发布 Gh 分支

<https://juejin.im/post/5b14b2f06fb9a01e5e3d3121>

## 发布 SPA 项目到 Ghp

本地仓库操作：

1. 新建本地分支 gh-pages
2. 安装 gh-pages 包
3. 修改 package.json 文件，增加 homepage

    ```
    "homepage": "https://github.com/chiyu-git.github.io/chiyu-demo",
    ```

4. 新增打包命令，这里的 build 指向打包后的文件，一般是 dist

    ```js
    "scripts": {
      "predeploy": "npm run build",
      "deploy": "gh-pages -d build",
    }
    ```

5. 使用 npm run deploy 命令发布到 github pages
6. 以后要发布的时候 直接在主分支搞就行 npm run deploy

## Gui 联动 Git

ci_template 直接显示到 message 面板

其实我之前都是改的 ci_template，所以 git 可以去读取 template，自然就可以联动上了
