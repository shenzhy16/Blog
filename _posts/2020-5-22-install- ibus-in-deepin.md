---
layout: article
title: Linux下使用ibus框架输入法
author: 沈卓洋
date: 2020-5-22 22:36:00 +0800
fig-caption: # Add fig caption (optional)
tags: [Linux,Deepin]
---
前两天因为在Calibre下无法输入中文的问题很头疼（详情[看这里](https://blog.shenzy.cn/2020/05/06/using-Calibre-in-Linux.html)），用了很多办法都无效，于是我尝试放弃Fcitx框架的输入法，试试ibus.
### 安装
安裝IBus框架
```bash
sudo apt-get install ibus ibus-clutter ibus-gtk ibus-gtk3 ibus-qt4
```
安装拼音引擎
```bash
sudo apt-get install ibus-pinyin
```
启用IBus框架
```bash
im-switch -s ibus
```
因为Deepin内置的im-config有点问题，此时可能会报错需要二元表达式，删除冗余文件即可
```bash
sudo rm -f /usr/share/im-config/data/23_ibus.*
```
然后重新尝试启用IBUS框架应该就不报错了

编辑`~/.bashrc`，添加
```bash
export GTK_IM_MODULE=ibus
export XMODIFIERS=@im=ibus
export QT_IM_MODULE=ibus
```
编辑`~/.xinputrc`，把`run_im fcitx`改成`run_im ibus`
### 使用体验
#### 缺点
* 感觉UI有点偏小
* 中文输入状态下输入英文不能直接按shift输入
* 终端中不能默认英文

#### 优点
* 允许编辑自定义词组，舒服了
* 应该不会有因为QT库导致中文无法输入的情况了