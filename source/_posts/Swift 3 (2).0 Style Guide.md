# Swift 3.0 Style Guide
#### Version 0.9 Beta

## 命名

1. ［强制］使用英文单词来命名。

	**Preferred:**

	```swift
	var passworld = String()
	var requestData = NSData()
	
	```
	禁止拼音，拼音英文混合，全中文，甚至emoji的命名
	
	**Preferred:**
	
	```swift
	let nihao = "hello"
	var qingqiuData = NSData() // 请求数据
	let 你好 = "hello"
	let 🍎 = "apple"
	```

2. ［强制］使用小写驼峰命名变量，大写驼峰命名类别。

	**Preferred:**
	
	```swift
	let helloWorld = "Hello"
	
	class HelloWorld {...}
	struct HeloWorld {...}
	```
	
	拒绝一切全大写，小写加下划线的命名方式
	
	**Preferred:**
	
	```swift
	let HELLOWORLD = "Hello"
	var hello_world = "hello"
	```
	
3. ［强制］名称做到望文生义，不要嫌名称过长。
	
	**正例**
	
	```swift
	private func bindingLockWithoutLockId(...)
	```
	
	拒绝无意义的缩写，或者不是通用的缩写
	
	**Preferred:**
	
	```swift
	private func bLock(...)
	```

4. ［强制］名称需要避免歧义。

	**Preferred:**

	```swift
	struct List {
		mutating func remove(at position: Index) -> Element
	}
	
	list.remove(at: x)
	
	```

	如果我们删除 `at` 这可能具有两种意思。第一，删除在 `x` 的元素，第二，我们搜索并删除与 `x` 相等的元素。
	
	**Preferred:**
	
	```swift
	struct List {
		mutating func remove(_ position: Index) -> Element
	}
	
	list.remove(x)
	```

5. ［强制］省略不必要的单词。

	可以使用较多的单词来描述信息，但是那些重复的，读者已经拥有的信息需要删掉。
	
	**Preferred:**

	```swift
	public mutating func remove(_ member: Element) -> Element?
	allViews.remove(cancelButton) // clearer
	```
	
	**Preferred:**

	```swift
	mutating func removeElement(_ member: Element) -> Element?
	allViews.removeElement(cancelButton)
	```
	
6. ［强制］根据用途来命名。

	**Preferred:**

	```swift
	var greeting = "Hello"
	class ProductionLine {
	  func restock(from supplier: WidgetFactory)
	}
	```
	
	**Preferred:**

	```swift
	var string = "Hello"
	class ProductionLine {
	  func restock(from widgetFactory: WidgetFactory)
	}
	```
7. ［强制］当涉及比较大的类型时，增加更多信息来澄清参数的作用。

	特别是当参数为`NSobject`,	`Any`,`AnyObject`的时候，还有一些`Int`,`String`，这些基本类型。在本文件使用的时候可以联系上下文，但当作为借口使用的时候，可能会对使用者造成困扰。
	
	**Preferred:**

	```swift
	func addObserver(_ observer: NSObject, forKeyPath path: String)
	grid.addObserver(self, forKeyPath: graphics) // clear
	```
	
	**Preferred:**

	```swift
	func add(_ observer: NSObject, for keyPath: String)
	grid.add(self, for: graphics) // vague
	```

