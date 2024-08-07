---
title: arch Linux 安装Nvidia 驱动之后 KDE输入密码不能进入桌面只有鼠标指针
date: 2023-07-29 22:43:44
tags: 
categories:
  - - debug
auto_excerpt: "true"
---
## 问题

arch Linux 安装Nvidia 驱动之后 KDE输入密码不能进入桌面只有鼠标指针

## 排查过程

通过回滚和安装，确定是 nvidia 包也就是安装驱动导致的问题

## 解决方案

ssh 进入只有鼠标指针的系统，然后编辑/etc/default/grub ，在GRUB_CMDLINE_LINUX_DEFAULT的字符串加入nvidia_drm.modeset=1参数，保存并运行sudo grub-mkconfig -o /boot/grub/grub.cfg以更新 GRUB 配置，重启计算机使新内核参数生效即可。
别忘了sudo vim /etc/mkinitcpio.conf去掉HOOKS中的kms，然后sudo mkinitcpio -P