---
title: 深度学习pycharm远程ssh连接
date: 2024-04-29 11:44:39
tags: AI
---

# 服务器端配置

## 软件安装

### SSH

远程终端连接,开启服务之后，客户端可以通过输入IP地址连接服务器（ifconfig查看）

```shell
sudo apt install ssh 
chkconfig --level 2345 sshd on #SSH服务开机自启动
ps -e | grep ssh    #查看ssh服务状态
service sshd status #也可以用这句
```

### xrdp

远程桌面软件，客户端可以直接通过windows远程桌面连接。

```shell
sudo apt install xrdp 
sudo systemctl status xrdp#验证安装是否成功
sudo adduser xrdp ssl-cert  #将xrdp用户添加到ssl-cert用户组
sudo systemctl restart xrdp
```

# 客户端配置

pycharm专业版有专门的远程连接选项。

## 工程建立

新建一个pycharm工程

![image-20240503184928184](http://typora-sdj.oss-cn-chengdu.aliyuncs.com/img/image-20240503184928184.png)

## 部署设置

![image-20240429115441411](http://typora-sdj.oss-cn-chengdu.aliyuncs.com/img/image-20240429115441411.png)

![image-20240429115534125](http://typora-sdj.oss-cn-chengdu.aliyuncs.com/img/image-20240429115534125.png)

![image-20240429115601170](http://typora-sdj.oss-cn-chengdu.aliyuncs.com/img/image-20240429115601170.png)

## 远程python解释器配置

![image-20240429115718683](http://typora-sdj.oss-cn-chengdu.aliyuncs.com/img/image-20240429115718683.png)

![image-20240429115805507](http://typora-sdj.oss-cn-chengdu.aliyuncs.com/img/image-20240429115805507.png)

## 服务器conda多用户配置

### 服务端首先建立一个新用户

```shell
sudo adduser aaa #创建一个用户名为aaa的新用户,创建的用户在home目录下
```

### conda多用户配置

在安装了anaconda的用户下，打开.bashrc文件找到anaconda initialize部分(通常有两个部分)，复制第一段到新用户的.bashrc文件下面。



