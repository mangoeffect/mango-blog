---
title: "ubuntu安装最新版clang"
categories: [ "工具" ]
tags: [ "ubuntu","clang","llvm" ]
draft: false
slug: "tools/install-clang-latest-on-ubuntu"
date: "2021-12-22 22:12:00"
---

## 添加llvm软件源

到llvm官网可以查看各个版本linux的源地址，官网地址[https://apt.llvm.org/](https://apt.llvm.org/)。以下是ubuntu18.04和ubuntu20.04这两个主流版本的llvm软件源地址：

- ubuntu18.04

```
deb http://apt.llvm.org/bionic/ llvm-toolchain-bionic main
deb-src http://apt.llvm.org/bionic/ llvm-toolchain-bionic main
# Needs 'sudo add-apt-repository ppa:ubuntu-toolchain-r/test' for libstdc++ with C++20 support
# 12
deb http://apt.llvm.org/bionic/ llvm-toolchain-bionic-12 main
deb-src http://apt.llvm.org/bionic/ llvm-toolchain-bionic-12 main
# 13
deb http://apt.llvm.org/bionic/ llvm-toolchain-bionic-13 main
deb-src http://apt.llvm.org/bionic/ llvm-toolchain-bionic-13 main
```

- ubuntu20.04

```
deb http://apt.llvm.org/focal/ llvm-toolchain-focal main
deb-src http://apt.llvm.org/focal/ llvm-toolchain-focal main
# 12
deb http://apt.llvm.org/focal/ llvm-toolchain-focal-12 main
deb-src http://apt.llvm.org/focal/ llvm-toolchain-focal-12 main
# 13
deb http://apt.llvm.org/focal/ llvm-toolchain-focal-13 main
deb-src http://apt.llvm.org/focal/ llvm-toolchain-focal-13 main
```

ubuntu系统软件源列表通常保存在/etc/apt/sources.list文件中，打开该文件在其末尾添加以上内容。推荐新建list文件的做法，参考google-chrome的做法，在

```
/etc/apt/sources.list.d/
```

新增单独的list文件，可以新建一个clang.list文件单独存放llvm的软件源地址，这样可以保持系统软件源地址与默认一致。

```
sudo vim /etc/apt/sources.list.d/
```

## 安装

完成软件源添加后，打开终端执行以下命令更新可用软件源地址

```
sudo apt update
```

执行安装，以下命令会默认安装最新稳定版clang

```bash
sudo apt install clang
```

或者按照完整的llvm工具链

```
sudo apt-get install clang-format clang-tidy clang-tools clang clangd libc++-dev libc++1 libc++abi-dev libc++abi1 libclang-dev libclang1 liblldb-dev libllvm-ocaml-dev libomp-dev libomp5 lld lldb llvm-dev llvm-runtime llvm python-clang
```

等等安装完成，查看clang版本

```
clang -v
Ubuntu clang version 14.0.0-++20211221052852+55c71c9eac9b-1~exp1~20211221172954.95
Target: x86_64-pc-linux-gnu
Thread model: posix
InstalledDir: /usr/bin
Found candidate GCC installation: /usr/bin/../lib/gcc/x86_64-linux-gnu/9
Selected GCC installation: /usr/bin/../lib/gcc/x86_64-linux-gnu/9
Candidate multilib: .;@m64
Selected multilib: .;@m64
```

-----

本文由芒果浩明发布，转载请注明出处。
本文链接：[https://mangoroom.cn/tools/install-clang-latest-on-ubuntu.html](https://mangoroom.cn/tools/install-clang-latest-on-ubuntu.html)