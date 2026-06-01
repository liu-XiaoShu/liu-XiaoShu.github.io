---
title: "[ESP32/ESP8266专题笔记-1]ubuntu下搭建ESP32/ESP8266-Ｍicropython开发环境"
date: 2022-11-04
categories: [嵌入式]
tags:
  - ESP32/ESP8266专题笔记
  - Ubuntu
description: 工具安装 ubuntu下 ESP8266/ESP32 固件烧入工具 pip3 install esptool 或 pip install esptool 或者源码安装 git clone https://github.com/
---

## 工具安装

### ubuntu下 ESP8266/ESP32 固件烧入工具
    
    
    pip3 install esptool
    或
    pip install esptool
    

或者源码安装
    
    
    git clone https://github.com/espressif/esptool.git
    

#### 固件烧入方法

（１）擦除原始固件
    
    
    esptool.py --port /dev/ttyUSB0 erase_flash  
    

（２）烧入新的固件
    
    
     esptool.py --port /dev/ttyUSB0 --baud 115200 write_flash --flash_size=detect 0 xx.bin
    

### ubuntu Ｍicropython编辑工具

vim 安装
    
    
    sudo apt install vim
    

###### 参考: https://blog.csdn.net/espressif/article/details/105028809

### ESP32/ESP8266-Ｍicropython 调试工具

### (1) 命令行的串口(终端)调试工具

picocom 是基于命令行的串口(终端)调试工具

picocom 安装
    
    
    sudo apt-get install picocom
    

picocom 使用说明
    
    
    sudo picocom -b 115200 /dev/ttyUSB0
    
    
    
    -b 是指定波特率 boundrate 为 115200
    /dev/ttyUSB0 就是端口号,需要替换为你自己的端口号
    

### (2) 串口调试工具

minicom 是串口(终端)工具

minicom 安装
    
    
    sudo apt-get install minicom 
    

minicom 使用说明
    
    
    sudo minicom -b 115200 -D /dev/ttyUSB0
    
    
    
    -b 是指定波特率 boundrate 为 115200
    /dev/ttyUSB0 就是端口号,需要替换为你自己的端口号
    

### ubuntu下命令行文件上传 ESP32/ESP8266工具

ampy 介绍  
ampy是一个简单的命令行工具，用于通过串口连接操作文件并在CircuitPython或MicroPython板上运行代码。使用ampy，您可以将文件从计算机发送到电路板的文件系统，将文件从电路板下载到计算机，甚至可以将Python脚本发送到电路板上执行。

ampy 安装
    
    
    pip install adafruit-ampy
    或者
    pip3  install adafruit-ampy
    

ampy 使用说明

向ESP32/ESP8266上传文件
    
    
    ampy --port /dev/ttyUSB0 put test.txt
    

向ESP32/ESP8266上传Ｍicropython文件，并且开机自动运行
    
    
    ampy --port /dev/ttyUSB0 put main.py
    

断电重启或按下复位，脚本自动运行

#### 高级一: 自主编译Ｍicropython固件

参考: [自主编译固件教程](https://zhuanlan.zhihu.com/p/506432934)

这个教程有个BUG 编译的固件会显示 内存分区失败，记得编译固件之前操作
    
    
     make -C ports/esp8266 submodules
     或者
      make -C ports/esp32 submodules

---

> 本文由 [CSDN 博客](https://blog.csdn.net/weixin_41596275/article/details/121381379) 迁移至本站。
