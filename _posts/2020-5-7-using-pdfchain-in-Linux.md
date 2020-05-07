---
layout: article
title: Linux下使用pdfchain
date: 2020-5-7 14:00:00 +0800
fig-caption: # Add fig caption (optional)
tags: [Linux]
---

Linux下对pdf文件进行一般操作，例如合并、拆分等，可以用到一些命令行的工具，例如pdftk、pdfjam，但是我总是记不住命令，每次查帮助文档略感麻烦，因此推荐一款图形界面工具pdfchain，它将pdftk做了图形界面，方便操作，支持合并、拆分、旋转、加密等.
pdfchain的UI如下图所示：
<img style="display: block; margin: 0 auto;" src="/assets/images/2020-5-7/pdfchain.png" alt="" />

## 下载
点击[这里](https://sourceforge.net/projects/pdfchain/files/latest/download)或复制到浏览器即可直接下载.
加载速度可能比较慢，好在源码包很小只有几百K，很快就能下完.

## 安装
首先`cd`到源码包的文件夹，然后执行以下命令即可
```bash
  ./configure
  make
  make install
```
## 报错

如果提示make没有指明目标并且找不到makefile，那么就是configure没有执行成功，导致没有生成makefile文件，查看执行configure时报的错即可.
而configure没有执行成功很有可能是缺少依赖或依赖版本太低，pdfchain需要用到以下依赖：

 * libsigc++
 * glibmm
 * giomm
 * gtkmm

例如缺少gtkmm-3.0，安装以下就可以了
```bash
sudo apt install gtkmm-3.0
```
另外， `make install`时可能会提示权限不足，那么用管理员权限执行就行了
```bash
sudo make install
```
## 清除和卸载
* `make clean` 在源码目录下清除一些二进制和目标文件
* `make distclean` 清除`confiure`命令生成的文件（源码包变回刚下载的样子）
* `make uninstall `卸载
