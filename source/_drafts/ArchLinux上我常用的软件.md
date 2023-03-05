---
title: ArchLinux上我常用的软件
tags:
---

## clash

这软件嘛，是用来翻越网络屏障的，windows,linux,android，mac都能免费用,iphone要美版帐号付费购买。在github上有[项目](https://github.com/Dreamacro/clash/releases)，根据cpu不同下载.tar.gz后缀的压缩包，intel下载clash-linux-386....,AMD下载clash-linux-amd64...,然后解压:

```shell
# 解压到当前目录了
gzip clash-linux-386-v1.13.0.gz -d

```



## Pycharm

### 安装
pycharm可以用社区版，免费的。

去官网下载社区版，解压到你的目标目录,看起来像是用java开发的软件。

启动只需要执行

```
pycharm目录/bin/pycharm.sh

```

### 出现的问题

#### 切换不了中文输入法的问题

输入法和补全发生按键冲突，都是ctrl + space,修改一下， 补全修改为ctrl + ,

settings -> keymap -> Main Menu -> code -> code completion -> Basic

这样修改完还是不行，ctrl + space 不会发生任何事情

编辑 pycharm目录/bin/pycharm.sh文件，在启动（exec)之前，也是Run The IDE之前添加环境变量，也就是加上三行.

ibus类型的输入法：

```
export GTK_IM_MODULE=ibus
export QT_IM_MODULE=ibus
export XMODIFIERS=@im=ibus
```

fcitx类型的输入法：

```
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS=@im=fcitx
```







