---
title: NFS挂载
date: 2024-05-06 19:54:19
tags: 嵌入式
---

# 嵌入式开发工作流

对于嵌入式开发而言一般需要接触3个系统，windows写文件或者写程序，虚拟机中开ubuntu系统交叉编译程序生成执行文件，开发版中将设备驱动文件加载或者运行编译好的可执行程序。所以这三大系统必然存在一个文件交换的过程。

## windows-ubuntu

通过filezilla通过网络连接，采用FTP协议传输文件；用SSH服务进行远程控制。

## windows-开发板

通过MobaXterm用串口连接终端，或者用网络（见下一节）

## ubuntu-开发板

通常通过网络连接将ubuntu目录挂载到开发板上。这个过程较为麻烦，有以下流程：

### 安装网络服务

#### ubuntu系统中

NFS是一种网络服务，开发板可以通过网线连接ubuntu来使用NFS服务，也可以通过OTG线连接。

首先安装NFS服务。

```shell
sudo apt-get install nfs-kernel-server#安装NFS服务
```

修改配置文件，允许开发板访问该目录

```shell
/home/nfs *(rw,nohide,insecure,no_subtree_check,async,no_root_squash)
```

重启NFS服务

```shell
sudo /etc/init.d/nfs-kernel-server restart
```

#### 开发板中

由于我选择采用OTG线进行连接，所以需要安装驱动模拟USB网卡。

```shell
modprobe g_ether
```

安装返回的语句中表示模拟出网卡USB0（或者是别的），然后设置其IP地址

```shell
ifconfig usb0 10.10.70.1
```

#### 在ubuntu中进行挂载

查找网卡设备

```shell
ifconfig -a
```

可以看到网卡设备为ens33(一般不一样)。然后设置其IP地址，并测试网络连接

```shell
sudo ifconfig ens35u1 10.10.70.2
ping -I usb0 10.10.70.2
```

然后就可以挂载了

```shell
mount -t nfs -o nolock 10.10.70.2:/home/sdj/linux/nfs /mnt
df -h #查看挂载的情况
```

挂载完后就可以在ubuntu系统中将文件复制到/nfs文件中，开发板就可以在/mnt中找到该文件。
