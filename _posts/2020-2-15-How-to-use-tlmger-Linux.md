---
layout: article
title: LInux下使用tlmgr
date: 2020-2-15 12:52:00 +0800
img: # Add image post (optional)
fig-caption: # Add fig caption (optional)
tags: [Linux,LaTeX]
---

## 初次使用

在Linux下初次使用TeXLive的包管理器tlmgr时会报错，例如我们用tlmgr安装frame宏包

```bash
$ tlmgr install framed
```

此时会报错

```
(running on Debian, switching to user mode!)
cannot setup TLPDB in /home/shen/texmf at /usr/bin/tlmgr line 5435.
```

此时需要初始化

```bash
$ tlmgr install framed
```

再次尝试安装宏包时继续报错

```
/usr/bin/tlmgr: Initialization failed (in setup_unix_one):
/usr/bin/tlmgr: could not find a usable xzdec.
/usr/bin/tlmgr: Please install xzdec and try again.
Couldn't set up the necessary programs.
Installation of packages is not supported.
Please report to texlive@tug.org.
tlmgr: exiting unsuccessfully (status 1).
```

按照提示安装`xzdec`后继续安装宏包

```bash
$ sudo apt-get install xzdec
$ tlmgr install framed
```

此时没有报错，宏包安装完成

```bash
(running on Debian, switching to user mode!)
tlmgr: package repository http://linorg.usp.br/CTAN/systems/texlive/tlnet
[1/1, ??:??/??:??] install: framed [8k]
tlmgr: package log updated: /home/myusername/texmf/web2c/tlmgr.log
running mktexlsr ...
done running mktexlsr.
```

## 其他命令

* `tlmgr update --self`更新tlmgr自身
* `tlmgr update --all`更新所有宏包
* `tlmgr install xx`安装宏包，xx为宏包名（不带后缀）