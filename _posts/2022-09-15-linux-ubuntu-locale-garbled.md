---
title: "［Linux专题笔记］ubuntu 中文显示乱码"
date: 2022-09-15
categories: [Linux]
tags:
  - Linux专题笔记
  - Ubuntu
description: 安装使用 zsh 终端中中文乱码 解决方法 vim ~/.zshrc 添加如下命令 export LC ALL=en US.UTF-8 export LANG=en US.UTF-8 生效配置 source ~/.
---
> 本文属于 **Linux / Ubuntu 专题** 系列。

## 📌 简介

安装使用 zsh 终端中中文乱码 解决方法 vim ~/.zshrc 添加如下命令 export LC ALL=en US.UTF-8 export LANG=en US.UTF-8 生效配置 source ~/.

---

## 安装使用 zsh 终端中中文乱码

### 解决方法
```bash
vim ~/.zshrc
添加如下命令
export LC_ALL=en_US.UTF-8  
export LANG=en_US.UTF-8

生效配置
source ~/.zshrc
```

## 安装使用 bash 终端中中文乱码

### 解决方法
```bash
vim ~/.bashrc
添加如下命令
export LC_ALL=en_US.UTF-8  
export LANG=en_US.UTF-8

生效配置
source ~/.bashrc
```

## vim 文件中中文乱码

### 解决方法
```bash
#编辑该文件
vim ~/.vimrc 
添加如下命令
set fileencodings=utf-8,ucs-bom,gb18030,gbk,gb2312,cp936
set termencoding=utf-8
set encoding=utf-8 

source ~/.vimrc
```
---

> 📎 **说明**：本文由 [CSDN「小树笔记」](https://blog.csdn.net/weixin_41596275/article/details/126857197) 迁移至 GitHub Pages，并在本站持续维护。
