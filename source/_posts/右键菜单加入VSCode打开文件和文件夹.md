---
title: 右键菜单加入VSCode打开文件和文件夹
date: 2024-05-15 17:52:37
tags: 技巧
---

# 右键菜单加入用VSCode打开文件和文件夹

由于安装VSCode时没有勾选“Open with Code”，导致无法通过右键文件夹打开VSCode。现在选择通过修改注册表的方式完成添加。

## 右键打开文件

1.  ==win+R==打开运行，输入==regedit==打开注册表，找到 ==HKEY_CLASSES_ROOT\*\shell== 分支,如果没有就新建一个项。
2. 在shell下新建一个==VisualCode==项，双击右侧窗口的==默认==，在数据里输入==用VSCode打开==。这个表示右键上显示的文字。
3. 在==VisualCode==下新建==Command==项，双击其默认键值栏，输入程序的exe路径==“D:\xx\xx\Code.exe” "%1"==（注意要加引号） 其中%1表示要打开的文件参数。
4. 配置缩略图。在==VisualCode==项上新建==可扩充字符串值==,命名为==Icon==，值为路径==“D:\xx\xx\Code.exe”==
5. 关闭注册表即可生效

## 右键打开文件夹

在注册表中找==HKEY_CLASSES_ROOT\Directory\shell==分支，其余和前面的全部一样。

## 右键空白处打开

在注册表中找==HKEY_CLASSES_ROOT\Directory\Background\shell==分支，其他的一样，除了第3步==%1==改为==%V==

