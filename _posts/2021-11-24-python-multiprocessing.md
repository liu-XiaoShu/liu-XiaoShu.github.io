---
title: "[python专题笔记]学习/工作遇到的坑记录－python 进程"
date: 2021-11-24
categories: [Python]
tags:
  - python专题笔记
  - multiprocessing
description: multiprocessing 共享 dict 嵌套键无法就地修改，需 copy 后 update 整体写回。
---

> 本文属于 **Python 专题** 系列，记录学习与工作中的实践笔记。

## 📌 简介

`multiprocessing.Manager().dict()` 创建的共享字典，**不能**通过 `process_dict["key1"]["key2"] = value` 直接改嵌套层级。本文记录现象与可行 workaround。

---

## 一、进程字典：嵌套键无法就地修改

### 问题现象

对 `process_dict["key1"]["key2"] = "222"` 赋值后，打印字典仍为空壳：

```python
import multiprocessing as MP

process_dict = MP.Manager().dict()
process_dict["key1"] = {}
process_dict["key1"]["key2"] = "222"
print(process_dict)  # {'key1': {}}  ← 内层未生效
```

### 解决方法

不能按层级索引逐层改，需要 **拷贝 → 在普通 dict 上改 → `update` 整体写回**：

```python
import multiprocessing as MP

process_dict = MP.Manager().dict()
process_dict["key1"] = {}

temporary_dict = process_dict.copy()
temporary_dict["key1"]["key2"] = "222"
process_dict.update(temporary_dict)

print(process_dict)  # {'key1': {'key2': '222'}}
```

---

## 二、进程通信列表操作

（原文在 CSDN 未展开，后续可在本站补充。）

---

> 📎 **说明**：本文由 [CSDN「小树笔记」](https://blog.csdn.net/weixin_41596275/article/details/121523122) 迁移至 GitHub Pages，并在本站持续维护。
