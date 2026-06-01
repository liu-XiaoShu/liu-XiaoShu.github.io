---
title: "[Vim + PlatformIO 开发 ESP 专题笔记-1] Ubuntu +Vim + PlatformIO 组成 Arduino 相关的开发环境"
date: 2022-11-21
categories: [嵌入式]
tags:
  - Vim + PlatformIO 开发 ESP 专题笔记
  - Ubuntu
description: 前言 我想在 Ubuntu 下开发 STC/STM/ESP/Arduino 相关的产品应用，但是我又偏爱 Vim 和命令行的组合方式编辑代码而且环境需要用docker，不想使用 Vs Code 或者 Arduino ide要怎么弄
---

#### 前言

我想在 Ubuntu 下开发 STC/STM/ESP/Arduino 相关的产品应用，但是我又偏爱 Vim 和命令行的组合方式编辑代码而且环境需要用docker，不想使用 Vs Code 或者 Arduino ide要怎么弄呢? 找了好久终于发现可以 Vim + PlatformIO 组成 结合组成的开发环境能够满足我的需求，下面就介绍该环境怎么搭建。

###### PlatformIO 介绍

百度百科: [PlatformIO](https://baike.baidu.com/item/PlatformIO/20494484?fr=aladdin)

###### 建议

建议环境在 docker 搭建，这样不影响电脑的环境，毕竟电脑环境比较复杂，不同项目之间最好隔离，故此推荐 docker

##### 安装python环境及vim

该步骤跳过，自行百度

##### 安装 PlatformIO

下载 PlatformIO 的core，执行安装脚本

安装
    
    
    python3 -c "$(curl -fsSL https://raw.githubusercontent.com/platformio/platformio/master/scripts/get-platformio.py)"
    

添加环境变量
    
    
    将 ~/.platformio/penv/bin 添加到路径 PATH 中
    

检查安装是否成功
    
    
    python3 get-platformio.py check core
    

检查配置
    
    
    python3 get-platformio.py check core --dump-state ~/.platformio/Cpioinstaller-state.json
    

最后安装 PlatformIO Core
    
    
    python3 get-platformio.py
    

如果顺利执行到此步， PlatformIO 已经安装完成

##### 创建对应的工程框架

###### 1 先确定使用的开发板，查找板子ID 如下：
    
    
    platformio board
    或者
    platformio boards | grep < your board >
    

###### 2 根据板子ID 创建工程

确定使用的开发板之后，执行下面的指令建立工程
    
    
    mkdir project_dir
    cd project_dir
    pio project init --ide vim --board < your board id >
     
    比如我的板子是 esp8266 falsh 1M 找到id 是 esp01_1m
    mkdir xiaoshu_esp8266_1m_project
    cd xiaoshu_esp8266_1m_project
    
    pio project init --ide vim --board esp01_1m
    

创建工程结构如下
    
    
    ├── include
    │   └── README
    ├── lib
    │   └── README
    ├── Makefile
    ├── platformio.ini
    ├── src
    └── test
        └── README
    
    

1） include: 头文件  
2）lib: 需要用到的库文件或其他库，如 ST7789 库  
3）src: 源代码位置，需要自己在这编辑自己的入口源码  
4）test  
5）platformio.ini:工程的一些基本配置  
下载程序可能需要添加: upload_port = /dev/ttyUSB0

###### 3 编写 Makefile 文件
    
    
    #SHELL := /bin/bash
    #PATH := /usr/local/bin:$(PATH)
    
    all:
        pio -f -c vim run
    
    upload:
        pio -f -c vim run --target upload
    
    clean:
        pio -f -c vim run --target clean
    
    program:
        pio -f -c vim run --target program
    
    uploadfs:
        pio -f -c vim run --target uploadfs
    
    update:
        pio -f -c vim update
    

执行 make 指令构建工程，执行 make upload 下载程序到开发板

##### 编写点亮LED 代码测试框架是否正常

###### 1 在如下结构的 src 目录下 编写mia.c
    
    
    ├── include
    │   └── README
    ├── lib
    │   └── README
    ├── Makefile
    ├── platformio.ini
    ├── src
    └── test
        └── README
    
    

代码示例
    
    
    #include <Arduino.h>                                                                                                                                                       
    #define led 2      // 定义板载led等的控制引脚是2号
    
    /**
     * 伪代码,讲解setup方法和loop方法的调用过程
     *
     * 大概原理
     *  1.Arduino上电初始化进入执行main方法
     *  2.main方法调用setup()方法
     *  3.while(true)死循环，在死循环里面调用loop方法
     *  
     * Arduino伪代码：
     *  
     *  int main(){
     *      ...一些代码...
     *      setup();
     *      ...一些代码...
     *      while(true){
     *          ...一些代码...
     *          loop();
     *          ...一些代码...
     *      }
     *      ...一些代码...
     *  }
     *
     * 在此，我们只需要在setup方法中进行一些必要的初始化（比如：波特率设置，引脚工作模式等
     *      在loop方法中进行业务逻辑处理
     *
     */
    
    //该方法在Arduino上电初始化的时候会自动回调一次，可以进行一些必要的初始化
    void setup(){
        pinMode(led,OUTPUT);    // 设置led的工作模式为输出模式
    }
    
    /** 该方法会被Arduino循环回调 */
    void loop(){
        digitalWrite(led,HIGH);     //设置led灯输出高电平
        delay(1000);                //延时一秒
        digitalWrite(led,LOW);      //设置led灯输出低电平
        delay(1000);                //延时一秒
        digitalWrite(led,HIGH);     //设置led灯输出高电平
        delay(500);                //延时0.5秒
    }
    
    

###### 2 编译

执行 `make` 指令构建工程，执行 `make upload` 下载程序到开发板

##### 如上就是整个搭建过程

---

> 本文由 [CSDN 博客](https://blog.csdn.net/weixin_41596275/article/details/127916566) 迁移至本站。
