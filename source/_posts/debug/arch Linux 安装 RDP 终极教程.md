---
title: arch Linux 安装 RDP 终极教程
date: 2023-07-30 12:15:54
tags: 
categories:
  - - debug
auto_excerpt: "true"
---

按照Arch 的RDP 官方文档配置 RDP 会黑屏，以下是标准流程。

环境：Arch linux+KDE

RDP 分为前端和后端，需要分别安装。

前端使用yay -S xrdp即可，后端根据显卡情况选择性安装，值得注意的事是不使用 GPU 的话CPU 的占用率会非常高：
1. yay -S xorgxrdp-glamor （英特尔与 AMD 核显）
2. yay -S xorgxrdp （纯 CPU）
3. yay -S xorgxrdp-nvidia （英伟达独显）

下一步sudo vim /etc/X11/Xwrapper.config，写入一行allowed_users=anybody并保存。

编辑sudo vim ~/.xinitrc，写入一行/usr/lib/plasma-dbus-run-session-if-needed startplasma-x11并保存，如果没有~/.xinitrc文件创建一个无妨。

配置成系统服务以开机自启：
sudo systemctl enable xrdp.service
sudo systemctl enable xrdp-sesman.service

重启系统：
sudo reboot

使用RDP默认端口3389即可访问。

最后，不推荐CPU 性能非常弱的情况下使用RDP。