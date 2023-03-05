---
title: 用Hexo和github搭建个人博客
date: 2023-02-28 15:57:28
tags:
---

# 1、前言

这篇文章记录了使用windows系统和linux系统（archlinux）来搭建Hexo博客的过程，考虑到可能在单位和家里都有写作的需求，这里也记录了使用多台电脑写作的方法。

# 2、安装

第一台电脑为“初始电脑”，也是创建项目的电脑，之后的电脑为“其他电脑”，为了防止下文让人疑惑，这里提前说明。


## 2.1、初始电脑的安装配置

### 2.1.1、安装必要软件

hexo是基于node.js的，所以我们需要用到npm（node package manager)----node.js的包管理器来安装hexo，因为node.js安装包自带了npm，所以我们需要安装node.js和hexo。
因为我们要上传到github，所以需要安装git。

#### 2.1.1.1、 windows系统

##### node.js

[官网](https://nodejs.org/zh-cn/)下载安装即可

安装完成后，打开cmd终端，测试是否安装成功

```cmd
# 显示版本号就对了，我这里重启电脑一次才可以
node -v
npm -v
```

##### hexo

```cmd
npm install hexo-cli -g
```

查看是否下载成功
```cmd
hexo -v
```

##### git

官网下载安装，可自行百度

查看是否安装成功

```cmd
git --version
```


#### 2.1.1.2、 archlinux系统的安装

因为我使用这个系统，就写在这里了，大多数人用windows系统，参考上面的2.2.1.1就可以了，此处可以略过。

安装nodejs,npm和git

```shell
sudo pacman -S nodejs npm git
```

通过查看node版本来验证是否安装成功

```shell
node -v
```

通过查看npm版本来验证是否安装成功

```shell
npm -v
```

通过查看git版本,来验证是否安装成功

```shell
git --version
```


安装hexo

```shell
sudo npm install hexo-cli -g
```
通过查看hexo版本，来验证是否安装成功

```shell
hexo -v
```

## 2.2、其他电脑的安装配置

### 2.2.1 安装必要的软件

参考2.1.1


### 2.2.2、部署到本地

```shell
# 远程仓库克隆到本地
git clone 仓库地址
# 安装依赖
npm install
# 等同于hexo generate
# 将source目录下的.md文件编译为html文件放在public目录下
# 会在站点根目录下生成public文件夹
hexo g
# 启动本地服务器，通过http://localhost:4000/访问
hexo s

```


下载下来的目录

```shell
.
..
_config.landscape.yml
_config.yml
.git
.github
.gitignore
package.json
scaffolds
source
themes
```


安装完依赖会多出node_modules目录

```shell
.
..
_config.landscape.yml
_config.yml
.git
.github
.gitignore
node_modules
package.json
scaffolds
source
themes
```

hexo g 之后多了db.json package-lock.json public

```shell
.
..
_config.landscape.yml
_config.yml
db.json
.git
.github
.gitignore
node_modules
package.json
package-lock.json
public
scaffolds
source
themes
```


## 3、写文章并上传

```shell
# 创建新文章，默认在source/_post目录下
hexo new 文章名
# 创建新文章的草稿，默认在source/_drafts目录下
hexo new draft 文章名
# 清除缓存，也就是删掉public目录
# 只在一台电脑上应该不需要这样
hexo clean
# 生成 等价于hexo generate
hexo g
# 本地查看 等价于 hexo server
hexo s
# 上传github
# 等价于hexo deploy
# 第一次需要浏览器验证，或者token验证，我选择浏览器验证
# hexo d会生成页面并推送到main分支，这个是根据配置
hexo d

# 上传源文件，第一次要
# --set-upstream 等价于 -u，关联远程的分支
git push --set-upstream origin hexo
#以后只用git push就可以了
```

