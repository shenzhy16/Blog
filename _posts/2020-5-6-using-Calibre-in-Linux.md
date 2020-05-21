---
layout: article
title: Linux下使用Calibre
author: 沈卓洋
date: 2020-5-6 17:15:00 +0800
img: # Add image post (optional)
fig-caption: # Add fig caption (optional)
tags: [Linux]
---

毕业论文差不多写完了，这两天空下来整理电脑里的教材和文献，原本打算都放到Mendeley里，但是考虑到教材大多是中文（Mendeley对中文支持不好），并且有大量以前攒下来的、不是自己方向的教材，颇有些污染库存的感觉，所以综合考虑下来我还是使用了Calibre.

## 安装

### 从镜像安装
从镜像安装当然是最简单的，传统艺能apt-get就行了

```bash
sudo apt-get install calibre
```

但是我在[官网](https://calibre-ebook.com/download_linux)看到这句话：

> Please do not use your distribution provided calibre package, as those are often buggy/outdated. Instead use the Binary install described below.

意思就是从镜像安装的缺点是版本太旧，建议从官网安装.

### 从官网安装

官网提供了一键安装的命令，系统将直接下载安装最新版

```bash
sudo -v && wget -nv -O- https://download.calibre-ebook.com/linux-installer.sh | sudo sh /dev/stdin
```

当然还可以安装指定版本，例如安装3.48.0
```bash
sudo -v && sudo calibre-uninstall && wget -nv -O- https://download.calibre-ebook.com/linux-installer.sh | sudo sh /dev/stdin version=3.48.0
```
也可以在[这里](https://download.calibre-ebook.com/)下载指定版本，解压运行calibre文件就可以.
## 使用体验
介绍Calibre功能的文章非常多，我不作太多介绍，仅谈我的需求和遇到的问题：
### 中文输入法支持问题
由于我使用的是从官网下载的Calibre，因此对中文输入法支持不好，无法在Calibre内调用fcitx（搜狗）输入法，原因大概是Calibre没有使用系统提供的Qt图形库及附带的插件，而是自带了Qt库，自带的Qt库和fcitx的版本不一致.

其实最简单的解决办法是从镜像源安装，镜像版本没有这个问题，从实际体验看二者几乎没有区别.

如果动手能力强的也可以自己解决，方法就是把系统的Qt库拷贝到 Calibre 对应目录，具体解决方法参阅:
* https://luyangp.github.io/fcitx-for-qt/
* https://www.douban.com/note/658979731/

### 不能创建多级标签
由于不能创建多级标签，一旦书籍涉及的学科较广，分类将变得相当麻烦，自带的标签可以缓解这个问题，但是又不能创建多级标签，因此实际使用体验依然糟糕.解决办法有：
* 增加一个含有固定字段值的栏目，用来填入分类的大类；
* 创建虚拟书库；
* 侧边栏创建用户分类.

这三个办法都是创建一个大类，将一部分标签聚集在一起，因此使用体验几乎一样，选择其一即可.例如我创建了用户分类，效果如下：

  ![img](/assets/images/2020-5-6/sort.jpg)

### 导出的Bib文件格式问题

如果电子书需要作为参考，Calibre支持导出Bib文件，方法是使用Calibre编制书目，选择BiB格式导出.但是导出的格式与我往常使用的、使用Biber编译通过的格式不同.

解决方法是首选项-添加栏目-创建自定义栏目，然后添加一个文本类型的栏目即可

###  格式转换反色
PDF转换成其他格式时会反色，似乎暂时并没有什么办法.

之前的一个思路是转换成EPUB格式，然后再转换成PDF格式，这样就获得了反色的PDF，然后再转换成EPUB，理论上就变成了正常的颜色.事实上转换来的PDF确实变成反色了，但最后的EPUB无法变正常.

这样不仅没得到正常的EPUB，连PDF都搭进去回不来了，淦！
