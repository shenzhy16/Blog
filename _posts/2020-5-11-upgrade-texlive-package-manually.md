---
layout: article
title: Linux下手动更新TexLive宏包
date: 2020-5-11 22:17:00 +0800
fig-caption: # Add fig caption (optional)
tags: [Linux,LaTeX]
---

在使用参考文献的国标样式宏包gb7714-2015时，编译显示
```
Package xkeyval Error: gb****** ' undefined in families blx@opt@pre'. [\blx@processoptions]
```
查阅[帮助文档](https://github.com/hushidong/biblatex-gb7714-2015)发现是宏包没有更新的缘故.然而
```bash
tlmg upgrade --all
```
却显示所有宏包都已经是最新的.所以只能手动更新宏包.
经过我的寻找，gb7714-2015宏包的目录在
```
/usr/share/texlive/texmf-dist/tex/latex/biblatex-gb7714-2015
```
于是从[仓库](https://github.com/hushidong/biblatex-gb7714-2015)下载项目之后将

* gb7714-2015.bbx
* gb7714-2015.cbx
* gb7714-2015ay.bbx
* gb7714-2015ay.cbx
* gb7714-2015ms.bbx
* gb7714-2015ms.cbx
* gb7714-2015mx.bbx
* gb7714-2015mx.cbx

复制到宏包所在目录替换即可.
即cd到下载下来的biblatex-gb7714-2015-master文件夹，然后
```bash
sudo cp gb7714-2015.bbx gb7714-2015.cbx gb7714-2015ay.bbx gb7714-2015ay.cbx gb7714-2015ms.bbx gb7714-2015ms.cbx gb7714-2015mx.bbx gb7714-2015mx.cbx /usr/share/texlive/texmf-dist/tex/latex/biblatex-gb7714-2015/
```
其他宏包的手动更新方式也是差不多，关键是要找到该宏包的目录