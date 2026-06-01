---
title: "[bluetooth使用笔记一]ubuntu下python3 操作蓝牙"
date: 2022-02-13
categories: [Python]
tags:
  - bluetooth使用笔记一
  - Ubuntu
description: PC 端 ubuntu 下如何利用python 操控蓝牙 1 首先确定 PC 端有蓝牙设备/或者已插入USB蓝牙适配器 2 安装 bluetooth python库 sudo apt-get install
---

### PC 端 ubuntu 下如何利用python 操控蓝牙

##### 1 首先确定 PC 端有蓝牙设备/或者已插入USB蓝牙适配器

##### 2 安装 bluetooth python库
    
    
    sudo apt-get install  libglib2.0-dev
    
    pip3  install pybluez
    

###### 3 测试库是否安装正确
    
    
    ipython3
    In [1]: import bluetooth
    
    

##### 安装报错
    
    
       compilation terminated.
        error: command 'x86_64-linux-gnu-gcc' failed with exit status 1
    
    

##### 解决方法
    
    
    sudo apt-get install libboost-python-dev libboost-thread-dev libbluetooth-dev libglib2.0-dev
    
    python3 -m pip install pybluez
    

###### 如上操作完成后，所需要的环境搭建完成

### PC 端 ubuntu 下python bluetooth 操控蓝牙应用代码示例

#### 1 扫描附近的蓝牙设备
    
    
    ipython3
    In [1]: import bluetooth
    
    In [2]: devs= bluetooth.discover_devices(duration=8,lookup_names=True, )
    
    In [3]: print(devs)

---

> 本文由 [CSDN 博客](https://blog.csdn.net/weixin_41596275/article/details/122907824) 迁移至本站。
