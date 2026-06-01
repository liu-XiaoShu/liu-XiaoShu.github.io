---
title: "[ESP32/ESP8266专题笔记-8]ubuntu下 编译 ESP32/ESP8266-Ｍicropython 固件"
date: 2022-11-04
categories: [嵌入式]
tags:
  - ESP32/ESP8266专题笔记
  - Ubuntu
description: 前期讲述的都在 Ｍicropython 怎么开发，如没有讲解 Ｍicropython 怎么编译出来，本期就开始讲解一下 编译 ESP8266 的 Ｍicropython 固件 环境介绍: 1\. 操作系统
---

前期讲述的都在 Ｍicropython 怎么开发，如没有讲解 Ｍicropython 怎么编译出来，本期就开始讲解一下

### 编译 ESP8266 的 Ｍicropython 固件

###### 环境介绍:

###### 1\. 操作系统: ubuntu 16.04

###### 2\. 板级: ESP8266

##### 一 搭建 / 安装 esp-open-sdk

###### 参考说明: [参考esp-open-sdk的使用说明](https://link.zhihu.com/?target=https://github.com/pfalcon/esp-open-sdk)

1、克隆esp-open-sdk到本地，并且循环克隆git子项目：
    
    
    git clone https://github.com/pfalcon/esp-open-sdk.git
    cd esp-open-sdk
    git submodule update --init
    

2、安装依赖工具：
    
    
    sudo apt-get install make unrar-free autoconf automake libtool gcc g++ gperf flex bison texinfo gawk ncurses-dev libexpat-dev python-dev python python-serial sed git unzip bash help2man wget bzip2
    

如果提示错误E: Unable to locate package XXX，只需要把XXX单独安装一下即可：
    
    
    sudo apt-get install XXX
    

之后需要把系统的python链接到python2：
    
    
    cd /usr/bin
    sudo ln -sf python2 python
    #如果你的系统python默认是python3，第3步make之后再改回即可：
    cd /usr/bin
    sudo ln -sf python3 python
    

安装python-serial（其实是pyserial）通常会出错，需要执行以下命令：
    
    
    cd esp-open-sdk
    wget "https://bootstrap.pypa.io/pip/2.7/get-pip.py"
    sudo python2 get-pip.py
    pip install pyserial
    

Ubuntu 14.04以后的版本安装完上述软件之后，可能需要安装libtool-bin：
    
    
    sudo apt-get install libtool-bin
    

3、执行make命令：
    
    
    cd esp-open-sdk
    make
    

需要注意的是：  
如果提示的是makeinfo，则把texifo重新安装一下即可，因为makeinfo包含在了texinfo里，单独安装makeinfo会失败：
    
    
    sudo apt-get install texifo
    

如果提示 could not find bash >= 3.1，要求bash版本大于3.1，需修改esp-open-sdk/crosstool-NG/configure.ac的193行，看到|`$EGREP '^GNU bash, version (3\.[1-9]|4)')`，它限制bash版本为3或4，修改为`|$EGREP '^GNU bash, version ([1-9.]+)')`，即取消bash版本限制：
    
    
    sudo vim esp-open-sdk/crosstool-NG/configure.ac     #修改bash版本要求
    

如果提示`error: could not find curses header, required for the kconfig frontends`，则安装libncurses5-dev：
    
    
     
    sudo apt-get install libncurses5-dev
    

如果卡在`Retrieving needed toolchain components' tarballs`，说明需要的源码包有下载失败的。 查看日志，定位下载失败的包：
    
    
    sudo vim crosstool-NG/build.log
    

我遇到的几个下载失败的包，执行对应的下载命令：
    
    
    cd crosstool-NG/.build/tarballs/
    wget "https://github.com/libexpat/libexpat/releases/download/R_2_1_0/expat-2.1.0.tar.gz"
    wget "https://web.archive.org/web/20210701145459/http://isl.gforge.inria.fr/isl-0.14.tar.gz"
    wget "ftp://sourceware.org/pub/newlib/newlib-2.0.0.tar.gz"
    

安装完整之后的crosstool-NG/.build/tarballs/里源码包列表：
    
    
    binutils-2.25.1.tar.bz2  gcc-4.8.5.tar.bz2  isl-0.14.tar.gz    ncurses-6.0.tar.gz
    cloog-0.18.4.tar.gz      gdb-7.10.tar.xz    mpc-1.0.3.tar.gz   newlib-2.0.0.tar.gz
    expat-2.1.0.tar.gz       gmp-6.0.0a.tar.xz  mpfr-3.1.3.tar.xz  xtensa_lx106.tar
    

这源码包都可以手动下载了放到文件夹里。

命令make -jX中的X表示核心数，如果是8核CPU，则改为make -j8可大大提高编译速度。但是要注意，多核心之后，任务不是顺序来的，很有可能核1编译好久，结果因为需要核2的编译结果，核2失败了，核1也只能失败。

4、把xtensa-lx106-elf添加到用户环境变量：
    
    
    sudo vim ~/.bashrc
    export PATH=/home/toor/esp-open-sdk/xtensa-lx106-elf/bin:$PATH
    

##### 二 编译 ESP8266 的 Ｍicropython 固件

1、克隆micropython到本地，并且循环克隆git子项目：
    
    
    git clone https://github.com/micropython/micropython.git
    cd micropython
    git submodule update --init
    

2、固化自己的代码：

如果要把自己的python模块添加进固件里，我们可以把这个模块放入`micropython/ports/esp8266/modules`，这里要注意，里面的文件不要删，直接添加进去就行。

如果我们要保护自己的源码，可以把自己的项目文件全部添加进`micropython/ports/esp8266/modules`里，然后在同一个目录里面的`_boot.py`文件最后面加上一行:
    
    
    import 项目主程序(即文件名不带后缀)
    

那么，你的代码就编译进去了，开机会自动启动你的项目主程序。

3、执行make命令：
    
    
     make -C ports/esp8266 submodules
    cd port/esp8266
    
    make -j BOARD=GENERIC_1M
    
    或者
    
    
    make -j BOARD=GENERIC
    

##### 参考

[github 官方](https://github.com/micropython/micropython/tree/master/ports/esp8266)  
[知呼](https://zhuanlan.zhihu.com/p/506432934)

注意: 参考知呼的教程会出现一个问题就是，文件系统操作失败

[知呼](https://zhuanlan.zhihu.com/p/506432934)编译出的固件，烧入 esp8266 的芯片内，启动报错
    
    
     
    
    Traceback (most recent call last):
    File "_boot.py", line 13, in <module>
    File "inisetup.py", line 44, in setup
    File "inisetup.py", line 16, in check_bootsec
    File "flashbdev.py", line 13, in readblocks
    MemoryError: memory allocation failed, allocating 16448 bytes
    
     MicroPython v1.19.1-543-gab317a0d6-dirty on 2022-10-27; ESP module with ESP8266
    Type "help()" for more information.
    

解决方法: 编译ESP8266之前前执行
    
    
     make -C ports/esp8266 submodules

---

> 本文由 [CSDN 博客](https://blog.csdn.net/weixin_41596275/article/details/127687021) 迁移至本站。（原 CSDN 标注为转载）
