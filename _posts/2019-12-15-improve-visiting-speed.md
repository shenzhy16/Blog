---
layout: article
title: 一种有效解决GitHub Page访问过慢的方法
date: 2019-12-15 21:03:00 +0800
description: 花了几天时间，总算找到了一种有效解决GitHub Page访问过慢的方法
img: # Add image post (optional)
fig-caption: # Add fig caption (optional)
tags: [数学,知识库]
---

由于我的数学知识库是基于TiddlyWiki的，本质上它只是一个HTML文件，因此利用GitHub Page我很容易就将它做成了一个[静态网页](https://math.shenzy.cn/)，并且将域名自定义为math.shenzy.cn。但是也正是因为知识库的完整内容都在一个HTML文件内，加载网站必须载入整个HTML文件，而GitHub又是境外的代码托管网站，因此知识库网站访问速度极慢。我曾经的解决方法是做了加载动画，但它只能缓解着急的情绪，而并不能解决实质问题

因为技术有限，我不可能改变知识库本身的文件结构，那么解决问题的唯一办法还是将文件放在国内的服务器上。最好的解决方案当然是租服务器，但是这仅是一个小项目，考虑到租服务器会带来的费用和备案等麻烦事，因此租服务器显然是小题大做了。

于是我的下一个考虑的方案是使用免费的对象存储服务。七牛云有免费的存储空间可用，我尝试了一下，发现七牛云访问确实很快，但它给我的知识库文件分配的是临时域名（一串随机字符），30天自动变更一次，要绑定自定义域名需要备案，而备案又需要服务器。因此七牛云这条路似乎也走不通。

我又想到了国内的代码托管服务，它们也许也有类似于GitHub Page的服务。国内的代码托管服务主要有Coding（腾讯云），阿里代码托管，Gitee码云。逐一试用之后发现：

* Coding支持Page服务，支持自定义域名，但访问速度极慢（据说服务器在香港）
* 阿里代码托管 不支持Page服务
* Gitee 支持Page服务，但自定义域名和自动更新需要付费，费用是99一年

这样我的选择似乎只有Gitee码云了，但是它的免费版不支持自定义域名，Gitee分配的域名又相当长，难以记忆。此时我的解决方案是，给我的个人域名解析内添加一条url转发的记录，在阿里云的后台尝试之后我发现，url转发要求转发的url有备案，因此这种方案也不可行。

既然目前只能在GitHub Page上使用自定义域名，那为什么不在GitHub Page上跳转呢？于是我将GitHub Page的index.html替换为

```javascript
<html>
 <head>
  <script language="javascript" type="text/javascript">
    window.location.href = 'https://shenzhy16.gitee.io/mathknowledgebase';
</script>
 </head>
 <body></body>
</html>
```

这样，GitHub Page上仅需加载以上代码，它的作用就是跳转到Gitee Page上，经过测试访问速度极快。并且由于Gitee也是Git代码托管平台，我可以方便的使用Git工具同时更新到两个平台。

但是这样做有一个小缺点，那就是跳转之后地址栏的url变了，虽然它并不影响实际使用，但我还是尝试去解决它。对于一个静态网页，要实现url不变的一个办法是使用iframe框架，于是代码变成了

```javascript
<html>
 <head></head>
 <body>
  <iframe src="https://shenzhy16.gitee.io/mathknowledgebase" frameborder="0" scrolling="yes" height="100%" width="100%"></iframe>  
 </body>
</html>
```

但是这样一来又有如下问题：

* 框架外面有白边
* 移动端显示的是被缩小后的整个页面，相当难用

于是我分别用了

```javascript
<body style="margin:0;padding:0;overflow:hidden" scroll="no">
```

和

```javascript
<meta name="viewport" content="width=device-width,minimum-scale=1.0,maximum-scale=1.0,user-scalable=no">
```

来解决上述问题，这样GitHub Page上的index.html就变成了

```javascript
<html>
 <head></head>
 <body style="margin:0;padding:0;overflow:hidden" scroll="no">  
  <iframe src="https://shenzhy16.gitee.io/mathknowledgebase" frameborder="0" scrolling="yes" height="100%" width="100%"></iframe>
  <meta name="viewport" content="width=device-width,minimum-scale=1.0,maximum-scale=1.0,user-scalable=no" />
 </body>
</html>
```

当然，用frame框架也可以达到相同的效果，并且框架外本来就没有白边，但是因为HTML5不支持frame框架，我也就没有用它。如果用frame框架实现，代码如下：

```javascript
<html>
 <head>
  <meta name="viewport" content="width=device-width,minimum-scale=1.0,maximum-scale=1.0,user-scalable=no" />
 </head>
 <frameset cols="100%">
  <frame src="https://shenzhy16.gitee.io/mathknowledgebase" />
 </frameset>
</html>
```

至此，我已经实现了我想要的所有功能，目前来说仅有一个缺点，那就是Gitee Page的免费版无法自动更新，每次Push之后需要手动更新。当然，这仅是稍麻烦了一些而已，并不重要。
