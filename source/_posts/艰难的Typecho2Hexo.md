---
title: 艰难的Typecho2Hexo
math: false
date: 2022-02-06 22:13:14
categories: 折腾
tags:
- linux
- hexo
---

闲来无事，打算把以前在 typecho 的文章，迁移这个博客上面来。于是就有了以下痛苦~~无聊~~的过程，现将其记录于此。
<!--more-->

## Install Typecho
由于之前备份了 typecho 的数据库，所以装一个上去还原就是了。  
于是直接用 qemu 起了一个 ubuntu18.04 server。为了节省时间，lnmp用的是[lnmp一键安装包](https://lnmp.org/)。之后直接进phpmyadmin还原就完事了。  

> note: 因为是本地安装，所以 lnmp vhost add 随便写个域名，之后去 /usr/local/nginx 自行修改server，port地址就行了

## Export posts
尝试了网上的很多脚本导出文章，要不就是过时了，要不就是我的环境有问题...都没能成功  
最后发现了一个叫 Typecho-Plugin-Tp2MD 的插件。导出后 hexo g 全是报错...主要是 front 有问题。找猫画虎修改了一下便可以用了。hexo 可以看这个[版本](https://github.com/ricofx47/Typecho-Plugin-Tp2MD)，~~亲测可用（）~~  

## 番外
有几篇文章始终报错，提示
```bash
expected variable end
```
网上搜索了一下，才知道不能连用两个{% raw %} {{ {% endraw %} ![](https://cdn.jsdelivr.net/gh/microlong666/tieba_mobile_emotions/image_emoticon27.png)，解决方法可以看这个[issue](https://github.com/hexojs/hexo/issues/3224)  

最后推荐一下我的这篇[文章](https://ricofx47.cc/2020/08/29/LOJ2958「COCI_2009.10」ALADIN/)，希望你可以看看（（
