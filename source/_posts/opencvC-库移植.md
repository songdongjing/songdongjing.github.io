---
title: opencvC++库移植
date: 2024-05-18 10:18:31
tags: 嵌入式
---

## 原因

由于嵌入式设备进行图像处理需要主打高性能的C++语言进行编程，所以需要移植opencvC++库。

## Ubuntu系统上的移植

ubuntu系统上的移植主要是针对嵌入式设备上进行处理，所以要加入交叉编译链，不能直接运行。

### 交叉编译环境配置

选择Linaro出品的交叉编译器，[Linaro Releases](https://releases.linaro.org/components/toolchain/binaries/4.9-2017.01/arm-linux-gnueabihf/)

安装过程

- 先创建一个文件夹，将交叉编译器解压到该文件夹中，并将其加入到环境变量中。

```shell
sudo mkdir /usr/local/arm 
sudo tar xf gcc-linaro-4.9.4-2017.01-x86_64_arm-linux-gnueabihf.tar.xz -C /usr/local/arm/
sudo vi /etc/profile
export PATH=$PATH:/usr/local/arm/gcc-linaro-4.9.4-2017.01-x86_64_arm-linux-gnueabihf/bin
```

- 验证过程：安装一些库来使用该编译器，并通过命令输出其版本号

```shell
sudo apt-get install lsb-core lib32stdc++6 
arm-linux-gnueabihf-gcc -v
```

### opencv3.4.1移植

下载地址[Releases - OpenCV](https://opencv.org/releases/)

- 在解压的文件夹中创建一个构建文件夹build和安装文件夹install

```shell
tar xf opencv-3.4.1.tar.gz
cd opencv-3.4.1/
mkdir build install
sudo aptitude install cmake cmake-qt-gui cmake-curses-gui
```

- 然后进入build目录，用cmake-gui生成makefiles。

```shell
cd build
cmake-gui
```

![image-20240518103225058](http://typora-sdj.oss-cn-chengdu.aliyuncs.com/img/image-20240518103225058.png)

![image-20240518103348130](http://typora-sdj.oss-cn-chengdu.aliyuncs.com/img/image-20240518103348130.png)

![image-20240518103402985](http://typora-sdj.oss-cn-chengdu.aliyuncs.com/img/image-20240518103402985.png)

![image-20240518103428537](http://typora-sdj.oss-cn-chengdu.aliyuncs.com/img/image-20240518103428537.png)

![image-20240518103441915](http://typora-sdj.oss-cn-chengdu.aliyuncs.com/img/image-20240518103441915.png)

![image-20240518103458728](http://typora-sdj.oss-cn-chengdu.aliyuncs.com/img/image-20240518103458728.png)

![image-20240518103510182](http://typora-sdj.oss-cn-chengdu.aliyuncs.com/img/image-20240518103510182.png)

-  HAVE_PTHREAD宏定义了编译需要pthread库 ，在common.cc文件中添加\#define HAVE_PTHREAD 宏定义才可以通过编译 

```shell
cd .. // 返回 opencv 源码顶层目录
vi 3rdparty/protobuf/src/google/protobuf/stubs/common.cc
```

![image-20240518103811326](http://typora-sdj.oss-cn-chengdu.aliyuncs.com/img/image-20240518103811326.png)

- 编译

```shell
cd build/
make -j 16
make install #把库安装到install目录下.
```

## windows上opencv移植

目前参考此教程尝试过安装3.4.1，在生成makefile文件时报错[2023年最全 Windows + VSCode 配置 OpenCV C++ 一站式开发调试环境教程 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/599002329)

```shell
CMake Error at cmake/OpenCVFindLibsVideo.cmake:221 (include):
  include could not find requested file:

    F:/embeded_learning/race/opencv3.4.1/opencv/build/mingw64-build/3rdparty/ffmpeg/ffmpeg_version.cmake
Call Stack (most recent call first):
  CMakeLists.txt:636 (include)
```

似乎是opencv库文件不全啥的。
