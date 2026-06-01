---
title: "[python专题笔记]层级嵌套字典问题记录"
date: 2022-03-07
categories: [Python]
tags:
  - python专题笔记
description: 问题一 : 只知道层级字典的key和顺序及值，怎么创建完整的字典 例如: key 列表和值如下: key 列表: ['8009 SSD 5', '111111111', '222222222222', '3333333
---

### 问题一 : 只知道层级字典的key和顺序及值，怎么创建完整的字典

###### 例如: key 列表和值如下:
    
    
    key 列表:
     ['8009_SSD_5', "111111111", "222222222222", "33333333333333333", 'available device infor', 'DEV_SSD25', 'status']
    值
    "ilde"
    

###### 需要创建的字典如下:
    
    
    {'8009_SSD_5': {'111111111': {'222222222222': {'33333333333333333': {'available device infor': {'DEV_SSD25': {'status': 'idle'}}}}}}}
    

###### 实现方式–递归
    
    
        def _create_nested_dict(self, nested_key_list, last_value, output_dict):
            """
                功能:   递归本体, 根据层级字典的key顺序,创建层级字典，并值赋值
                参数:   nested_key_list:    层级嵌套字典key列表, 列表元素顺序就是key 顺序
                        last_value:         最终键，对应的值
                        output_dict:        输出的层级嵌套字典
                返回值: /
            """
            if len(nested_key_list) > 1:
                KEY = nested_key_list[0]
                output_dict[KEY] = {}
                del nested_key_list[0]
                self._create_nested_dict(nested_key_list, last_value, output_dict[KEY])
            else:
                output_dict[nested_key_list[0]] = last_value
    
        def CreateNestedDict(self, nested_key_list, last_value):
            """
                功能:   调用接口, 根据层级字典的key顺序,创建层级字典，并值赋值
                参数:   nested_key_list:    层级嵌套字典key列表, 列表元素顺序就是key 顺序
                        last_value:         最终键，对应的值
                返回值: output_dict:        输出的层级嵌套字典
            """
            output_dict = {}
            self._create_nested_dict(nested_key_list, last_value, output_dict)
            return output_dict
    
    

### 问题二 : 比较复杂(嵌套)字典内容是否一致

使用 dictdiffer 库进行 复杂字典比较

###### (一) 安装
    
    
     pip3 install dictdiffer
    

###### (一) 使用示例
    
    
    In [6]: import dictdiffer
    
    In [7]: a = {"xiaoshu":{"name":"xiaoshu", "Gender":"man"}}
    
    In [8]: b = {"xiaoshu":{"name":"xiaoshu", "Gender":"woman"}}
    
    In [9]: print(list(dictdiffer.diff(a, b)))
    [('change', 'xiaoshu.Gender', ('man', 'woman'))]

---

> 本文由 [CSDN 博客](https://blog.csdn.net/weixin_41596275/article/details/123109528) 迁移至本站。
