---
title: "[树梅派专题笔记]树莓派安装oh-my-zsh及问题解决"
date: 2022-09-13
categories: [嵌入式]
tags:
  - 树梅派专题笔记
description: 安装 oh-my-zsh 1\. 更新软件源索引 sudo apt-get update 2\. 安装zsh sudo apt-get install zsh 或者 sudo apt install z
---

#### 安装 oh-my-zsh

###### 1\. 更新软件源索引
    
    
    sudo apt-get update
    

###### 2\. 安装zsh
    
    
    sudo apt-get install zsh
    或者
    sudo apt install zsh
    

###### 3\. 查看一下系统对各Shell的支持情况
    
    
    cat /etc/shells
    

###### 4\. 下载oh-my-zsh自动配置脚本
    
    
     wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O  oh-my-zsh.sh
    

###### 5\. 执行oh-my-zsh自动配置脚本
    
    
    chmo +x oh-my-zsh.sh
    ./oh-my-zsh.sh
    

###### 6\. 执行oh-my-zsh自动配置脚本
    
    
    chsh -s /bin/zsh
    

###### 7\. 配置主题完成安装
    
    
    vim ~/.zshrc
    
    我选择的主题是ys
    ZSH_THEME="ys"
    
    source ~/.zshrc
    

#### 使用 oh-my-zsh出现的问题

###### 问题一: 安装oh-my-zsh，在zsh 执行已安装的命令有些提示没有该命令
    
    
    ifconfig
    zsh: command not found: ifconfig
    
    

###### 问题一 原因: 因在zsh 下使用的配置是 .zshrc 而这里生效时候，没有把已安装的工具的路径设置全局变量:

###### 问题一 解决方法:
    
    
     sudo vim ~/.bash_profile #创建文件
    添加如下命令
    export PATH=/bin:/usr/bin:/usr/local/bin:/sbin:$PATH
     
    再把该文件　添加到zshrc
    
    sudo vim ~/.zshrc
    
    source ~/.bash_profile
    
    最后
    source ~/.zshrc

---

> 本文由 [CSDN 博客](https://blog.csdn.net/weixin_41596275/article/details/126834750) 迁移至本站。
