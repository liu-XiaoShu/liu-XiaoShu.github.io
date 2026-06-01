---
title: "[gitHub使用笔记一]ubuntu下快速访问github官网的方法"
date: 2022-02-13
categories: [Linux]
tags:
  - gitHub使用笔记一
  - Ubuntu
description: ubuntu下快速访问github官网的方法一 修改hosts文件 1 先打开dns查询网站：[dns查询网站](https://tool.chinaz.com/dns/?type=1&host=github
---
> 本文属于 **Linux / Ubuntu 专题** 系列。

## 📌 简介

[ ok ] Restarting networking (via systemctl): networking.service.

---

### ubuntu下快速访问github官网的方法一

### 修改hosts文件

### 1 先打开dns查询网站：[dns查询网站](https://tool.chinaz.com/dns/?type=1&host=github.com&ip=)

### 2 输入城名 github.com , 点击<检测>

### 3 找到 TTL值最小，把对应的IP记下来

### 4 记录到hosts文件中
```bash
打开文件
sudo vim /etc/hosts

添加 格式是ip 域名
20.205.243.166 github.com
```

### 5 刷新DNS
```bash
sudo /etc/init.d/networking restart
[ ok ] Restarting networking (via systemctl): networking.service.
```

至此可以重新访问github
---

> 📎 **说明**：本文由 [CSDN「小树笔记」](https://blog.csdn.net/weixin_41596275/article/details/122905304) 迁移至 GitHub Pages，并在本站持续维护。
