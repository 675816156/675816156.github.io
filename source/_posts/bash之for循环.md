---
title: bash之for循环
date: YYYY-MM-DD HH:mm:ss
categories: 
- shell脚本
tags:
- 方式一 执行几次
- 方式二 遍历命令行参数
- 方式三 遍历当前文件
---
# 方式一 执行几次

```shell
#!/bin/bash

for ((i = 0; i < 5; ++i));
# for i in {1..5};
# for i in {1..5..2}; i每次步长为2
do
    echo $i;
done
```

# 方式二 遍历命令行参数

```shell
#!/bin/bash

for arg;
# for arg in $@;
do
    echo $arg;
done
```

# 方式三 遍历当前文件

```shell
#!/bin/bash

for i in *;
# for i in ac apropos at arp; 
do
    echo $i;
done
```