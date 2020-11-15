---
title: 使用Shell选项进行调试
date: YYYY-MM-DD HH:mm:ss
categories: 
- shell脚本
tags:
- Shell的调试选项、简写及其意义：
- shell用于调试的内部变量及其含义
---
# Shell的调试选项、简写及其意义：

- ***noexec*** ：n：读取脚本中命令，进行语法检查，但并不执行这些命令
- ***xtrace*** ：x：在执行每个命令前，将每个命令打印到标准输出(stdout)
- ***无*** ：c…：从…中读取命令

***例如：***

```bash
set -n
echo "Start executing this script..."
var=0
while :
	if [ $var -gt 3]
	then
		break
	fi
	let "var=var+1"
done
```

***执行结果：***

```bash
jun@jun:~/shell$ ./debug_3.sh
./debug_3.sh: line 10: syntax error near unexpected token `done'
./debug_3.sh: line 10: `done' #直接输出错误信息。而未打印Start executing this script...
```

还可以`sh -n 脚本名`

# shell用于调试的内部变量及其含义

- ***LINENO*** ：表示shell脚本的行号
- ***FUNCNAME*** ：数组变量，表示整个调用链上所有的函数名
- ***PS4*** ：设置-x选项的提示符，默认值是”+”符合

