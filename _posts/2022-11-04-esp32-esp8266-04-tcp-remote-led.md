---
title: "[ESP32/ESP8266专题笔记-4] ESP32开发板-Ｍicropython-TCP 远程通信"
date: 2022-11-04
categories: [嵌入式]
tags:
  - ESP32/ESP8266专题笔记
description: ESP32开发板-Ｍicropython-TCP 远程通信控制LED操作 1 硬件篇，确定连接LED的GPIO口 2 软件篇 （１）ESP32能控制灯亮灭 from machine import
---
> 本文属于 **ESP32 / ESP8266 专题** 系列，记录 Micropython 与嵌入式实践。

## 📌 简介

ESP32开发板-Ｍicropython-TCP 远程通信控制LED操作 1 硬件篇，确定连接LED的GPIO口 2 软件篇 （１）ESP32能控制灯亮灭 from machine import

---

### ESP32开发板-Ｍicropython-TCP 远程通信控制LED操作

## 1 硬件篇，确定连接LED的GPIO口

## 2 软件篇

### （１）ESP32能控制灯亮灭
```python
from machine import Pin # 导入Pin模块
from utime import sleep_ms #导入延时函数

def led(control_cmd):
    LED = Pin(22,Pin.OUT) # 构建 LED 对象，GPIO22 输出
    if control_cmd == "灯亮":
        LED.value(0)  # 点亮LED
    elif control_cmd == "灯灭":
        LED.value(1)  # 熄灭LED
    elif control_cmd == "灯闪":
        for i in range(3):
            LED.value(0)  # 点亮LED
            sleep_ms(300)
            LED.value(1)  # 熄灭LED
            sleep_ms(400)
```

### （２）ESP32能连接局域网(使用内置wifi)
```python
import network

ssid = "wifi名"
passwd = "wifi密码"
def connectWifi(ssid,passwd):
    global wlan
    wlan=network.WLAN(network.STA_IF)         #create a wlan object
    wlan.active(True)                         #Activate the network interface
    wlan.disconnect()                         #Disconnect the last connected WiFi
    wlan.connect(ssid,passwd)                 #connect wifi
    while(wlan.ifconfig()[0]=='0.0.0.0'):
        time.sleep(1)
    return True
```

### （３）创建tcp服务，接收tcp网络消息
```python
def connectWifi(ssid,passwd):
    global wlan
    wlan=network.WLAN(network.STA_IF)         #create a wlan object
    wlan.active(True)                         #Activate the network interface
    wlan.disconnect()                         #Disconnect the last connected WiFi
    wlan.connect(ssid,passwd)                 #connect wifi
    while(wlan.ifconfig()[0]=='0.0.0.0'):
        time.sleep(1)
    return True
try:
    connectWifi(SSID,PASSWORD)
    ip=wlan.ifconfig()[0]                    
    listenSocket = socket.socket()            
    listenSocket.bind((ip,port))  
    listenSocket.listen(1)                
    listenSocket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)    
    print("*"*90)
    print("TCP 服务器开启, IP: %s PORT: %s"%(ip, port))
    print("*"*90)
    while True:
        print("等待消息接收 ...")
        conn,addr = listenSocket.accept()  
        print("连接自 ...",addr)
        while True:
            data = conn.recv(1024)           
            if(len(data) == 0):
                print("接收完成 ......")
                conn.close()                    
                break
            message_content  = data.decode()
            led(message_content)
            print("\033[0;36;32m接收消息内容: [%s]\033[0m"%message_content)
            ret = conn.send(data)#send data
except:
    if(listenSocket):
        listenSocket.close()
    wlan.disconnect()
    wlan.active(False)
                          
```

### （４）创建tcp服务，完整代码
```python
#ESP32上运行相关代码
import network
import socket
import time
from machine import Pin # 导入Pin模块
from utime import sleep_ms #导入延时函数

SSID="WIFI名称"
PASSWORD="WIFI密码"
port=10000
wlan=None
listenSocket=None

def led(control_cmd):
    LED = Pin(22,Pin.OUT) # 构建 LED 对象，GPIO22 输出
    if control_cmd == "灯亮":
        LED.value(0)  # 点亮LED
    elif control_cmd == "灯灭":
        LED.value(1)  # 熄灭LED
    elif control_cmd == "灯闪":
        for i in range(3):
            LED.value(0)  # 点亮LED
            sleep_ms(300)
            LED.value(1)  # 熄灭LED
            sleep_ms(400)

def connectWifi(ssid,passwd):
    global wlan
    wlan=network.WLAN(network.STA_IF)     
    wlan.active(True)              
    wlan.disconnect()               
    wlan.connect(ssid,passwd)         
    while(wlan.ifconfig()[0]=='0.0.0.0'):
        time.sleep(1)
    return True

try:
    connectWifi(SSID,PASSWORD)
    ip=wlan.ifconfig()[0]                   
    listenSocket = socket.socket()            
    listenSocket.bind((ip,port))           
    listenSocket.listen(1)                  
    listenSocket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)   
    print("*"*90)
    print("TCP 服务器开启, IP: %s PORT: %s"%(ip, port))
    print("*"*90)
    while True:
        print("等待消息接收 ...")
        conn,addr = listenSocket.accept()    
        print("连接自 ...",addr)
        while True:
            data = conn.recv(1024)            
            if(len(data) == 0):
                print("接收完成 ......")
                conn.close()               
                break
            message_content  = data.decode()
            led(message_content)
            print("\033[0;36;32m接收消息内容: [%s]\033[0m"%message_content)
            ret = conn.send(data)
except:
    if(listenSocket):
        listenSocket.close()
    wlan.disconnect()
    wlan.active(False)
```

### （５）下载代码至ESP32内
```bash
sudo ampy --port /dev/ttyUSB0 put main.py
```

### （６）PC控制端代码，用于发送控制指令
```python
#!/usr/bin/env python3
# -*- coding:utf-8 -*-
#-----------------------------------------
import socket

class TcpClient:
    def __init__(self, PORT, HOST, BUFSIZ):
        """
            功能:   初始化tcp参数
            参数：  PORT 端口
                    HOST ip地址
                    BUFSIZ buf 大小/字节
            返回值: 无
        """
        self.PORT = PORT
        self.HOST = str(HOST)
        self.BUFSIZ = BUFSIZ
```python
def TcpSend(self, sendMsg):
"""
功能:   发送TCP 指令到指定ip及端口中
参数：  发送命令字符串
返回值: bool: True/False
"""
ADDR = (self.HOST, self.PORT)
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect(ADDR)

s.send(sendMsg.encode())
RX_DATA = s.recv(self.BUFSIZ)
#接收数据编码转换
RX_DATA = RX_DATA.decode()
s.close()
if RX_DATA == sendMsg:
print("\033[0;36;32m[TCP消息服务]: {消息: %s} 发送成功\033[0m"%sendMsg)
return True
else:
print("\033[0;36;31m[TCP消息服务]: {消息: %s} 发送失败\033[0m"%sendMsg)
return False
#s.send('exit'.encode())
```

if __name__=="__main__":
    tcp = TcpClient(10000, "192.168.xxx.xxx", 1024)
    while True:
        send_cmd = input("发送指令:")
        tcp.TcpSend(send_cmd)
```

### （７）移动控制端代码，用于发送控制指令(网络调试助手)

![手机网络调试助手界面](/assets/img/posts/esp32-esp8266-04-tcp-remote-led/img-01.jpeg)
---

> 📎 **说明**：本文由 [CSDN「小树笔记」](https://blog.csdn.net/weixin_41596275/article/details/122926164) 迁移至 GitHub Pages，并在本站持续维护。
