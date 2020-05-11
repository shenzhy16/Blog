---
layout: article
title: Linux下刷新桌面环境
date: 2020-5-11 22:49:00 +0800
fig-caption: # Add fig caption (optional)
tags: [Linux]
---

我使用的是Deepin，~~美观是美观~~（好看就完事儿了！），但有时候不太稳定，偶尔会出现以下几种情况

* 文件夹图标显示不正确
* Dock栏只剩下高斯模糊
* 桌面假死

这是终端一般都是可用的，因此在终端中刷新一下桌面环境可以解决绝大部分的问题

```bash
sudo systemctl refresh lightdm
```

注意，刷新桌面环境后，原本的进程都会结束，因此需要提前保存好文件.