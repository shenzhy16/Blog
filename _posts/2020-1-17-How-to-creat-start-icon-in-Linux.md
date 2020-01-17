---
layout: article
title: Deepin如何为应用程序创建开始菜单
date: 2020-1-17 13:37:00 +0800
img: # Add image post (optional)
fig-caption: # Add fig caption (optional)
tags: [Linux,Deepin]
---

以管理员权限打开 `/usr/share/application`，新建`*.desktop`文件（文件名随意），添加以下代码

```
[Desktop Entry]
Type=Application
Name= //应用程序名称
Comment= //鼠标经过上面时的提示名称
Type= //常见的"Type"数值是"Application"和"Link"。Application表示指向一个应用程序，而Link表示指向一个URL
Exec= //只有在"Type"类型是"Application"时才有意义,表示应用程序的可执行文件路径
URL= //只有在"Type"类型是"Link"时才有意义,定义了该Desktop Entry文件指向的URL
Icon= //应用程序图标的路径
Terminal=false//是否显示终端
StartupNotify=false
Categories=Office//所属分类
```

保存后更新数据库`update-desktop-database`
