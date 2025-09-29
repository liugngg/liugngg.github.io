---
date: 2025-09-28
tags:
  - 电脑技术
title: 利用Win10和Win11自带的包管理工具安装Chrome
lastmod: 2025-09-28
categories: []
draft: false
---

Win10和Win11自带了Winget包管理工具,可以直接下载安装.
打开终端,CMD或者Powershell都行：

1. 带更新组件的:  
`winget install --id=Google.Chrome -e`
2. 不带更新组件的:
`winget install --id=Google.Chrome.EXE -e`

install换成show就是查看信息,里面有安装包的下载地址.
命令中，-e 是一个选项，表示“精确匹配”（exact match）。
使用 -e 选项可以确保 winget 只安装 ID 完全匹配 Google.Chrome 的包，而不是其他可能包含类似名称的包。