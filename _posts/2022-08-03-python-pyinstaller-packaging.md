---
title: "[python专题笔记]学习/工作遇到的坑记录－pyinstaller 打包"
date: 2022-08-03
categories: [Python]
tags:
  - python专题笔记
description: win下pyinstaller 打包问题 问题一 被打包的代码中有多进程任务，在win下打包出来的可执行文件，执行时候会出现如下报错 no such option: --multiprocessiong fork 解决方法 [参考连
---
> 本文属于 **Python 专题** 系列，记录学习与工作中的实践笔记。

## 📌 简介

win下pyinstaller 打包问题 问题一 被打包的代码中有多进程任务，在win下打包出来的可执行文件，执行时候会出现如下报错 no such option: --multiprocessiong fork 解决方法 [参考连

---

### win下pyinstaller 打包问题

- 问题一 被打包的代码中有多进程任务，在win下打包出来的可执行文件，执行时候会出现如下报错
```bash
no such option: --multiprocessiong fork
```

    

- 解决方法
- [参考连接](https://blog.csdn.net/zyc121561/article/details/82941056)
```python
最近在使用Pyinstaller打包Python程序的时候发现，打包过程正常，但在运行时会出错，表现为进程不断增加至占满电脑CPU死机，程序版本及环境为：
```bash
Windows 10
Python3.6
Pyinstaller 3.4
经过网上的多番搜索查阅发现是因为程序使用了多进程模式，而在windows上Pyinstaller打包多进程程序需要添加特殊指令。
这里是官方github给出的解释：
https://github.com/pyinstaller/pyinstaller/wiki/Recipe-Multiprocessing
```

修改方式比较简单，在 if __name__=='__main__:'下添加一句multiprocessing.freeze_support() 即可。

如下:

if __name__=='__main__':
	# 在此处添加
	multiprocessing.freeze_support()
	# 这里是你的代码
	# ......

如果你的Pyinstaller版本低于3.3版本的话，还需要额外添加一个模块：

import os
import sys

# Module multiprocessing is organized differently in Python 3.4+
try:
    # Python 3.4+
    if sys.platform.startswith('win'):
        import multiprocessing.popen_spawn_win32 as forking
    else:
        import multiprocessing.popen_fork as forking
except ImportError:
    import multiprocessing.forking as forking

if sys.platform.startswith('win'):
    # First define a modified version of Popen.
    class _Popen(forking.Popen):
        def __init__(self, *args, **kw):
            if hasattr(sys, 'frozen'):
                # We have to set original _MEIPASS2 value from sys._MEIPASS
                # to get --onefile mode working.
                os.putenv('_MEIPASS2', sys._MEIPASS)
            try:
                super(_Popen, self).__init__(*args, **kw)
            finally:
                if hasattr(sys, 'frozen'):
                    # On some platforms (e.g. AIX) 'os.unsetenv()' is not
                    # available. In those cases we cannot delete the variable
                    # but only set it to the empty string. The bootloader
                    # can handle this case.
                    if hasattr(os, 'unsetenv'):
                        os.unsetenv('_MEIPASS2')
                    else:
                        os.putenv('_MEIPASS2', '')
```bash
# Second override 'Popen' class with our modified version.
forking.Popen = _Popen
```

把上述内容保存为multiprocess.py ，然后在你的程序中引入该模块即可：

from multiprocess import *

值得提醒一点的是，上述程序打包时提示的错误非常难以定位到多进程处的问题，而是会提示一些别的包或者依赖文件找不到等等。

另外有关Pyinstaller打包常出现的问题可以在我的部分文章中找到一些解决方案：
Pyinstaller基本用法：https://blog.csdn.net/zyc121561/article/details/79563662
Pyinstaller隐式导入出错解决：https://blog.csdn.net/zyc121561/article/details/79562935
```

- Mac 上使用pyinstaller 打包出现如下报错：
```bash
entitlements_file=self.entitlements_file)
File "/usr/local/lib/python3.7/site-packages/PyInstaller/building/utils.py", line 391, in checkCache
osxutils.sign_binary(cachedfile, codesign_identity, entitlements_file)
File "/usr/local/lib/python3.7/site-packages/PyInstaller/utils/osx.py", line 383, in sign_binary
raise SystemError("codesign failure!")
SystemError: codesign failure!
```

    

解决方法
```bash
安装4.3 版本 pyinstaller

pip3 install ptinstaller==4.3
```

- 备注:
```bash
Mac 下对pyinstaller 打包电脑权限要求高，　打包和时候使用sudo

sudo pyinstaller xx
```

- 问题二， 添加资源问题  
- * 打包资源，需要把路径经过如下函数处理，用于资源在启动时候，找到解压存放的绝对路径 /tmp/xxx
```python
def get_resource_path(self, relative_path):
"""
功能:   获取资源的绝对路径，适用于PyInstaller
参数:   relative_path 相对路径
返回值: 绝对路径
"""
if hasattr(sys, '_MEIPASS'):
return os.path.join(sys._MEIPASS, relative_path)
return os.path.join(os.path.abspath("."), relative_path)
```

    

- * pyinstaller 打包资源添加方法
- *       * 打包普通资源 --add-data= 原始路径:．
- *       * 打包可执行文件资源 --add-binary=原始路径:．
---

> 📎 **说明**：本文由 [CSDN「小树笔记」](https://blog.csdn.net/weixin_41596275/article/details/121753883) 迁移至 GitHub Pages，并在本站持续维护。
