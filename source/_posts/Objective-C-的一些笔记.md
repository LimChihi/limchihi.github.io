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

### 方法冒号

冒号是方法名称的组成部分

```objective-c
- (void) medthod;
- (void) medthod: (Parameter) par；
```

使用选择器选择的时候是这样的

```objective-c
@selector(medthod);
@selector(medthod:);
```



## Setter, Getter

### 命名

对于一个属性`car` ,setter方法一般命名为`setCar`, getter方法一般命名为`car`

而`getCar`一般是一个参数，需要你传入一个指针，然后函数将处理结果通过这个指针返回。所以getter不会命名为`getCar`.

## 指针

### 定义常量指针

​	保证指针不会变化

```Ob
NSString * const first = @"Hello";
```

## 字符串

### 判断两个字符串相等

使用`sEqualToString`而不是`==`



## Category

在不修改原来的类上的基础增加新的方法

优先级比原类的方法高，会直接覆盖原类的方法

### 语法

`Person+Man.h`

```objective-c
#import "Person.h"

@interface Person (PersonMan) 

- (void) info;

@end
```

`Person+Man.m`

```objective-c
#import "Person+Man.h"
@implementation Person(Man)
  
- (void) info {
  ...
}
  
@end
```

调用的时候需要导入Category的.h文件。



## Extension

Extension一般是用来更改权限，使方法私有或者改变属性的读写权限

### 语法

#### 方法一

`PersonExtend.h`

```objective-c
#import "person.h"
@interface Person ()

- (void) smile;

@end
```

然后在`person.m`中引入`PersonExtend.h`

#### 方法二

直接写到`Person.m`中

```objective-c
@interface Person()
...  
@end
  
@implementation Person
...  
@end
```






## 其他

### 合并空值

下面代码的意思是如果`name`为空，则用`"Anonymous"`

```objective-c
NSString *name = nil;
NSLog(@"Hello, %@!", name ?: @"Anonymous");
```

### 前向引用

```objective-c
@class
```

不用 `import` 

### 发送消息

只有访问属性的时候使用点语法，其他使用使用中括号调用