更多命名规范请查阅：[Apple Design Guidelines](https://swift.org/documentation/api-design-guidelines/#promote-clear-usage)

## delegate

When creating custom delegate methods, an unnamed first parameter should be the delegate source. (UIKit contains numerous examples of this.)

**Preferred:**

```swift
func namePickerView(_ namePickerView: NamePickerView, didSelectName name: String)
func namePickerViewShouldReload(_ namePickerView: NamePickerView) -> Bool
```

**Not Preferred:**

```swift
func didSelectName(namePicker: NamePickerViewController, name: String)
func namePickerShouldReload() -> Bool
```

## 类型推断

除非是`option`类型或其他需要特殊标明(`UInt8`, `CGFloat`)的类型，否者尽量使用类型推断。

**Preferred:**

```swift
let balance: Float = 1.2
let hello = "hello"
let number = 23
let selector = #selector(viewDidLoad)
view.backgroundColor = .red
let toView = context.view(forKey: .to)
let view = UIView(frame: .zero)
```

**Preferred:**

```swift
let balance = 1.2 //Double
let hello: String = "hello"
let number: Int = 23
let selector = #selector(ViewController.viewDidLoad)
view.backgroundColor = UIColor.red
```

#### 空字典与数组的初始化

**Preferred:**

```swift
var names: [String] = []
var lookup: [String: Int] = [:]
```

**Not Preferred:**

```swift
var names = [String]()
var lookup = [String: Int]()
```

	
## 范型

范型的命名同样需要符合可描述性，命名规则同上，如果一个范型没有一个比较明显的规律，使用传统的范型命名如 `T`，`U`

**Preferred:**

```swift
struct Stack<Element> { ... }
func write<Target: OutputStream>(to target: inout Target)
func swap<T>(_ a: inout T, _ b: inout T)
```

**Preferred:**

```swift
struct Stack<T> { ... }
func write<target: OutputStream>(to target: inout target)
func swap<Thing>(_ a: inout Thing, _ b: inout Thing)
```

## 代码结构

使用 `extension` 来组织代码，每一个功能组成一个模块，并且用 `// MARK: -` 来标明模块。

### 协议规范

当给一个model添加一个 `protocol` 的时候，`protocol` 方法应当使用一个分开的 `extension`。 这能让 `protocol` 与相关的方法很清晰的关联在一起，并能非常简单添加新的`protocol`与其所必需的方法。

**Preferred:**

```swift
class MyViewController: UIViewController {
  // class stuff here
}

// MARK: - UITableViewDataSource
extension MyViewController: UITableViewDataSource {
  // table view data source methods
}

// MARK: - UIScrollViewDelegate
extension MyViewController: UIScrollViewDelegate {
  // scroll view delegate methods
}
```

**Not Preferred:**

```swift
class MyViewController: UIViewController, UITableViewDataSource, UIScrollViewDelegate {
  // all methods
}
```

自从Xcode不再允许在派生类中重新声明遵从某`protocol`后，就没用必要总是为相应的`protocol`及其方法添加相应的方法另开一个`extension`


###  无用代码

所有无用的代码，包括Xcode自动生成的代码与占位注释。

只是简单的调用了基类里面的方法的方法也需要删除，包括UIApplicationDelegate内的空方法和无用方法。

Aspirational methods not directly associated with the tutorial whose implementation simply calls the superclass should also be removed. This includes any empty/unused UIApplicationDelegate methods.

**Preferred:**

```swift
override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
  return Database.contacts.count
}
```

**Not Preferred:**

```swift
override func didReceiveMemoryWarning() {
  super.didReceiveMemoryWarning()
  // Dispose of any resources that can be recreated.
}

override func numberOfSections(in tableView: UITableView) -> Int {
  // #warning Incomplete implementation, return the number of sections
  return 1
}

override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
  // #warning Incomplete implementation, return the number of rows
	  return Database.contacts.count
}

```

### 尽量少的 `Improt`

保持Improts的简洁性。没有用到的库不应该引入，如果一个库包含了另外一个库，则不必重复引入两个库。比如如果引入了 `Foundation` 则不必引入 `UIKit` 

##  空格

* 锁进使用 `Tap` 制表符键而不是4个空格来节省时间与提升键盘寿命。
* 花括号(`func`/`if`/`else`/`switch`/`while` etc.)，应当开在声明的同一行，并在新的一行结束。
* Tips: 使用 ***Control-I***可以自动格式化部分格式。

**Preferred:**

```swift
if user.isHappy {
  // Do something
} else {
  // Do something else
}
```

**Not Preferred:**

```swift
if user.isHappy
{
  // Do something
}
else {
  // Do something else
}
```

* 在两个方法间，需要也只能空一行来保持视觉清爽。
* 方法体内可以使用一个空行来分开两种功能，不过如果你用来太多的空行，说明你的一个方法内实现了过多功能，需要重构。
* 冒号的左边不加空格，而右边需要加空格。三元运算符的`? :`，空字典的 `[:]`，和`#selector`的匿名参数`(_:)`例外。
 
**Preferred:**

```swift
class TestDatabase: Database {
  var data: [String: CGFloat] = ["A": 1.2, "B": 3.2]
}
```

**Not Preferred:**

```swift
class TestDatabase : Database {
  var data :[String:CGFloat] = ["A" : 1.2, "B":3.2]
}
```

* 推荐在100字符后换行，不作强硬要求。
* 避免首位无意义的空格。
* 在每个文件的最后加上一行空行

## 注释

尽量使用注释来表明代码的参数，返回值，作用，或者如何运行的。注释需要及时更新保持最新状态。

避免在代码块中注释，尽量使代码本身就具有注释的功能。

方法文档的格式使用如下形式

```swift
///	Get `UIAlertcontroller` that with a cancle button. It can not be expanded.
///
/// 获取一个包含取消按钮的不可被拓展的`UIAlertController`
///
///	- parameter title:      AlertController's title. Default is nil.
///	- parameter message:	AlertController's message. Default is nil.
///
///	- returns:              A Alert Controller
func getAlertController(title: String? = nil, message: String? = nil ) -> UIAlertController  {
	...
}
```
## class

### self的使用
如无必要，不要使用`self`

### 计算属性
如果一个计算属性是只读的，那么可以忽略`get`，`get`只有在`set`也存在的时候才是必须的。

**Preferred:**

```swift
var diameter: Double {
  return radius * 2
}
```

**Not Preferred:**

```swift
var diameter: Double {
  get {
    return radius * 2
  }
}
```

## 方法声明

使方法声明尽可能的短。

```swift
func reticulateSplines(spline: [Double]) -> Bool {
  // reticulate code goes here
}
```

如果有许多的参数，则在合适的地方换行（比如100字符内），并在新的一行前增加一个缩进。

```swift
func reticulateSplines(spline: [Double], adjustmentFactor: Double,
    translateConstant: Int, comment: String) -> Bool {
  // reticulate code goes here
}
```

## 闭包

只有在参数存在唯一一个闭包并在参数的最后时使用尾闭包语法糖，当有超过一个闭包的时候紧致使用尾闭包。同时，闭包名称也应该具有描述性，不能直接写`closure`

**Preferred:**

```swift
UIView.animate(withDuration: 1.0) {
  self.myView.alpha = 0
}

UIView.animate(withDuration: 1.0, animations: {
  self.myView.alpha = 0
}, completion: { finished in
  self.myView.removeFromSuperview()
})
```

**Not Preferred:**

```swift
UIView.animate(withDuration: 1.0, animations: {
  self.myView.alpha = 0
})

UIView.animate(withDuration: 1.0, animations: {
  self.myView.alpha = 0
}) { f in
  self.myView.removeFromSuperview()
}
```

当单个闭包表达式上下文清晰时，使用隐式的返回

```swift
attendeeList.sort { a, b in
  a > b
}
```

使用链式编程会使尾闭包更加清晰易读。

```swift
let value = numbers.map { $0 * 2 }.filter { $0 % 3 == 0 }.index(of: 90)

let value = numbers
  .map {$0 * 2}
  .filter {$0 > 50}
  .map {$0 + 10}
```

## 类型

尽量使用Swift原生的类型，而不使用Swift提供的Objective-C类型，比如能使用`String`就不用｀NSString｀。

**Preferred:**

```swift
let width = 120.0                                    // Double
let widthString = (width as NSNumber).stringValue    // String
```

**Not Preferred:**

```swift
let width: NSNumber = 120.0                          // NSNumber
let widthString: NSString = width.stringValue        // NSString
```

### 常量

常量使用`let`关键字来定义，而变量使用`var`来定义。如果可以，如果改变量在其生命周期内值没有改变，那么久应该把这个定义成常量。

***Tips*** 直接使用`let`来定义所有变量，直到编译器提示`Error`才改变其定义为`var`

定义全局常量的时候尽量使用类型常量而不是实例常量，使用`static let`就可以直接简单的定义类型常量。这样的方式定义的类型常量优于直接定义的全局常量，更容易区分于实例变量。更不会污染全局变量池。

**Preferred:**

```swift
enum Math {
  static let e = 2.718281828459045235360287
  static let root2 = 1.41421356237309504880168872
}

let hypotenuse = side * Math.root2

```
***Note:*** 这样可以避免枚举被不小心实例化，而且全局有效

### 可选型

如果一个变量可能被赋值为`nil`，那么可以把这个变量或者函数返回值声明为可选型（？）。

如果一个变量不会在一开始就被初始化，而在稍后初始化，并且确定一定没有`nil`且一定在使用前被初始化的话，可以使用隐式解包（！）。

使用可选型的时候，如果只需要，一次或者同一次访问有多个可选型，使用可选值链

```swift
self.textContainer?.textLabel?.setNeedsDisplay()
```

如果一次拆包有多次使用的话可以使用可选型绑定。

```swift
if let textContainer = self.textContainer {
  // do many things with textContainer
}
```

不需要在可选型命名的时候，标明这是个可选型，比如`optionalString` 是不推荐的 `maybeView`，因为我们可以很轻易的查看这个类型。

使用可选型绑定的时候，新的局部解包变量名称于原来的可选型名称相同即可。

**Preferred:**

```swift
var subview: UIView?
var volume: Double?

// later on...
if let subview = subview, let volume = volume {
  // do something with unwrapped subview and volume
}
```

**Not Preferred:**

```swift
var optionalSubview: UIView?
var volume: Double?

if let unwrappedSubview = optionalSubview {
  if let realVolume = volume {
    // do something with unwrappedSubview and realVolume
  }
}
```

## 访问控制

与`let`一样，可以使用`private`的情况请务必使用`private`， `fileprivate`可以在`extension`的时候使用。

基本不用`open`,`public`与`internal`

访问修饰符一般放在最前面，除非是`static``@IBAction`, `@IBOutlet` 和 `@discardableResult`的情况。

**Preferred:**

```swift
private let message = "Great Scott!"

class TimeMachine {  
  fileprivate dynamic lazy var fluxCapacitor = FluxCapacitor()
}
```

**Not Preferred:**

```swift
fileprivate let message = "Great Scott!"

class TimeMachine {  
  lazy dynamic fileprivate var fluxCapacitor = FluxCapacitor()
}
```

## 循环

尽量使用`for-in`风格的循环而不使用`while`

**Preferred:**

```swift
for _ in 0..<3 {
  print("Hello three times")
}

for (index, person) in attendeeList.enumerated() {
  print("\(person) is at position #\(index)")
}

for index in stride(from: 0, to: items.count, by: 2) {
  print(index)
}

for index in (0...3).reversed() {
  print(index)
}
```

**Not Preferred:**

```swift
var i = 0
while i < 3 {
  print("Hello three times")
  i += 1
}

var i = 0
while i < attendeeList.count {
  let person = attendeeList[i]
  print("\(person) is at position #\(i)")
  i += 1
}
```

## 避免过多的嵌套

当有多个条件需要判断的时候，使用`if`或者`if-let`语法会导致多层嵌套，导致后来者阅读问题。这种情况可以使用`guard`

**Preferred:**

```swift
func computeFFT(context: Context?, inputData: InputData?) throws -> Frequencies {

  guard context != nil else { 
    throw FFTError.noContext 
  }
  guard inputData != nil else { 
    throw FFTError.noInputData 
  }
    
  // use context and input to compute the frequencies
    
  return frequencies
}
```

**Not Preferred:**

```swift
func computeFFT(context: Context?, inputData: InputData?) throws -> Frequencies {

  if let context = context {
    if let inputData = inputData {
      // use context and input to compute the frequencies

      return frequencies
    } else {
      throw FFTError.noInputData
    }
  } else {
    throw FFTError.noContext
  }
}
```

## 分号

禁止出现分号

## 圆括号

在判断条件中，不推荐使用圆括号。

**Preferred:**

```swift
if name == "Hello" {
  print("World")
}
```

**Not Preferred:**

```swift
if (name == "Hello") {
  print("World")
}
```

不过在一个比较长语句的情况下，圆括号可以让表达更佳清晰。

**Preferred:**

```swift
let playerMark = (player == current ? "X" : "O")
```

---

Power By LimChihi