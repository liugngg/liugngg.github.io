---
date: 2025-09-28
tags:
  - 电脑技术
title: 命令行修改网卡IP及DNS
lastmod: 2025-09-28
categories: []
draft: false
---

1. 设置固定IP和DNS
```
@echo off
echo 正在设置ip,子网,请稍候…
rem "本地连接"为网卡名称，可在网络连接中查询，如"本地连接"
netsh interface ip set address name=“本地连接” source=static addr=192.168.1.202 mask=255.255.255.0 gateway=192.168.1.254 gwmetric=1
echo.
echo 正在设置DNS,请稍候…
netsh int ip set dns name=“本地连接” source=static addr=114.114.114.114
netsh int ip add dns name=“本地连接” addr=223.5.5.5 index=2
echo 设置完毕!
ipconfig
exit
```
2. 自动获取IP和DNS
```
@echo off
netsh interface ipv4 set address name=“本地连接” source=dhcp
netsh interface ipv4 set dns name=“本地连接” source=dhcp
echo ----
exit
```
