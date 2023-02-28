---
title: 用Butterfly主题美化基于Hexo的个人博客
date: 2023-03-01 00:32:47
tags:
---

# 1、前言

根据上篇文章（===用Hexo和github搭建个人博客===）可以获得一个可用但不美观的hexo博客，这篇文章介绍如何用Butterfly主题美化它。
Hexo的主题有很多，据说Next主题不错但是加载比较慢，这里就选择了这个轻量化的主题。

# 2、安装Butterfly

安装方法可以参考它的[官网](https://github.com/jerryc127/hexo-theme-butterfly)

进入到hexo项目目录,克隆github上的butterfly项目到themes目录下

```shell
git clone -b master https://github.com/jerryc127/hexo-theme-butterfly.git themes/butterfly
# 删除themes/butterfly目录里面的.git目录，我不想提交子仓库，暂时直接删掉
```
修改hexo的配置文件_config.yml，将主题改成butterfly

```yml
theme: butterfly
```

重新启动项目

```shell
hexo clean
hexo g
hexo s
# 本地查看
```
如果出现报错extends includes/layout.pug block content include ./includes/mixins/post-ui，尝试安装下面两个包

```shell
npm install hexo-renderer-pug hexo-renderer-stylus
# 重新启动项目
hexo clean
hexo g
hexo s
# 本地查看
```



