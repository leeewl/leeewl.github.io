---
title: 实体机安装ArchLinux单系统教程2023年版
date: 2023-03-02 21:36:00
tags:
---

这是我在笔记本上安装ArchLinux单系统的过程，跟安装在虚拟机上，有少许不同，所以记录下来，供以后参考。

## 制作启动U盘
下载镜像，使用Rufus软件制作启动盘

电脑开机后，不停按F12,进入boot选项，选择从U盘启动，比如我是
```shell
UEFI:USB Flash Disk ...
```
这个u盘在安装完成后也有作用，ArchLinux是滚动更新，有时会发生滚挂了的情况，这时候可以用u盘引导，解决问题。

## 启动模式检查

```shell
ls /sys/firmware/efi/efivars
```

若目录不存在，则是以BIOS/CSM模式启动，否则是UEFI模式启动，这篇文章的前提是以UEFI模式启动。

## 连无线网
如果电脑网线插口，应该插上就能用，但现在很多笔记本没有了网线接口，所有这里用无线网络接口，先声明一下，代码部分以符号‘#’开头的文字都是注释，注释一般再命令的前面一行，不需要输入。

```shell
# 使用命令iwctl进入iwd模式，也就是终端最前方有“[iwd]#”字样
iwctl
# 查询电脑的网卡,我这里网卡号是wlan0，下面的wlan0需要替换成你的网卡号
device list
# 显示周围的网络名字
station wlan0 scan
station wlan0 get-networks
# 连接网络，如果式加密的wifi，会提示输入密码
station wlan0 connect 网络名字
# 离开iwd模式
quit
# 查看是否联网成功，如果显示ms则成功，如果显示timeout就是失败
ping -c 4 www.baidu.com

```

## 磁盘分区

分区方案

| 分区 | 大小 | 用途|
|---- | ---- | ---- |
| sda1| 200M | boot |
| sda2| 8G | swap |
| sda3| 40G | / |
| sda4| 剩下 | /home |

```shell
# 查看磁盘分区，从这里可以看到磁盘号,一般是/dev/sda 或者 /dev/sdb,我们这里式sda
fdisk -l

#使用伪图形界面进行分区,分区后写入（write) 离开（quit）
cfdisk /dev/sda  

# 格式化和初始化
# 用于/boot目录的分区
mkfs.fat -F32 /dev/sda1
# swap分区
mkswap /dev/sda2
swapon /dev/sda2
# 用于根目录的分区
mkfs.ext4 /dev/sda3
# 用于/home目录的分区
mkfs.ext4 /dev/sda4

#挂载分区到根目录
mount /dev/sda3 /mnt
# 创建efi目录
mkdir -p /mnt/boot/efi
# 挂载分区到/boot
mount /dev/sda1 /mnt/boot
# 创建home目录
mkdir  /mnt/home
# 挂载分区到/home
mount /dev/sda4 /mnt/home
# 分区表导入文件
genfstab -U /mnt >> /mnt/etc/fstab
```

## 添加国内源

国内源会快一点

```shell
vim /etc/pacman.d/mirrorlist

# 注释下面第一行添加
Server = http://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch

```

## 安装系统和基本软件

```shell
pacstrap /mnt base linux linux-firmware base-devel
# 切换到安装好的系统
arch-chroot /mnt

# 安装引导
pacman -S grub efibootmgr  
# 下面两条grub-install是同一个命令，选择一个执行就行
grub-install /dev/sda  
# 指定目录,我在这里遇到找不到efi的提示，所以要指定目录
grub-install --efi-directory=/boot/efi
grub-mkconfig -o /boot/grub/grub.cfg
```

## 设置

```shell
# 配置时区
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

# 设置硬件时间
hwclock --systohc

# 设置语言
# 将/etc/locale.gen中`en_US.UTF-8`和`zh_CN.UTF-8`的注释去掉
vim /etc/locale.gen
# 生成一下
locale-gen
# 在 `/etc/locale.conf`文件中写入`LANG=en_US.UTF-8`
echo LANG=en_US.UTF-8 >> /etc/locale.conf

# 给root创建密码
passwd

# 设置主机名，下面的my_pc_name替换成你需要的名字
echo my_pc_name > /etc/hostname

# 设置hosts

echo 127.0.0.1   localhost >> /etc/hosts
echo ::1         localhost >> /etc/hosts
echo 127.0.0.1   my_pc_name >> /etc/hosts


```

## 安装软件

```shell
# 网络相关
pacman -S iwd dhcpcd dialog wpa_supplicant ntfs-3g networkmanager

# 微码，根据电脑的cpu式Intel和AMD，选择一个安装
pacman -S intel-ucode   #Intel
pacman -S amd-ucode     #AMD

```

## 重启

早点重启，看看吃否成功，成功之后再进行其他配置，这是一个阶段性胜利
```shell
exit  
umount -R /mnt  
# 重启之前拔出U盘，不然又从U盘启动了
reboot
```

## 设置bios
开机按F2,进入bios，添加uefi引导

此处应有图片～～～～～


## 设置网络并联网

```shell
# 打开联网服务和蓝牙
systemctl start iwd.service
systemctl enable iwd.service
systemctl start systemd-resolved.service
systemctl enable systemd-resolved.service

#配置网络连接和DNS,三行文字
echo "[General]\nEnableNetworkConfiguration=true\nNameResolvingService=systemd" >> /etc/iwd/main.conf

# 用文章开篇“连无线网”的方法联网
```

## 创建用户

一般不用root用户操作，因为权限太大了，这里需要创建一个新用户

```shell
# 将新建用户移入wheel组并指定shell为bash(以后可修改),替换下面的new_user_name为要创建的用户名
useradd -m -G wheel -s /bin/bash new_user_name

# 为新用户设置密码
passwd new_user_name

# 为新用户添加管理员权限（可以使用sudo命令）
#将文件/etc/sudoers中的wheel ALL=(ALL) ALL那一行前面的注释去掉
sed -i 's/# %wheel ALL=(ALL:ALL) ALL/%wheel ALL=(ALL:ALL) ALL/g' /etc/sudoers

# 切换用户
su - new_user_name
```

## 结束

至此，一个可以上网的archlinux系统就安装成功了，其他的功能就需要根据个人喜好安装了，比如需要用什么桌面，什么terminal之类的。

