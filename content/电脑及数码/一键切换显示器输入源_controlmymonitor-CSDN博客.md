---
title: 一键切换显示器输入源_controlmymonitor-CSDN博客
source: https://blog.csdn.net/weixin_44904331/article/details/135701977
created: 2025-05-21
tags:
  - 电脑技术
lastmod: 2025-09-28
categories: []
draft: false
---

本文链接： [https://blog.csdn.net/weixin\_44904331/article/details/135701977](https://blog.csdn.net/weixin_44904331/article/details/135701977)

---

因工作原因，公司给配了一台新的台式机 🖥️，同时，又不想放弃使用自己的 [笔记本](https://so.csdn.net/so/search?q=%E7%AC%94%E8%AE%B0%E6%9C%AC&spm=1001.2101.3001.7020) ，在只有一台外接显示屏的情况下，达到外接显示随时切换的目的。

最开始，仅通过手动按 [显示器](https://so.csdn.net/so/search?q=%E6%98%BE%E7%A4%BA%E5%99%A8&spm=1001.2101.3001.7020) 按钮方式操控，体验下来，太繁琐！！😓  
无奈，便寻找更快捷的方式，于是，便有了本文。

记录一键修改视屏输入源的方式，做到 Windows <-> Mac 的无缝切换。

## 一、Windows端

> Windows端切换到Mac，借助ControlMyMonitor软件和Python脚本实现

### 1.1 ControlMyMonitor

> ControlMyMonitor工具是开源免费的，只能在Windows上运行。  
> 该工具主要用于更改显示器配置，它能够快速更改亮度、色温、对比度等设置，支持页面或命令行操作，无需通过显示器按钮。

**操作步骤**

1. [点击](http://www.nirsoft.net/panel/controlmymonitor.exe) 下载ControlMyMonitor到本地指定目录  
	![[attachments/cd283580bce2e301274e066c14e5e6f9_MD5.png]]
2. 打开软件，可以看到如下页面  
	![[attachments/5fd65a044559c32eb111aa039da3c19a_MD5.png]]

> 其中，Input Select代表输入选择，Possible Values代表可选项  
> 经过测试发现，17代表DP源（Windows），15代表VGA源（Mac）  
> 此参数依据显示器支持的输入源和已接入的源变动，每台电脑都不一样，所以需要测试确认源的对应值

1. 确认源的对应值后，通过命令行方式，尝试切换输入源：`./controlmymonitor.exe /SetValue Primary 60 15*`
	命令解析：
	- controlmymonitor.exe：及官网下载的软件
	- SetValue：该软件的内置命令，用于设定值
	- Primary：主显示器
	- 60：输入源设定
	- 15：VGA输入口（我这里对应Mac）
	成功切换后，及代表试验成功。否则，换Possible Values值中另一个试试？
2. 既然命令行方式已试验成功，我们试着简化它，不用每次敲命令行。我们可以通过Python脚本实现切换源功能，源码如下：
```python
# cut_source.py
__doc__ = """
    用于一键切换视频输入源
"""

import os
import sys
from ctypes import windll

def cut(source):
    """
    切换显示器输入源
    """
    # 需要修改为本机软件所在路径
    exe_path = "cd /d D:\Program Files\controlmymonitor"
    cmd = f"{exe_path} && controlmymonitor.exe /SetValue Primary 60 {source}*"
    os.system(cmd)

def lock_screen():
    """
    锁屏
    """
    user = windll.LoadLibrary("user32.dll")
    user.LockWorkStation()

if __name__ == "__main__":
    # 1:  MAC > VGA
    # 15: WIN > DP
    try:
        argv = sys.argv[1]
    except Exception as e:
        argv = "15"  # 默认为15，可手动改
    cut(argv)
    lock_screen()
12345678910111213141516171819202122232425262728293031323334353637
```

> 说明：
> 
> ```
> 依赖：Python3
> 
> 运行方式：python cut_source.py [value]
> 
> 可选参数value：切换目的源（我这里默认为15）
> 12345
> ```
> 
> 功能描述：
> 
> ```
> 运行该脚本后，切换指定视频输入源并锁屏
> 1
> ```

1. 运行测试后，发现功能有了，但还是不方便（需要运行python脚本，依赖环境）。

因此，我们把脚本编译为Windows可直接运行的.exe文件。使用 `pyinstaller` 库，终端运行： `pip install pyinstaller`  
安装成功后，cd到脚本所在位置，运行 `pyinstaller -F cut_source.py -w` ，将其编译为.exe文件，目录层级如下：

> 注意事项 ⚠️：
> 
> 默认生成的exe会放在dist目录下，我们只需要cut\_source.exe文件

![[attachments/d5bc01135b92a97d2275aad4f90879bd_MD5.png]]  
生成exe文件后，直接双击就可切换显示器输入源啦。为了更快捷，可将其 `发送快捷方式` 到桌面 或 固定到任务拦。至此，达到了Windows > Mac的目的。

## 二、Mac端

使用 [MAC触发角功能](https://support.apple.com/zh-cn/guide/mac-help/mchlp3000/mac#:~:text=%E5%9C%A8%20Mac%20%E4%B8%8A%E4%BD%BF%E7%94%A8%E8%A7%A6%E5%8F%91%E8%A7%92%201%20%E5%9C%A8%20Mac%20%E4%B8%8A%EF%BC%8C%E9%80%89%E5%8F%96%E8%8B%B9%E6%9E%9C%E8%8F%9C%E5%8D%95%20%3E%E2%80%9C%E7%B3%BB%E7%BB%9F%E8%AE%BE%E7%BD%AE%E2%80%9D%EF%BC%8C%E7%84%B6%E5%90%8E%E7%82%B9%E6%8C%89%E8%BE%B9%E6%A0%8F%E4%B8%AD%E7%9A%84%E2%80%9C%E6%A1%8C%E9%9D%A2%E4%B8%8E%E7%A8%8B%E5%BA%8F%E5%9D%9E%E2%80%9D,%E5%AF%B9%E4%BA%8E%E8%A6%81%E4%BD%BF%E7%94%A8%E7%9A%84%E6%AF%8F%E4%B8%AA%E8%BE%B9%E8%A7%92%EF%BC%8C%E7%82%B9%E6%8C%89%E5%BC%B9%E5%87%BA%E5%BC%8F%E8%8F%9C%E5%8D%95%EF%BC%8C%E7%84%B6%E5%90%8E%E9%80%89%E5%8F%96%E4%B8%80%E4%B8%AA%E9%80%89%E9%A1%B9%EF%BC%8C%E5%A6%82%E2%80%9C%E9%80%9A%E7%9F%A5%E4%B8%AD%E5%BF%83%E2%80%9D%E3%80%81%E2%80%9C%E5%90%AF%E5%8A%A8%E5%8F%B0%E2%80%9D%E6%88%96%E2%80%9C%E9%94%81%E5%AE%9A%E5%B1%8F%E5%B9%95%E2%80%9D%E3%80%82%20%E8%8B%A5%E8%A6%81%E5%B0%86%E4%BF%AE%E9%A5%B0%E9%94%AE%E4%B8%8E%E8%A7%A6%E5%8F%91%E8%A7%92%E9%85%8D%E5%90%88%E4%BD%BF%E7%94%A8%EF%BC%8C%E8%AF%B7%E5%9C%A8%E6%9F%A5%E7%9C%8B%E5%BC%B9%E5%87%BA%E5%BC%8F%E8%8F%9C%E5%8D%95%E6%97%B6%E6%8C%89%E4%BD%8F%20Command%E3%80%81Shift%E3%80%81Option%20%E6%88%96%20Control%20%E9%94%AE%E6%88%96%E8%80%85%E8%BF%99%E4%BA%9B%E9%94%AE%E7%9A%84%E7%BB%84%E5%90%88%E3%80%82%204%20%E7%82%B9%E6%8C%89%E2%80%9C%E5%AE%8C%E6%88%90%E2%80%9D%E3%80%82) ，配置鼠标移到指定位置后将显示器置为 `睡眠状态` ，配合外接显示器 `自动切换输入源` 功能，达到Mac > Windows切换的目的。

---

## 三、附录

### 3.1 pyinstaller参数说明

- \-F：打包后只生成单个exe格式文件
- \-D：默认选项，创建一个目录，包含exe文件以及大量依赖文件
- \-c：默认选项，使用控制台(就是类似cmd的黑框)
- \-w：不使用控制台
- \-p：添加搜索路径，让其找到对应的库
- \-i：改变生成程序的icon图标
