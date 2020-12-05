---
title: bash调试方法之tee命令
date: YYYY-MM-DD HH:mm:ss
categories: 
- shell脚本
tags:
- 调试钩子
---
tee命令将一个输出分为两个支流，一个到标准输出，另一个到某个输出文件。tee命令用于shell脚本的管道及输入/输出重定向的调试上。
例如：

```bash
ips=`ifconfig |tee 1| grep 'inet addr'|tee 2| cut -d: -f2 |tee 3| cut -d ' #
echo $ips
```

# 调试钩子

例如：

```bash
DEBUG()
{
	if [ "$DEBUG" = "true" ];
	then
		$@
	fi
}

a=0
b=2
c=100
DEBUG echo "a=$a b=$b c=$c"
while :
do
	DEBUG echo "a=$a b=$b c=$c"
	if ((a >= 10))
	then
		break
	fi

	let "a=a+2"
	let "b=b*2"
	let "c=c-10"
done
```

执行结果：

```bash
jun@jun:~/shell$ DEBUG=true ./debug_2.sh
a=0 b=2 c=100
a=0 b=2 c=100
a=2 b=4 c=90
a=4 b=8 c=80
a=6 b=16 c=70
a=8 b=32 c=60
a=10 b=64 c=50
```