---
title: bash调试方法之trap命令
date: YYYY-MM-DD HH:mm:ss
categories: 
- shell脚本
tags:
- bash调试方法之trap命令
---
trap是Linux内建命令，用于捕捉信号。trap命令可以收到某种信号时所执行的命令，其格式如下：

```bash
trap command sig1 sig2 ...sigN
```

Shell在执行时，会产生三个伪信号，即shell产生的信号，不是操作系统产生的信号。

- EXIT ：从函数退出，或整个脚本执行完毕
- ERR ：当一条命令返回非0
- DEBUG ：脚本中每条命令执行之前

***下面举例：***

```bash
#!/bin/bash

function test_trap()
{
	echo 'test_trap';
	var=2016;
	return 1;
}

trap 'echo "[$LINENO], var = $var"' DEBUG;
trap 'echo "[$LINENO], return error"' ERR;
trap 'echo "[$LINENO], function exit"' EXIT;
test_trap;
```