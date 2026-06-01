---
title: "[python专题笔记]pip 问题"
date: 2022-10-12
categories: [Python]
tags:
  - python专题笔记
description: 一 pip3 如下报错 问题现象 Traceback (most recent call last): File '/usr/local/bin/pip3', line 7, in <module> from pip. inte
---
> 本文属于 **Python 专题** 系列，记录学习与工作中的实践笔记。

## 📌 简介

一 pip3 如下报错 问题现象 Traceback (most recent call last): File '/usr/local/bin/pip3', line 7, in <module> from pip. inte

---

一 pip3 如下报错

## 问题现象
```python
Traceback (most recent call last):
  File "/usr/local/bin/pip3", line 7, in <module>
    from pip._internal.cli.main import main
  File "/usr/local/lib/python3.5/dist-packages/pip/_internal/cli/main.py", line 57
    sys.stderr.write(f"ERROR: {exc}")
                                   ^
SyntaxError: invalid syntax
```

## 解决方法
```bash
| curl -fsSL -o- https://bootstrap.pypa.io/pip/3.5/get-pip.py | python3.5 |
| --- | --- |

```

二 pip3 报错如下

## 问题现象
```python
Using cached https://pypi.tuna.tsinghua.edu.cn/packages/00/9e/92de7e1217ccc3d5f352ba21e52398372525765b2e0c4530e6eb2ba9282a/cffi-1.15.0.tar.gz (484 kB)
Requirement already satisfied: pycparser in /home/spd_vspfeed/.local/lib/python3.5/site-packages (from cffi>=1.0->soundfile) (2.21)
Building wheels for collected packages: cffi
Building wheel for cffi (setup.py) ... error
ERROR: Command errored out with exit status 1:
command: /usr/bin/python3 -u -c 'import sys, setuptools, tokenize; sys.argv[0] = '"'"'/tmp/pip-install-i6djzrkb/cffi_3fba870a8d544416aee0a8ac798ff394/setup.py'"'"'; __file__='"'"'/tmp/pip-install-i6djzrkb/cffi_3fba870a8d544416aee0a8ac798ff394/setup.py'"'"';f=getattr(tokenize, '"'"'open'"'"', open)(__file__);code=f.read().replace('"'"'\r\n'"'"', '"'"'\n'"'"');f.close();exec(compile(code, __file__, '"'"'exec'"'"'))' bdist_wheel -d /tmp/pip-wheel-d5e8dtc6
cwd: /tmp/pip-install-i6djzrkb/cffi_3fba870a8d544416aee0a8ac798ff394/
Complete output (54 lines):
Package libffi was not found in 
```

    

## 解决方法
```bash
sudo apt-get install build-essential libssl-dev libffi-dev python-dev
```

三 pip 报错如下
```python
pip3 install esptool      
Traceback (most recent call last):
  File "/usr/bin/pip3", line 11, in <module>
    sys.exit(main())
  File "/home/spd-pub/.local/lib/python3.5/site-packages/pip/__init__.py", line 11, in main
    from pip._internal.utils.entrypoints import _wrapper
  File "/home/spd-pub/.local/lib/python3.5/site-packages/pip/_internal/utils/entrypoints.py", line 12
    f"pip{sys.version_info.major}",
                                 ^
SyntaxError: invalid syntax
```

## 解决方法
```bash
wget https://bootstrap.pypa.io/pip/3.5/get-pip.py
python3 get-pip.py
```
---

> 📎 **说明**：本文由 [CSDN「小树笔记」](https://blog.csdn.net/weixin_41596275/article/details/125066029) 迁移至 GitHub Pages，并在本站持续维护。
