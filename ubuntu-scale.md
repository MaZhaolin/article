---
title: ubuntu18.04设置150%缩放
date: 2019-01-17 13:13:15
tags: linux
---

运行以下命令
``` shell
gsettings set org.gnome.settings-daemon.plugins.xsettings overrides "[{'Gdk/WindowScalingFactor', <2>}]"
xrandr --output DP-2 --scale 1.5x1.5
```
