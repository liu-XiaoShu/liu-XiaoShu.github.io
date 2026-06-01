---
title: "[python专题笔记]修饰器学习"
date: 2022-08-27
categories: [Python]
tags:
  - python专题笔记
description: 修饰器 修饰器介绍 Python的修饰器（decorator） 是一个非常强大的功能，一种优秀的设计模式(装饰器模式)，将重复的内容抽象出来，赋予一个函数其他的功能，但又不去改变函数自身。使得代码极其简洁，易于维护。但其本质
---
> 本文属于 **Python 专题** 系列，记录学习与工作中的实践笔记。

## 📌 简介

修饰器 修饰器介绍 Python的修饰器（decorator） 是一个非常强大的功能，一种优秀的设计模式(装饰器模式)，将重复的内容抽象出来，赋予一个函数其他的功能，但又不去改变函数自身。使得代码极其简洁，易于维护。但其本质

---

## 修饰器

### 修饰器介绍

**Python的修饰器（decorator）** 是一个非常强大的功能，一种优秀的设计模式(装饰器模式)，将重复的内容抽象出来，赋予一个函数其他的功能，但又不去改变函数自身。使得代码极其简洁，易于维护。但其本质上还是一个语法糖

**装饰器模式简介**

**装饰器模式（Decorator Pattern）** 允许向一个现有的对象添加新的功能，同时又不改变其结构。这种类型  
的设计模式属于结构型模式，它是作为现有的类的一个包装。这种模式创建了一个装饰类，用来包装原有  
的类，并在保持类方法签名完整性的前提下，提供了额外的功能。

**优点:** 装饰类和被装饰类可以独立发展，不会相互耦合，装饰模式是继承的一个替代模式，装饰模式可以动态扩展一个实现类的功能。  
**缺点:** 多层装饰比较复杂。  
**使用场景:** 1、扩展一个类的功能。 2、动态增加功能，动态撤销。  
**注意事项:** 可代替继承。

### 修饰器解决现实问题

在实际的项目中，经常会遇到需要在原有项目上加一些功能，但是这些功能可能很快被删除掉，如何实现这种要求呢？如下例子:
```python
def function():
    print("hello word !")
```

1. 如果我想打印上述函数的执行时间，该如何做?
```python
def function():
    start_time = time()
    print("hello word !")
    end_time = time()
    print(end_time - start_time)
```

1. 如上的操作，修改了原始函数
2. 假如函数很复杂，那么我想快速增加功能和删除功能，对原始函数修改较大，特别是反复删除和增加修改困难
3. 使函数代码越来越大，不简洁
4. 这种在原有函数里面加代码，也不符合开闭原则

**修饰器实现例子**
```python
@print_run_time
def function():
    print("hello word !")
```

### 修饰器的实现

## 函数是对象的理解

在Python中，万物皆对象。(Everything in Python is an object.)，当你定义一个函数的时候，你就得到了一个函数对象，这个对象的引用就是函数名。通过函数名，你可以做如下事情：

1. 调用函数
2. 将函数赋值给其它变量
3. 把它当成参数传给其它函数
4. 可以在函数内定义一个函数，并做为返回值返回
```python
# 定义一个加法函数
def add(a, b):
    return a + b
 
# 1. 调用函数
sum1 = add(1, 2)

# 2. 把函数赋值给其它变量
sum_of_two_num = add
# 通过其它变量调用add
sum2 = sum_of_two_num(2, 3)

# 3. 把它当成参数传给其它函数
def function_1(str_content):
    return str_content + "function_1"

str_list = ["xiaoshu_", "LSK_"]
#map map(function_1, str_list) 就是 对 str_list 中的每个元素x调用 function_1(x)
print(list(map(function_1, str_list)))

# 4. 可以在函数内定义一个函数，并做为返回值返回
def test1():
    print("======================== test1 =========>")
    def test2(input_data):
        if type(input_data) != str:
            print("非字符串 !")
        else:
            print("字符串 !")
    return test2

t = test1()
print(t)

t("STR")
t(1)
```

## 为函数增加功能(修饰一下函数)

我写了一个加法计算的函数，现在我需增加一个功能，增加一个类型判断，但是这个功能又不想嵌入加法函数内，因为我想使得代码极其简洁，易于维护。代码如下:
```python
def test1(function):
    print("======================== test1 =========>")
    def test2(input_1, input_2):
        if type(input_1) == int and type(input_2) == int:
            print("输入类型正确")
            return function(input_1, input_2)
        else:
            print("输入类型错误")
            return False
    return test2

def add(x, y):
    return x + y

new_add = test1(add)
print(new_add)
print(new_add(1, 2))
```

