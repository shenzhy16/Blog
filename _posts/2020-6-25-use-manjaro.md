---
layout: article
title: Manjaro安装与使用体验
author: 沈卓洋
date: 2020-6-25 23:22:00 +0800
fig-caption: # Add fig caption (optional)
tags: [Linux,Manajro]
---

因为一些Deepin下难以忍受的缺点，包括但不限于
* debian系的包过于陈旧
* 自定义程度过低，例如不支持顶栏，仅有的半官方包亦过于简陋、经常崩溃
* 15.11版本已不再维护，旧bug不会被修复；而我又不喜欢V20的拟物风，且V20必定有更多bug

加之新硬盘到了，索性重新安装了，这次我选择了Manjaro+KDE
### 安装
依照官网帮助文档即可
* [烧录镜像](https://wiki.manjaro.org/index.php?title=Burn_an_ISO_File)
* [安装](https://wiki.manjaro.org/index.php?title=Installation_Guides)

具体安装流程和基本设置此处不赘述，需要注意的是，将系统装在SSD较HDD能明显感觉开机速度提升，其余分区并不会自动挂载，自动挂载分区只需修改`/etc/fatab`即可，挂载`/home`分区时注意备份隐藏文件，系统设置都在里面

### 添加国内源
#### 添加国内镜像
```bash
$ sudo pacman-mirrors -i -c China -m rank
```
选择速度最快的或全选都可；
#### 添加ArchLinuxCN
在`/etc/pacman.conf`后面添加
```bash
[archlinuxcn]
SigLevel = Optional TrustedOnly
#中科大源
Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch
#清华源
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch
```

然后更新源列表

```bash
$ pacman-mirrors -g
```
更新pacman数据库并跟新系统
```bash
$ sudo pacman -Syyu
```
安装`archlinuxcn-keyring`
```bash
$ sudo pacman -S archlinuxcn-keyring
```

### 添加和安装字体
如果有一个安装了Windows的分区, 可以通过链接它们来使用其字体，例如 Windows的C:\盘被挂载在/windows:
```bash
$ sudo ln -s /windows/Windows/Fonts /usr/share/fonts/WindowsFonts
```
然后重新生成字体缓存：
```bash
$ sudo fc-cache -f
```
或者将Windows的字体复制到/usr/share/fonts:
```bash
$ sudo mkdir /usr/share/fonts/WindowsFonts
$ sudo cp /windows/Windows/Fonts/* /usr/share/fonts/WindowsFonts
$ sudo chmod 755 /usr/share/fonts/WindowsFonts/*
```
同样重新生成字体缓存
```bash
$ sudo fc-cache -f
```
查看安装的中文字体检查是否安装完成
```bash
$ fc-list :lang=zh
```
安装完成后可能出现字体发虚的情况，可以参考[这里](https://wiki.archlinux.org/index.php/Font_Configuration_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)/Chinese_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#Windows%C2%AE%E6%98%BE%E7%A4%BA%E6%95%88%E6%9E%9C%E7%9A%84%E5%AD%97%E4%BD%93%E5%8F%82%E8%80%83%E9%85%8D%E7%BD%AE)

### 安装常见软件
这里仅叙述安装过程中可能会遇到问题的软件
#### 输入法
这里不使用搜狗拼音输入法，而是安装sun，原因是搜狗安装过程中会有包冲突，似乎是其使用QT4库导致不兼容的问题
```bash
$ sudo pacman -S fcitx-im fcitx-configtool fcitx-sunpinyin fcitx-cloudpinyin
```
然后创建`~/.xprofile`文件并添加
```bash
sudo vi ~/.xprofile
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS="@im=fcitx"
```
#### 网易云音乐
```bash
$ sudo pacman -S electron-netease-cloud-music
```
这个包UI和官方的不一样，习惯上可能会不适应，实测另一个包`netease-cloud-music`不能输入中文，似乎也是QT库的问题
#### Github Desktop
```bash
$ sudo pacman -S github-desktop-bin
```
这个包有bug，即点击图标无法启动，原因是启动的二进制文件路径写错了（或者可以认为是文件的路径放错了），解决方式是修改`/usr/bin/github-desktop`，把`/opt/github-desktop/github-desktop`改为/`opt/github-desktop/github-desktop/github-desktop`，史诗级三次套娃
#### WPS
```bash
$ sudo pacman -S wps-office-cn
```
这样安装的WPS依旧是英文的，需要额外安装中文语言包
```bash
$ sudo pacman -S wps-office-mui-zh-cn
```
#### 其他软件
* vim
* calibre
* anki
* viusal-studio-code
* goldendict
* texlive-most、texlive-lang
* texstudio
* typeta

#### 其他问题
安装软件后可能出现没有图标的问题，右键任务栏开始图标/编辑应用程序/编辑/恢复到系统菜单 即可
### 美化
由于不同人对美的看法见仁见智，KDE内关于主题和设置项也提供了方便的UI，故此处不赘述主题和设置的美化过程；
#### 启动引导
可以在[grub引导主题网站](https://www.gnome-look.org/browse/cat/109/)下载grub主题并安装；不同的grub主题路径：
* `/boot/grub/theme`
* `/usr/share/grub/theme` 

安装到boot意味着安装到第一个路径下.
启动引导的配置文件路径是`/boot/grub/grub.cfg`，对其进行修改以隐藏不想要的引导选项，编辑前最好备份一下；也可使用Grub customizer进行编辑，它提供了方便的UI界面.使用如下命令行可以生成新的配置文件
```bash
$ sudo grub:grub-mkconfig -o /boot/grub/grub.cfg
```
### 使用体验

#### 优点

* 自定义程度高，主题较多
* 包新，且支持AUR
* 自带KDE Connetc跨设备协作
* manjaro的[wiki](wiki.manjaro.org/index.php/Main_Page)相对完善，arch的[wiki](https://wiki.archlinux.org/index.php/Main_page)也有相当大的参考价值，许多问题都能在这里找到解决方案

#### 缺点和尚存的问题

* 有概率滚挂
* KDE环境资源占用较大，有时候会卡顿
* 连接KDE Connect并没有连上蓝牙
* 似乎没有手机电脑同时使用蓝牙耳机的解决方案

总的来说还是相当不错的，没有Deepin开箱即用的省心，但换来的配置完后的舒心让我有满足感