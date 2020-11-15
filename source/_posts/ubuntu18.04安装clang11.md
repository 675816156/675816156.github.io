---
title: ubuntu18.04安装clang11
date: YYYY-MM-DD HH:mm:ss
categories: 
- Ubuntu
tags:
- 1、添加软件源
- 2、设置key
- 3、update一下源
- 4、安装
- 5、创建软连接
---
## 1、添加软件源

```bash
sudo vim /etc/apt/sources.list

deb http://apt.llvm.org/bionic/ llvm-toolchain-bionic-11 main
deb-src http://apt.llvm.org/bionic/ llvm-toolchain-bionic-11 main
```

## 2、设置key

```shell
tsocks wget -O - https://apt.llvm.org/llvm-snapshot.gpg.key | sudo tsocks apt-key add -
```

## 3、update一下源

```shell
 sudo tsocks apt-get update
```

## 4、安装

```shell
sudo tsocks apt-get install clang-11 lldb-11 lld-11 libc++-11-dev libc++abi-11-dev -y
```

## 5、创建软连接

```shel
sudo ln -s clang-11 /usr/bin/clang
sudo ln -s clang++-11 /usr/bin/clang++
sudo ln -s /usr/bin/llvm-ar-11 /usr/bin/llvm-ar
sudo ln -s /usr/bin/llvm-as-11 /usr/bin/llvm-as
sudo ln -s /usr/bin/clangd-11 /usr/bin/clangd
sudo ln -s /usr/bin/clang-tidy-11 /usr/bin/clang-tidy
```

[ubuntu clang安装方法]: 参考链接(https://apt.llvm.org/)

