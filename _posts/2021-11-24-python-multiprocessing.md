---
title: "[python专题笔记]学习/工作遇到的坑记录－python 进程"
date: 2021-11-24
categories: [Python]
tags:
  - python专题笔记
description: 一 python 进程通信字典更新深度问题 直接在进程字典中修改多级的键值失败 问题现象，修改 process dict['key1']['key2'] = '222' 失败 In [1]: import multipro
---

## 一 python 进程通信字典更新深度问题

#### 直接在进程字典中修改多级的键值失败

  * 问题现象，修改`process_dict["key1"]["key2"] = "222"` 失败


    
    
    In [1]: import multiprocessing as MP
    
    In [2]: process_dict = MP.Manager().dict()
    
    In [3]: type(process_dict)
    Out[3]: multiprocessing.managers.DictProxy
    
    In [4]: process_dict["key1"]= {}
    
    In [5]: type(process_dict)
    Out[5]: multiprocessing.managers.DictProxy
    
    In [6]: print(process_dict)
    {'key1': {}}
    
    In [7]: process_dict["key1"]["key2"] = {}
    
    In [8]: print(process_dict)
    {'key1': {}}
    
    In [9]: type(process_dict)
    Out[9]: multiprocessing.managers.DictProxy
    
    In [10]: process_dict["key1"]["key2"] = "222"
    
    In [11]: type(process_dict)
    Out[11]: multiprocessing.managers.DictProxy
    
    In [12]: print(process_dict)
    {'key1': {}}
    
    

  * 解决方法： 不能根据键值索引修改，需要整体修改方式来修改  
可以先拷贝一份字典内容，创建临时普通字典，再在普通字典上操作，最后赋值给进程字典，做到整体同时修改，例子如下


    
    
    In [1]: import multiprocessing as MP
    
    In [2]: process_dict = MP.Manager().dict()
    
    In [3]: type(process_dict)
    Out[3]: multiprocessing.managers.DictProxy
    
    In [4]: process_dict["key1"]= {}
    
    In [5]: print((process_dict))
    {'key1': {}}
    In [6]: process_dict["key1"]["key2"] = "222"
    
    In [7]: print((process_dict))
    {'key1': {}}
    
    In [8]: temporary_dict = process_dict.copy() #拷贝一份修改前的字典内容
    
    In [9]: #上述拷贝的普通字典完成
    
    In [10]:
    
    In [11]: temporary_dict["key1"]["key2"] = "222"
    
    In [12]: process_dict.update(temporary_dict)
    
    In [13]: print((process_dict))
    {'key1': {'key2': '222'}}
    
    In [14]: type(process_dict)
    Out[14]: multiprocessing.managers.DictProxy
    
    
    

## 二 python 进程通信列表操作

---

> 本文由 [CSDN 博客](https://blog.csdn.net/weixin_41596275/article/details/121523122) 迁移至本站。
