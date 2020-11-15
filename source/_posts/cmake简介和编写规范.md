---
title: cmake简介
date: YYYY-MM-DD HH:mm:ss
categories: 
- cmake
tags:
- cmake命令一般格式
- cmake中变量的使用
- 第一个实例 Hello World for CMake
  - 在源码所在目录编译：
  - 如果你想把编译生成的中间文件放到另外一个目录可以使用下面的方式：
- 设置使用的编译器：
- 添加编译选项：
---
cmake是一种开源的软件工程构建管理工具，开发人员可以设定一系列参数来配置工程构建的过程。这些参数可以写到一个文本文件中，cmake读取此文件，生成Microsoft Visual Studio或AppleXcode或automake、nmake、Borland等格式的Makefiles，以前没有cmake你就要在你代码工程里放一堆适应不同构建系统的Makefile文件。

# cmake命令一般格式

cmake是识别指定目录下的CMakeLists.txt然后执行文件中的命令，命令基本格式如下：

```
command (args ...) # 这是注释，args参数之间用空格隔开
```



# cmake中变量的使用

cmake中变量引用：`${VAR}`
比如：

```
set (Foo a b c)
command (${Foo}) # 等同于command (a b c)，给命令传递三个参数
command ("${Foo}") # 等同于command ("a b c"), 给命令传递一个参数
​``` 
系统的环境变量或注册表可以被cmake直接访问，例如：
​```shell
$ENV{VAR} # 访问系统环境变量
[HKEY_CURRENT_USER\\Software\\path1\\path2;key] # 访问注册表
```



# 第一个实例 Hello World for CMake

`CMakeLists.txt`:

```
cmake_minimum_required(VERSION 3.2.2) # 指定系统需提供的cmake最低版本
project (hello) # 设置workspace里的工程名字

set (SRCS hello.c) # 设置project中的源文件，可以加多个用空格隔开

add_executable(hello ${SRCS}) # 设置编译hello目标，所需要的源文件
```



## 在源码所在目录编译：

1、`cmake .`
2、`make`

## 如果你想把编译生成的中间文件放到另外一个目录可以使用下面的方式：

```
mkdir out
cd out/
cmake ../
make
```

# 设置使用的编译器：

```
set (CMAKE_CXX_COMPILER "aarch64-linux-gnu-g++")`
`set (CMAKE_C_COMPILER "aarch64-linux-gnu-gcc")
```

# 添加编译选项：

```
set (CMAKE_CXX_FLAGS "-DDEBUG")`
`set (CMAKE_C_FLAGS "-DDEBUG")
```