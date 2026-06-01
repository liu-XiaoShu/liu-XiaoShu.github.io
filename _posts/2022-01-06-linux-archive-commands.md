---
title: "[Linux 命令相关笔记] 打包/解压/压缩"
date: 2022-01-06
categories: [Linux]
tags:
  - Linux 命令相关笔记
description: 命令使用 unzip 解压 win 系统创建的压缩包，出现乱码 出现乱码，原因是win 创建的文件编码格式和linux 解压使用的格式不一致，可以指定编码格式解压 例如: zunip -O GBK XXX.zp
---
> 本文属于 **Linux / Ubuntu 专题** 系列。

## 📌 简介

命令使用 unzip 解压 win 系统创建的压缩包，出现乱码 出现乱码，原因是win 创建的文件编码格式和linux 解压使用的格式不一致，可以指定编码格式解压 例如: zunip -O GBK XXX.zp

---

## 命令使用 unzip 解压 win 系统创建的压缩包，出现乱码

** 出现乱码，原因是win 创建的文件编码格式和linux 解压使用的格式不一致，可以指定编码格式解压
```bash
例如: zunip -O GBK XXX.zp
```
---

> 📎 **说明**：本文由 [CSDN「小树笔记」](https://blog.csdn.net/weixin_41596275/article/details/122338282) 迁移至 GitHub Pages，并在本站持续维护。
