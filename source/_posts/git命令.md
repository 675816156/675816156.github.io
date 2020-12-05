---
title: git命令笔记
date: YYYY-MM-DD HH:mm:ss
categories: 
- git
tags:
- git
---
# 关于git status时显示utf-8中文为八进制字符编码问题，解决中文文件名在git命令中的显示问题：

```
git config --global core.quotepath false
```

# 设置提交说明对应的字符编码，才能用git log正常显示：

```
git config --global i18n.logOutputEncoding gbk
```

# 设置录入提交说明时使用的字符编码：

```
git config --global i18n.commitEncoding gbk
```

# 配置用户名和邮箱

```
git config --global user.name "chenjun"
git config --global user.email chenjun675816156@gmail.com
```

# 设置git命令别名

```
git config --global alias.st status
git config --global alias.ci commit
git config --global alias.co checkout
git config --global alias.br branch
```

# 开启git颜色设置

```
git config --global color.ui true
```

# 编辑全局配置

```
git config -e --global
```

# git指定版本库目录和工作目录

```
git --git-dir=<path> --work-tree=<path>
# GIT_DIR环境变量为工作区指定版本库目录
```

# 使用专门的grep命令，这样在工作区根目录下执行查找

```
git grep -n "start_kernel"
```

# git修改提交说明

```
# 下面这个命令只能修改最近的那次提交说明
git commit --amend
# 下面可以修改任意提交说明
git rebase -i <commit-id>^
# 5 # Commands:
# 6 # p, pick = use commit 空操作
# 7 # r, reword = use commit, but edit the commit message 编辑提交信息
# 8 # e, edit = use commit, but stop for amending 停止在指定的commit-id可以添加修改的代码或文件，并修改提交信息
# 9 # s, squash = use commit, but meld into previous commit 把指定的commit修改代码合并到上一个commit
#10 # f, fixup = like "squash", but discard this commit's log message 同squash只是放弃commit信息
#11 # x, exec = run command (the rest of the line) using shell 类似reset --hard到那个commit前一个提交，执行命令再回来，如果不执行什么命令直接rebase --continue，那么x执行的那个commit就自动删除了。
```

# git add命令

```
# 将所有修改过的文件(包括deleted和modified，不包括new)加入暂存区
git add -u
# 将本地删除文件和新增文件(包括deleted、modified、new)都登记到暂存区
git add -A
# 对一个文件内的修改进行有选择性的添加
git add -p
```

# git diff差异比较

```
# 支持二进制文件比较，逐行比较，逐字比较。
git diff --word-diff
git diff          # 工作区和暂存区对比差异
git diff --cached # 暂存区和版本库对比差异
git diff HEAD     # 工作区和版本库对比差异
```

# 工作进度保存

```
# 如果在工作区的修改尚未完全时，有紧急任务，需要从一个干净的工作区开始新的工作
git stash
git checkout <new_branch>
# 紧急任务完成，继续原来的工作
git checkout <orignal_branch>
git stash pop
```

# 禁止Git对文件权限的跟踪

```
git config --local  core.fileMode false
```

# 显示git仓库的位置

```
# 显示git仓库目录
git rev-parse --git-dir
# 显示工作区根目录
git rev-parse --show-toplevel
# 显示当前相对于工作区的相对目录
git rev-parse --show-prefix
# 显示放弃目录后退到工作区的根的深度
git rev-parse --show-cdup
```

# git status命令查看文件状态

```
git status -s
M  (Modified)
D  (Deleted)
A  (Add)
?? (New)
第二列 表示文件的修改依旧在工作区，未提交到暂存区
第一列 文件已提交到暂存区
```