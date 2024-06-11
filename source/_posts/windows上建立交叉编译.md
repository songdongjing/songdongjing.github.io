---
title: windows上建立交叉编译
date: 2024-05-16 16:57:50
tags: 嵌入式 
---

## 交叉编译的目的

交叉编译就是在一种计算机环境种运行编译程序能编译出另外一种环境下运行的代码。由于嵌入式系统里使用的芯片性能比较弱，，所以离不开X86电脑强大高效的桌面环境进行软件开发。

常见的交叉编译工具链为

- 在Windows PC上，利用ADS（ARM 开发环境），使用armcc编译器，则可编译出针对ARM CPU的可执行代码。
- 在Linux PC上，利用arm-linux-gcc编译器，可编译出针对Linux ARM平台的可执行代码。
- 在Windows PC上，利用cygwin环境，运行arm-elf-gcc编译器，可编译出针对ARM CPU的可执行代码。

## 工具链的种类

GCC的命名规则为：==**arch [-vendor] [-os] [-(gnu)eabi]-gcc**==

- 带 [] 的是可选部分。

- arch： 芯片架构，比如 32 位的 Arm 架构对应的 arch 为 arm，64 位的 Arm 架构对应的 arch 为 aarch64。

- vendor ：工具链提供商，大部分工具链名字里面都没有包含这部分。

- os ：编译出来的可执行文件(目标文件)针对的操作系统，比如 Linux。

  比如arm-linux-gnueabi-gcc和aarch64-linux-gnu-gcc表示适用于Arm Cortex-A 系列芯片，使用的是glibc库。

## 建立交叉编译环境

比较权威的建立交叉编译环境的教程如下。

1. IBM的《[如何为嵌入式开发建立交叉编译环境](http://www.ibm.com/developerworks/cn/linux/l-embcmpl/)》
2. huihoo的《[**一步一步的制作arm-linux交叉编译环境**](http://docs.huihoo.com/rt_embed/arm_linux.html)》

更方便的是直接用编译好的交叉编译环境。

http://ftp.kelp.or.kr/pub/arm-linux/toolchain/，http://ftp.handhelds.org/projects/toolchain/，http://www.handhelds.org/download/projects/toolchain/