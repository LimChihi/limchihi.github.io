---
title: Objective-C 的一些笔记
toc: true
comments: false
date: 2017-04-10 19:55:12
tags:
- Objective-C
- iOS
categories: iOS
description: 读Objective-C for Swift developers的一些笔记
---

## 前言

最近在看**Objective-C for Swift developers**做些笔记，以防忘记

## 方法

### 调用方法

oc使用`[]`来给方法发送信息。

```objective-c
MyObjec *myObject = [MyObject new]
```

一般来说实例化一个类，都会先分配内存，在init

```objective-c
MyObject *myObject = [[MyObject alloc] init];
```

有多个参数的时候，使用空格分割，而不是逗号

```objective-c
[myObject executeMethod:hello param2:world];
```

## 指针

### 定义常量指针

​	保证指针不会变化

```Ob
NSString * const first = @"Hello";
```

## 字符串

### 判断两个字符串相等

使用`sEqualToString`而不是`==`

## 其他

### 合并空值

下面代码的意思是如果`name`为空，则用`"Anonymous"`

```objective-c
NSString *name = nil;
NSLog(@"Hello, %@!", name ?: @"Anonymous");
```

### 