---
title: uniq命令基本用法
date: YYYY-MM-DD HH:mm:ss
categories: 
- shell脚本
tags:
- uniq命令选项：
---
uniq命令用于去除文本文件中的重复行，类似sort命令的-u选项，但是uniq命令和sort -u命令存在区别：

# uniq命令选项：

```
-c ： 打印每行在文本中重复出现的次数（可用于统计文件单词数目）
-d ： 只显示有重复的记录，每个重复记录只出现一次
-u ： 只显示没有重复的记录
```