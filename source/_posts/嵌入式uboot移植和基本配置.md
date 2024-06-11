---
title: 嵌入式uboot移植和基本配置
date: 2024-04-30 15:52:10
tags: 嵌入式
---



# linux系统移植

一个完整的linux系统包括bootloader(最常用的是U-Boot)、Linux kernel和rootfs。

![image-20240501131138118](http://typora-sdj.oss-cn-chengdu.aliyuncs.com/img/image-20240501131138118.png)

## MX6ULL启动方式

MX6ULL的boot模式可以通过GPIO高低电平选择。

串行下载：通过USB或者UART将代码下载到外置存储设备，可以通过OTG1这个USB口向SD、NAND下载代码。

内部boot模式：芯片执行内部的bootROM代码，进行硬件初始化，从boot设备将代码拷贝到指定的RAM中。

![image-20240430161001019](C:/Users/63155/AppData/Roaming/Typora/typora-user-images/image-20240430161001019.png)

![image-20240430160816613](http://typora-sdj.oss-cn-chengdu.aliyuncs.com/img/image-20240430160816613.png)

## uboot是啥？如何移植？

芯片上电后先运行一段bootloader程序，用于初始化DDR等外设，然后将linux内核从flash拷贝到DDR中，最后启动linux内核。

uboot(Universal Boot Loader)可以看作一个裸机综合例程，通常存在3三种uboot类型。

![image-20240501131401745](http://typora-sdj.oss-cn-chengdu.aliyuncs.com/img/image-20240501131401745.png)

一般都是选择开发板厂商的uboot代码。如果有些外设驱动不支持，就需要自己移植。

### 移植过程

uboot支持图形化配置界面，所以首先安装ncurses库，是字符终端的图形界面库，否则编译会出错。

```shell
sudo apt-get install libncurses5-dev
```

下载解压uboot文件，是一个包含很多文件的大型工程源码。**根据需要可以自行修改其中的文件（这个过程才叫移植，具体可以看正点原子驱动开发第31章到34章，讲解了内部文件原理和修改方法）**。

![image-20240501132439187](http://typora-sdj.oss-cn-chengdu.aliyuncs.com/img/image-20240501132439187.png)

然后根据使用的开发板型号进行编译，我采用的开发板是512MB(DDR3)+8GB(EMMC)的核心板，通过下面的命令进行编译。命令主要包含:①ARCH:目标为arm架构②CROSS_COMPILE:指定交叉编译器。

```shell
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- distclean #清除工程
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf-  mx6ull_14x14_ddr512_emmc_defconfig   #配置uboot为DDR512和emmc(在configs目录中进行编写)
make V=1 ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- -j12 #用12核编译uboot
```

编译完成后生成的文件主要有u-boot.bin，u-boot.imx。u-boot.bin是裸机程序，需要在其前面加上头部(IVT、DCD等数据)才能在MX6ULL上执行，即u-boot.imx。

最后将uboot.imx写入SD卡中，然后用SD卡来启动uboot。这里用正点原子写好的烧写程序写入SD卡中,将boot设置为SD卡启动。

```shell
chmod 777 imxdownload #给予 imxdownload 可执行权限，一次即可
./imxdownload u-boot.bin /dev/sdd #烧写到 SD 卡，不能烧写到/dev/sda 或 sda1 设备里面！
```

将板子用串口线连接到电脑，就可以通过串口终端软件进入uboot命令行模式。输入help可以直接查看支持的所有命令。

## linux内核移植和启动

首先需要安装lzop库，这是一款文件压缩工具，使用lzo压缩库来提供服务，linux内核使用了该算法进行压缩，所以安装了lzop库才能编译成功。

```shell
sudo apt-get install lzop
```

解压完后的linux源码目录如下（如果想知道原理和修改参考正点原子35-37章）

![image-20240501141812108](http://typora-sdj.oss-cn-chengdu.aliyuncs.com/img/image-20240501141812108.png)

编译过程如下：

```shell
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- distclean
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- imx_v7_defconfig 
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- menuconfig #用图形配置界面
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- all -j16
```

由于配置了图形界面，所以编译过程会弹出linux图形配置界面。这里不用做任何配置，按ESC退出即可。

编译完成后：

- arch/arm/boot目录下生成zImage，也就是linux镜像文件；

- 在arch/arm/boot/dts下生成很多.dtb文件，也就是设备树文件。

### linux内核修改(这节可以不用做，前面配置文件已经有了)

NXP会将linux内核某个版本一致到自己的CPU后，再开放给NXP的CPU开发者，这里我采用NXP修改后的linux版本。

#### 修改顶层Makefile

首先需要修改顶层Makefile，将ARCH和CROSS_COMPILE这两个变量的值改为arm和arm-linux-gnueabihf（即配置cpu架构和交叉编译器）

#### 配置并编译linux内核

imx_v7_mfg_defconfig配置文件默认支持MX6UL,且编译出来的zImage文件可以通过NXP官方提供的MfgTool工具烧写（文件名中mfg就代表这个意思）。

### linux内核启动

首先要把uboot环境变量**bootargs**内容修改一下

设置linux终端为UART1，波特率为115200，根文件系统放在mmcblk1设备的分区2（即EMMC的分区2），等待mmc设置初始化以后再挂载，根文件系统是可读写的。

```shell
console=ttymxc0,115200 root=/dev/mmcblk1p2 rootwait rw
```

将zImage文件和imx6ull-14x14-evk.dtb文件复制到虚拟机tftp目录下，因为要通过uboot中的tftp命令将其下载到开发板中。

```shell
cp arch/arm/boot/zImage /home/sdj/linux/tftpboot/ -f
cp arch/arm/boot/dts/imx6ull-14x14-evk.dtb /home/sdj/linux/tftpboot/ -f
```

然后启动开发板进入uboot命令模式，用tftp命令下载到开发板中

```shell
tftp 80800000 zImage
tftp 83000000 imx6ull-14x14-evk.dtb
bootz 80800000 - 83000000
```

此刻linux内核已经启动了。linxu内核启动之后是需要根文件系统的，bootargs环境变量已经指定了根文件系统存储再/dev/mmcblk1p2中，也就是EMMC分区2中。且我的开发板再出场的时候已经再EMMC分区2中烧写好了根文件系统，所以可以直接用。

### 内核修改

由于前面编译的是NXP官方的MX6UL EVK型号的linux内核，我的板子是MX6UL ALPHA,内核能正常运行，是因为CPU是一个型号，但是外设会有很多不一样的地方，所以需要修改配置文件来支持的自己的板子的设备。具体参考正点原子37.3节

## 根文件系统

根文件系统一般叫做rootfs，根文件系统是内核启动时mount（挂载）的第一个文件系统，内核代码映像文件保存在根文件系统中，而系统引导启动程序会在根文件系统挂载之后从中把一些基本的初始化脚本和服务等加载到内存中去运行 。（具体原理参考正点原子第38章）
