---
layout: article
title: Debian下打包
author: 沈卓洋
date: 2020-5-12 16:43:00 +0800
fig-caption: # Add fig caption (optional)
tags: [Linux,Debian]
---
首先创建一个文件夹，例如
```bash
mkdir ~/debian
```
将该文件夹看做根目录，将二进制包和桌面图标文件放入对应的文件夹中，例如我们安装TiddlyDesktop到/opt下，那么
```bash
mv ~/TiddlyDesktop/ ~/debian/opt/
```
并且创建开始菜单
```bash
touch ~/debian/usr/share/applications/TiddlyDeskop.desktop
```
写入开始菜单的信息，参阅[这篇博客](https://blog.shenzy.cn/2020/01/17/creat-start-icon-in-Linux.html).
然后加入一些安装卸载相关的文件
```bash
mkdir ~/debian/Debian
cd ~/debian/Debian
touch control postrm
```
其中control表示控制信息，其文件内容为
```
Package: 包名，不允许非小写字母和-+符号
Version: 版本号
Priority: optional
Description: 描述
Architecture: 架构，例如amd64
Maintainer: 维护者
Homepage: 主页
```
postrm表示卸载时候做些什么，例如其文件内容可以为
```
sudo rm -rf /opt/TiddlyDesktop
```
最后打包完事儿了

```bash
sudo dpkg -b ~/debian TiddlyWiki.deb
```

