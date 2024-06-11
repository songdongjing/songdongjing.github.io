---
title: mysql安装与远程连接
date: 2024-05-27 19:21:02
tags: 工具使用
---

## 1.动机

在做智能空战实验的时候，由于生成了一系列的数据，通过csv文件保存在本地有点麻烦，正好我们实验室有一台长期开机的服务器，我打算试一下在服务器上安装sql来保存我生成的所有数据，从而更方便的查看我的数据。

## 2.安装过程

### 2.1安装mysql

首先打开MobaXterm，用SSH远程连接服务器，在服务器下输入下列命令。

```shell
sudo apt install mysql-server -y  # 安装
mysql --version  # 查看版本
sudo systemctl status mysql  # 查看运行状态
netstat -tln  # 以数字ip形式显示mysql的tcp监听状态
```

### 2.2设置mysql的root密码

```shell
sudo mysql -u root  # 使用root无密码登录
```

登录后输入下列语句

```mysql
alter user 'root'@'localhost' identified with mysql_native_password by 'password';  # 为root添加密码
exit;
```

### 2.3设置允许root远程登录和ip远程登录

```shell
mysql -u root -p  # 使用root有密码登录
```

在mysql终端中输入下列语句

```mysql
use mysql;  # 使用名为mysql的数据库
update user set host='%' where user='root';  # 运行root远程登录
flush privileges;  # 权限刷新
exit;
```

直接远程登录会失败，需要修改配置文件让所有ip都可以远程登录

```shell
sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
修改bind-address = 0.0.0.0
修改后重启：sudo systemctl restart mysql
```

测试连接语句

```shell
mysql -u root -p -h 192.168.1.100（ip替换为实际MySQL服务器ip）
```

## 3.远程连接

由于我的主要ide为pycharm，其有专门的用于数据库远程连接的工具。

![image-20240527193057408](http://typora-sdj.oss-cn-chengdu.aliyuncs.com/img/image-20240527193057408.png)

![image-20240527193150108](http://typora-sdj.oss-cn-chengdu.aliyuncs.com/img/image-20240527193150108.png)

需要注意的是，这里需要安装mysql的驱动，直接下载会下载失败，需要开代理，然后在界面设置自动远程http代理。

一般连接上后，不会显示所有的数据库，还需要进行如下操作。

![image-20240527193400828](http://typora-sdj.oss-cn-chengdu.aliyuncs.com/img/image-20240527193400828.png)

![image-20240527193415128](http://typora-sdj.oss-cn-chengdu.aliyuncs.com/img/image-20240527193415128.png)

这样就可以直接右键csv文件，选择导入数据库就能看到了。
