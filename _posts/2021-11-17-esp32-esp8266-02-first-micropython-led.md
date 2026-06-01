---
title: "[ESP32/ESP8266专题笔记-2]ubuntu下 编写第一个 ESP32开发板-Ｍicropython程序"
date: 2021-11-17
categories: [嵌入式]
tags:
  - ESP32/ESP8266专题笔记
  - Ubuntu
description: ESP32开发板-Ｍicropython程序-亮灯 Ｍicropython 操作ESP32 GPIO 点亮 LED 1 编写代码 前提条件，已知ESP32 GPIO引脚图 , 我这里是GPIO22 连接一个LED from mach
---

### ESP32开发板-Ｍicropython程序-亮灯

Ｍicropython 操作ESP32 GPIO 点亮 LED

1 编写代码

前提条件，已知ESP32 GPIO引脚图 , 我这里是GPIO22 连接一个LED
    
    
    from machine import Pin # 导入Pin模块
    from utime import sleep_ms #导入延时函数
    
    LED = Pin(22,Pin.OUT) # 构建 LED 对象，GPIO22 输出
    
    LED.value(0)  # 点亮LED
    sleep_ms(500)
    LED.value(1)  # 熄灭LED
    sleep_ms(1000)
    
    

2 上传代码前提  
开发版串口正确连接 PC USB，查看开发板内脚本情况
    
    
    Port /dev/ttyUSB0, 19:21:21
    Press CTRL-A Z for help on special keys
    >>> import os
    >>> os.listdir()
    ['boot.py', 'ssd1306.py', 'main.py']
    >>> 
    
    
    

说明: 开发板子中 Ｍicropython 上电自动启动流程,如下图所示:

上电

boot.py

main.py

由上述流程可知道要想代码上电自启动，必须代码文件名为boot.py 或者main.py，但是不建议修改boot.py 其内部可能包好比较关键的代码，比如你之前设置的开机自动连接网络的代码，所以这里我们把点灯代码文件名修改为 main.py, 执行如下烧录命令，把代码拷贝到开发板中
    
    
    ampy --port /dev/ttyUSB0 put main.py

---

> 本文由 [CSDN 博客](https://blog.csdn.net/weixin_41596275/article/details/121385275) 迁移至本站。