**问题** : 这个写法，要实现函数新增功能的时候，需要在调用的时候多写步骤，假如在多个地方调用，那么修改起来复杂

**正确且简单写法**
```python
def test1(function):
    print("======================== test1 =========>")
    def test2(input_1, input_2):
        if type(input_1) == int and type(input_2) == int:
            print("输入类型正确")
            return function(input_1, input_2)
        else:
            print("输入类型错误")
            return False
    return test2

@test1
def add(x, y):
    return x + y

print(add(1, 2))
```

如下代码，可以想象是一个函数的帽子，在执行函数之前，把函数作为参数，传入帽子计算完毕后，再执行本函数
```bash
@test1
```

### 修饰器几种修饰场景

### 一 用函数修饰函数
```python
#一　用函数修饰函数
def test1(function):
    print("======================== test1 =========>")
    def test2(input_1, input_2):
        if type(input_1) == int and type(input_2) == int:
            print("输入类型正确")
            return function(input_1, input_2)
        else:
            print("输入类型错误")
            return False
    return test2

@test1
def add(x, y):
    return x + y

print(add(1, 2))
```

### 二 用函数修饰类
```python
# 二、用函数修饰类
#!/usr/bin/python3

def decorate_class(aClass):
    def call(*args, **kwargs):
        print('you have create a %s class, its name is %s.' % (aClass.__name__, args[0]))
        return aClass(*args, **kwargs)
    return call

@decorate_class
class People():
    def __init__(self, name):
        self.name = name
```python
def print(self):
print('My name is %s.' % (self.name))
```

a = People('Tom')
a.print()

b = People('Jerry')
b.print()
```

### 三 用函数修饰类方法
```python
# 三、用函数修饰类方法
#!/usr/bin/python3

def decorate_method(func):
    def call(*args, **kwargs):
        print('you have called %s().' % (func.__name__))
        func(*args, **kwargs)
    return call

class aClass():
    def __init__(self, name):
        self.name = name
    @decorate_method
    def print(self):
        print('My name is %s.' % (self.name))
a = aClass('Jerry')
a.print()
```

### 四 用类修饰函数
```python
# 四、用类修饰函数
#!/usr/bin/python3
class decorate_func():
    def __init__(self, func):
        self.func = func
```python
def __call__(self, *args, **kwargs):
print('youhave called %s().' % (self.func.__name__))
self.func(*args, **kwargs)
```

@decorate_func
def func(name):
    print('My name is %s.' % (name))

func('Bob')
```

### 五 用类修饰类
```python
# 五、用类修饰类
#!/usr/bin/python3

class decorate_class():
    def __init__(self, aClass):
        self.aClass = aClass
    def __call__(self, *args, **kwargs):
        print('You have created a %s class.' % (self.aClass.__name__))
        return self.aClass(*args, **kwargs)

@decorate_class
class People():
    def __init__(self, name):
        self.name = name
    def print(self):
        print('My name is %s .' % (self.name))

a = People('Tom')
```

### 六 匿名函数作为函数修饰器
```python
# 六、匿名函数作为函数修饰器
#!/usr/bin/python3

def attrsetter(attr, value):
    """ Return a function that sets ``attr`` on its argument and returns it. """
    return lambda method: setattr(method, attr, value) or method

def depends(*args):
    if args and callable(args[0]):
        args = args[0]
    elif any('id' in arg.split('.') for arg in args):
        raise NotImplementedError("Compute method cannot depend on field 'id'.")
    return attrsetter('_depends', args)

class People():
    def __init__(self, name):
        self.name = name
    # 这里用的是函数修饰器，但不是depends函数，而是depends函数执行完之后返回的匿名函数作为函数装饰器
    @depends('test')
    def print(self):
        print('My name is %s .' % (self.name, ))

a = People('Tom')
a.print()
print(a.print.__dict__)
```

参考

https://www.cnblogs.com/willsdu/p/16422647.html

https://www.cnblogs.com/watermeloncode/p/16531534.html

https://zhuanlan.zhihu.com/p/160979609
---

> 📎 **说明**：本文由 [CSDN「小树笔记」](https://blog.csdn.net/weixin_41596275/article/details/126541721) 迁移至 GitHub Pages，并在本站持续维护。
