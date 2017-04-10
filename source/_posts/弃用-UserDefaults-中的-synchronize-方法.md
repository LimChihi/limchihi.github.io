---
title: 弃用 UserDefaults 中的 synchronize() 方法
toc: true
comments: false
date: 2017-04-04 17:21:00
tags: 
- Swift
- iOS
categories: iOS
description: 弃用 UserDefaults 中的 synchronize() 方法，优化性能，减少磁盘I/O。
---

## 注意

在连续大量的写入数据的时候杀掉程序，会丢失数据，但这种写入，不应该用UserDefaults。

## 前言

在看[没故事的桌同学](http://www.jianshu.com/u/88a056103c02)的一篇[Swift里我用这个姿势写UserDefaults](http://www.jianshu.com/p/332ea092188e)的时候，评论下面有人说不必调用`synchronize()`，而在以前我们习惯的用写法都是设置一个Value便`synchronize()`一下，来确保数据被保存下来。便随手写了一个小Demo来验证这个问题。

## Xcode and Swift Version

- Xcode Version 8.2.1 

- Swift 3.0

## Demo

我设置了两个`UIButton` 和一个`UILabel`。

```swift
@IBOutlet weak var tipsLabel: UILabel!

@IBAction func setAction(_ sender: UIButton) {
	UserDefaults.standard.set("LimChihi", forKey: "Name")
	fatalError()
}

@IBAction func readAction(_ sender: UIButton) {
	let info = UserDefaults.standard.value(forKey: "Name") as? String
	tipsLabel.text = info ?? "nil"
}
```

第一个`setAction`用来设置值，然后使用`fatalError()`来使App在设置完成后立马crash掉，来模拟最极端的情况。

第二个`readAction` 用来读取值，并显示在`tipsLabel`上，如果为空则自动设为`"nil"`

## 结果

我进行了两次实验，第一次设置值后，即使crash掉，App仍然能够成功读取设置的值。说明在初次设置的时候，不使用`synchronize()`是完全没有问题的。

第二次，我修改了设置的Value，先读取了第一次我存储的值（没有问题），然后重新存储了新的Value（也是存储完立即crash掉），最后读取的时候显示的是新修改的Value。说明修改一个已经存在的值，不使用`synchronize()`也是完全没有问题的。

最后Apple关于`synchronize()`的文档里面有这样一段话

> synchronize is deprecated and will be marked with the NS_DEPRECATED macro in a future release.

所以，直接弃用这个浪费性能方法吧。

## 更新

因为我的方法可能缺少一些更加极端的条件，所以我又做了一次测试。

```swift
override func viewWillAppear(_ animated: Bool) {
    
    DispatchQueue.global().async { self.test() }
    DispatchQueue.global().async { self.test() }
    DispatchQueue.global().async { self.test() }
    DispatchQueue.global().async { self.test() }
    DispatchQueue.global().async { self.test() }
    DispatchQueue.global().async { self.test() }
    DispatchQueue.global().async { self.test() }
    DispatchQueue.global().async { self.test() }
}

@IBAction func setAction(_ sender: UIButton) {
    UserDefaults.standard.set("LimChihi", forKey: "Name")
    fatalError()
}
@IBAction func readAction(_ sender: UIButton) {
    let str = UserDefaults.standard.value(forKey: "Name")
    print(str ?? "nil")
    
}

func test() {
    while true {
        var a = 99999999999 * 50000
        a += 33
    }
}

```

用一个简单的while死循环来模拟高CPU负荷的情况，开了8个后台线程，然后最高CPU占用率在720-780%左右，结果依然成立，设置后立刻crash，也不影响数据。

此实验可以完全复制。
![屏幕快照 2017-04-06 下午12.29.55.png](http://upload-images.jianshu.io/upload_images/1967429-94c01cc7c75b4702.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果有什么更极端的条件，也可以在评论下提醒我，我再重新测试。