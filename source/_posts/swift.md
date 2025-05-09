---
title: Swift学习摘记
date: 2025-04-17 20:21:21
tags:
categories: 编程语言
excerpt: 学习swift中的基本语法和Swift-UI等框架知识.
thumbnail: https://my-blog-img-1358266118.cos.ap-guangzhou.myqcloud.com/undefined20250509094625386.png?imageSlim
---


# 初见

默认情况下，函数使用它们的参数名称作为它们参数的标签，在参数名称前可以自定义参数标签，或者使用 `_` 表示不使用参数标签:

```swift
func greet(_ person: String, on day: String) -> String {
    return "Hello \(person), today is \(day)."
}
greet("John", on: "Wednesday")
```

> 参数标签指的是调用时候的名称, 参数名指的是函数内部.



函数是第一等类型，这意味着函数可以作为另一个函数的返回值。

```swift
func makeIncrementer() -> ((Int) -> Int) {
    func addOne(number: Int) -> Int {
        return 1 + number
    }
    return addOne
}
var increment = makeIncrementer()
increment(7)
```



函数也可以作为另一个函数的输入参数:

```swift
func hasAnyMatches(list: [Int], condition: (Int) -> Bool) -> Bool {
    for item in list {
        if condition(item) {
            return true
        }
    }
    return false
}
func lessThanTen(number: Int) -> Bool {
    return number < 10
}
var numbers = [20, 19, 7, 12]
hasAnyMatches(list: numbers, condition: lessThanTen)
```



可以通过参数位置而不是参数名字来引用参数——这个方法在非常短的闭包中非常有用。

```swift
let sortedNumbers = numbers.sorted { $0 > $1 }
print(sortedNumbers)
```





`actor`与 `class`类似, 但是可以序列化访问, 保护共享、可变的数据.



### 对象和类

#### 构造与析构

使用 `self.`替代 `this->`, 使用  `init` 和  `deinit`分别声明构造和析构函数:

```swift
class NamedShape {
    var numberOfSides: Int = 0
    var name: String

    init(name: String) {
        self.name = name
    }

    func simpleDescription() -> String {
        return "A shape with \(numberOfSides) sides."
    }
}
```



子类如果要重写父类的方法的话，需要用 `override` 标记:

```swift
class Square: NamedShape {
    var sideLength: Double

    init(sideLength: Double, name: String) {
        self.sideLength = sideLength
        super.init(name: name)
        numberOfSides = 4
    }

    func area() ->  Double {
        return sideLength * sideLength
    }

    override func simpleDescription() -> String {
        return "A square with sides of length \(sideLength)."
    }
}
let test = Square(sideLength: 5.2, name: "my test square")
test.area()
test.simpleDescription()
```

> - 使用 `:className`的方法声明父类.
> - `super.init` 是用来调用父类（超类）的初始化方法的。当创建一个子类的实例时，子类可能需要初始化一些自己的属性，同时还需要确保父类的属性也被正确初始化。这时就需要使用 `super.init` 来调用父类的初始化方法，完成父类的初始化过程
> - 严格的顺序要求: 子类必须先初始化自己的属性，然后调用 `super.init`，最后才能访问或修改继承来的属性。



### 计算属性

#### 普通的计算属性

在属性内部使用 `{}`并加上 `return`, 可以让访问这个属性的时候, 返回值由结构体的其他属性计算得到.

```swift
struct Temperature {
  var celsius: Double
  var fahrenheit: Double{
    return celsius *1.8 + 32
  }
  
  init(celsius: Double){
    self.celsius = celsius
  }
}
```

> 1. 此时实例化一个结构体就只需要给出一个属性的值.
> 2. `self`在上述的Swift代码中是不可或缺的, 因为形参和内部属性的名称相同.



#### 使用 getter 和 setter 的计算属性:

```swift
class EquilateralTriangle: NamedShape {
    var sideLength: Double = 0.0

    init(sideLength: Double, name: String) {
        self.sideLength = sideLength
        super.init(name: name)
        numberOfSides = 3
    }

    var perimeter: Double {
      // 根据存储属性进行计算
        get {
            return 3.0 * sideLength
        }
      // 设置属性
        set {
            sideLength = newValue / 3.0
        }
    }

    override func simpleDescription() -> String {
        return "An equilateral triangle with sides of length \(sideLength)."
    }
}
var triangle = EquilateralTriangle(sideLength: 3.1, name: "a triangle")
print(triangle.perimeter)
triangle.perimeter = 9.9
print(triangle.sideLength)
```

计算属性同样是一个属性, 但是根据调用方式的不同, 有返回和设置两种方式.

```swift
triangle.perimeter = 9.9
print(triangle.perimeter)
```



### Mutating

默认情况下, 结构体中的方法不能直接修改结构体的属性. 需要显式声明为 `mutating`:

```swift
struct User{
  let username:String
  var isVisible:Bool = true
  var friends: [string] = []
  
  mutating func addFriend(username: String){
    friends.append(username)
  }
}
```



### 属性监视器



使用 `willSet` 和 `didSet`。写入的代码会在属性值发生改变时调用，但不包含构造器中发生值改变的情况:

- 分别可以使用 `newValue`与 `oldValue`来表示属性将要改变的值以及改变之前的值. 

e.g. 确保三角形的边长总是和正方形的边长相同。

```swift
class TriangleAndSquare {
    var triangle: EquilateralTriangle {
        willSet {
            square.sideLength = newValue.sideLength
        }
    }
    var square: Square {
        willSet {
            triangle.sideLength = newValue.sideLength
        }
    }
    init(size: Double, name: String) {
        square = Square(sideLength: size, name: name)
        triangle = EquilateralTriangle(sideLength: size, name: name)
    }
}
var triangleAndSquare = TriangleAndSquare(size: 10, name: "another test shape")
print(triangleAndSquare.square.sideLength)
print(triangleAndSquare.triangle.sideLength)
triangleAndSquare.square = Square(sideLength: 50, name: "larger square")
print(triangleAndSquare.triangle.sideLength)
```



### 枚举

```swift
enum Rank: Int {
    case ace = 1
    case two, three, four, five, six, seven, eight, nine, ten
    case jack, queen, king
    func simpleDescription() -> String {
        switch self {
        case .ace:
            return "ace"
        case .jack:
            return "jack"
        case .queen:
            return "queen"
        case .king:
            return "king"
        default:
            return String(self.rawValue)
        }
    }
}
let ace = Rank.ace
let aceRawValue = ace.rawValue
```

- 如果没有设置第一个成员的初始值, 默认从 `0` 开始;
- 缺省值按照递增处理;
-  `case`之外可以设置方法. 



使用 `init?(rawValue:)` 初始化构造器来从原始值创建一个枚举实例:

```swift
if let convertedRank = Rank（rawValue:3）｛
	let threeDescription = convertedRank.simpleDescription（）
｝
```

> - `if let`表示可选绑定, 安全地解包可选值





#### 解包

1. `if let`解包

```swift
if let A = B {
  ...
}
```

如果 `B`不是 nil, 就将其赋值给A, 然后执行 `{}`内部的语句. 

2. `??`

```swift
var score : Int ?  = nil

print(score ?? default_score)
// 成绩score不是nil, 就将其打印, 否则输出默认的成绩
```



对字典进行索引:

```swift
// scores是一个Int数组
for ( major, scores) in all_scores {
  for score in scores{
    ...
  }
}

// 如果key没有使用, 可以直接忽略
for( _, socres) in all_scores{
  ...
}
```



swift支持对字典进行更新或者移除的时候, 返回并使用就值:

```swift
if let oldValue = scores.updateValue(100, forKey:"fad"){
  print(..)
}else{
  ...
}

if let oldValue = scores.removeValue(forKey: "fad"){
  print("fad's old value was \(oldValue)")
}
```

 



- `@IBAction`表示组件交互和代码相绑定(允许在交互的时候执行外部定义的函数);
- `@IBOutlet`表示允许代码的响应改变组件本身的状态(字体、大小等). 

<img src="swift.assets/image-20250412115454400.png" alt="image-20250412115454400" style="zoom:57%;" />







### 概念

#### 闭包

闭包指的是可以在特定位置运行的、不需要名称的函数.

```swift
scene.setOnStartHandler｛ 
// 闭包的主体
｝
```



- `toggle()`可以自动切换变量的布尔值.

  ```swift
  Button("Press Me") {
      isOn.toggle()
  }
  ```



#### 状态属性

- 使用 `@State`在视图之外定义;
- 当状态属性的值发生改变时, 会自动更新视图中相关的部分.

- 对于状态对象, 使用 `@StateObject`来声明.

#### 绑定

由 `@Binding`声明将属性连接到其他地方, 允许子视图对属性的修改并同步.

在属性的前面增加 `$`，表明会同步修改可信源.



#### 字符串插值

在较长字符串中使用常量、变量或代码表达式，使它们替换为其当前值以求出字符串的值。

例如，在字符串"Katy ate a \（fruit）."中，如果fruit 是带有值 "peach"的变量，那么在求字符串的值时，\（fruit）由"peach"替换，变为 "Katy ate a peach."。



### 其他

#### 自动的动画效果

当状态属性发生改变时, 我们希望对应控制的视图元素的变化具有动画效果, 那么可以指定: e.g.

```swift
Circle()
    .frame(maxHeight: 200)
    .foregroundColor( isOn ? .purple : .mint 
    .shadow(color:isOn ? .indigo : .orange , radius: 20)
    .scaleEffect(isOn ? 1: 0.75)
    .animation( .default, value: isOn)
```

> 其中的 `value： isOn`表示追踪的状态属性.



在一个视图中创建状态对象,  然后在 `app`中声明为环境变量并在子视图中使用.



# 设计原则

principle 



- 需要长按进行交互的组件, 在轻触时ICON放大或者缩小 





# 官方手册学习记录

## 基础知识

- Swift 使用*字符串插值*将常量或变量的名称作为占位符包含在较长的字符串中，并提示 Swift 将其替换为该常量或变量的当前值。将名称包在括号中，并在左括号前用反斜杠进行转义：

  ```swift
  print("The current value of friendlyWelcome is \(friendlyWelcome)")
  // 打印 "The current value of friendlyWelcome is Bonjour！"
  ```

- 不必使用 `;`, 但是如果想在一行中编写多个独立语句，则*必须*使用分号：

  ```swift
  let cat = "🐱"; print(cat)
  // 打印 "🐱"
  ```

- 整数边界: 使用 min, max进行访问

  ```swift
  let minValue = UInt8.min  // minValue 等于 0，类型为 UInt8
  let maxValue = UInt8.max  // maxValue 等于 255，类型为 UInt8
  ```

- 类型别名: `typealias`

  ```swift
  typealias AudioSample = UInt16
  
  var maxAmplitudeFound = AudioSample.min
  // maxAmplitudeFound 现在为 0
  
  ```

### 元组

- 作用: 多个值组合成一个复合值

```swift
let http404Error = (404, "Not Found")
// http404Error 的类型为（Int，String），且等于（404，"Not Found"）
```

如果只需要元组的部分值，则在分解元组时使用下划线 (`_`) 忽略不需要的部分



- 分解元组

  ```swift
  let (statusCode, statusMessage) = http404Error
  print("The status code is \(statusCode)")
  // 打印 "The status code is 404"
  print("The status message is \(statusMessage)")
  // 打印 "The status message is Not Found"
  ```

- 可以在定义元组时为元组中的各个元素命名：

  ```swift
  let http200Status = (statusCode: 200, description: "OK")
  ```

  然后可以使用元素名访问:

  ```swift
  print("The status code is \(http200Status.statusCode)")
  // 打印 "The status code is 200"
  print("The status message is \(http200Status.description)")
  // 打印 "The status message is OK"
  ```

  

- 也可以直接使用从零开始的索引来访问, e.g. `http200Status.0`
  

---

- <u>可选</u>: 存储这种类型的值或者 `nil`.

- 提供后备值: `??`

  ```swift
  let name: String? = nil
  let greeting = "Hello, " + (name ?? "friend") + "!"
  print(greeting)
  // 打印 "Hello, friend!"
  ```

  - 如果 `??` 之前的值不是 `nil`, 就会正常解包, 否则选择后备值;
  - 使用 `()` 包裹.

- <u>隐式解包可选</u>: 安全假定一直都有值时使用

  ```swift
  let possibleString: String? = "An optional string."
  let forcedString: String = possibleString! // 需要显式解包
  
  let assumedString: String! = "An implicitly unwrapped optional string."
  let implicitString: String = assumedString // 隐式解包
  ```



---

### 错误处理

函数在声明中包含 `throws` 关键字，表示它可以抛出错误。调用可以抛出错误的函数时，要在表达式前加上 `try` 关键字.

Swift 会自动将错误传播到当前作用域之外，直到它们被 `catch` 子句处理为止。

```swift
do {
    try canThrowAnError()
    // 无错误的情况
} catch {
    // 抛出错误的情况
}
```

> 细节部分在后面补充



### 断言和先决条件

#### 使用断言进行调试

```swift
let age = -3
assert(age >= 0, "A person's age can't be less than zero.")
// 该断言失败的原因是 -3 并不 >= 0。
```

断言的第一个参数是预期的、正确的结果, 如果不满足条件就会显示报错. 但是不会阻止程序继续运行.



#### 强制执行先决条件

当条件有可能为假，但*必须*为真才能继续执行代码时，请使用先决条件.

向该函数传递一个计算结果为 `true` 或 `false` 的表达式，以及一条在条件结果为 `false` 时显示的信息:

```swift
// 在下标的实现中...
precondition(index > 0, "Index must be greater than zero.")
```

---

## 运算符

### 基本运算符

- 与 C 和 Objective-C 中的赋值运算符不同，Swift 中的赋值运算符本身不返回值。以下语句无效：

  ```swift
  if x = y { // 这是无效的，因为 x = y 不返回值。
  }
  ```

  - 可以防止不小心使用赋值运算符（=） 而非等于运算符（==）.

- 基本的四则运算不允许值的溢出.



> [!NOTE]
>
> 在 Swift 中对负数的处理与模运算符有所不同:
>
> 为了确定 `a % b` 的答案，`%` 运算符计算以下等式并返回 `余数` 作为输出：
>
> ```
> a` = (`b` x `某个乘数`) + `余数
> ```
>
> 其中 `某个乘数` 是 `b` 在 `a` 中能容纳的最大倍数。
>
> ```swift
> 9 % 4    // 等于 1
> 
> -9 % 4   // 等于 -1
> ```



- 数值的正负号可以使用前缀 `-` 切换，称为<u>一元负号运算符</u>.

  - 中间没有任何空格.

    ```swift
    let three = 3
    let minusThree = -three       // minusThree 等于 -3
    ```

#### 元组的计算

- 前提: 如果两个元组具有相同的类型和相同数量的值，则可以比较它们.
- 规则: 
  - 元组是从左到右逐个值进行比较的，直到比较发现两个不相等的值为止。
  -  这两个值将进行比较，并且该比较的结果决定了整个元组比较的结果。
  -  如果所有元素都相等，那么这两个元组本身就相等。

> [!NOTE]
>
> 只有当给定的运算符可以应用于各自元组中的每个值时，元组才能与该运算符进行比较.

```swift
("blue", false) < ("purple", true)  // 错误，因为 < 不能比较布尔值
```



#### 空合并运算符

`a ?? b`的结果与下面的运算相同:

```swift
a != nil ? a! : b
```



#### 区间运算

- *闭区间运算符*（`a...b`）定义了一个从 `a` 到 `b` 的范围，包括 `a` 和 `b` 的值。`a` 的值不能大于 `b`;

  - 在需要使用所有值的情况下很有用

  - e.g.

    ```swift
    for index in 1...5 {
        print("\(index) 乘以 5 等于 \(index * 5)") 
    }
    ```

- *半开区间运算符*（`a..<b`）定义了一个从 `a` 到 `b` 但不包括 `b` 的范围.

  - 对于处理从基数 0 开始的列表（如数组）时特别有用，因为它可以计数到列表长度（但不包括列表长度）.

  - e.g.

    ```swift
    let names = ["Anna", "Alex", "Brian", "Jack"] 
    let count = names.count
    for i in 0..<count {
        print("第 \(i + 1) 个人叫 \(names[i])")
    }
    ```

- 闭区间运算符有一种替代形式，用于一直延伸到尽可能远的区间 —— 例如，一个包含从索引 2 到数组末尾所有元素的区间。

  ```swift
  for name in names[2...] { print(name) }
  // Brian
  // Jack
  ```

- 半开区间运算符也有一种只写最后一个值的单侧形式

  ```swift
  for name in names[..<2] { print(name) }
  // Anna
  // Alex
  ```



#### 逻辑运算

> [!NOTE]
>
> Swift 逻辑运算符 `&&` 和 `||` 遵循<u>从左到右</u>的结合顺序，这意味着带有多个逻辑运算符的复合表达式会首先评估最左边的子表达式.





## 控制流

## 协议

- 如果类需要继承, 需要将父类写在所有的协议之前
- 不能在协议定义中为方法参数指定默认值。
- 协议也可以要求遵循协议的类型 <u>实现指定的构造器</u>, 和协议内部的方法一样, 不需要写花括号和构造期的实体
  - 如果是类, 必须在构造函数的开头加上 `required`修饰符. 这是为了确保所有继承的子类也提供这个构造函数的实现, 从而确保遵守协议;
  - 但是如果一个类被声明为了 `final`, 也就是无法被继承, 那么就不需要 `required`的声明





### 协议的基本用法

协议可以规定属性类型以及属性被操作的权限, 通常和类、结构体和枚举进行绑定, 作为一种强制的约束.

```swift
protocol Tax{
  var national: Double { get } 
  var individual: Double{ set get}
}
```

同样可以在协议中使用 `mutating` 来声明一个改变自身属性的方法:

```swift
protocol Tax{
  var national: Double { get } 
  var individual: Double{ set get}
  
  mutating func changeTax(newValue: Double)
}
```

- 需要注意的是, 结构体内部的方法如果要修改自身属性, 也需要声明 `mutating`, 但是类则不需要额外的声明.

  ```swift
  struct Taxas: Tax{
     var national: Double
    var individual: Double
    
    mutating func changeTax(newValue: Double){
      national = newValue
    }
  }
  ```



### 补充协议

- 使用 `,` 连接不同的协议

- `extension`可以为协议的函数设置默认方法, 就不需要继续在每一个类、结构体或枚举中重新定义

  - 同样可以补充数据类型

    ```swift
    extension Int {
      var abs: Int {
        get {
          if self >= 0 {
            return self
          }else{
            return -self
          }
        }
      }
    }
    
    print((-3).abs);
    // 3
    ```



#### 有条件地遵循协议

让 `Array` 类型只要在存储遵循 `TextRepresentable` 协议的元素时，就遵循 `TextRepresentable` 协议:

```swift
extension Array: TextRepresentable where Element: TextRepresentable {
    var textualDescription: String {
        let itemsAsText = self.map { $0.textualDescription }
        return "[" + itemsAsText.joined(separator: ", ") + "]"
    }
}
let myDice = [d6, d12]
print(myDice.textualDescription)
// 打印 "[A 6-sided dice, A 12-sided dice]"
```



#### 扩展里声明协议遵循

当一个类型已经遵循了某个协议中的所有要求，却还没有声明遵循该协议时，可以通过空的扩展来让它遵循该协议:

```swift
struct Hamster {
    var name: String
    var textualDescription: String {
        return "A hamster named \(name)"
    }
}
extension Hamster: TextRepresentable {}
```



### Error handling 

系统提供了 `Error`协议用于错误处理, 主动给予错误的捕捉情况.

使用方法:

1. 定义遵循相关协议的枚举类型, 作为错误的类型;
2. 定义可能抛出错误的函数;
3. 使用 `do...catch`块来结构化地处理错误.

e.g.

```swift
// 定义一个错误类型，遵循 Error 协议
enum PasswordError: Error {
    case tooShort
    case tooWeak
}

// 一个函数，可能抛出错误
func validate(password: String) throws {
    if password.count < 6 {
        throw PasswordError.tooShort
    }
    if password == "123456" {
        throw PasswordError.tooWeak
    }
}

// 使用 do-catch 捕捉错误
do {
    try validate(password: "123456")
    print("密码验证通过 ✅")
} catch PasswordError.tooShort {
    print("❌ 密码太短，请至少使用 6 个字符")
} catch PasswordError.tooWeak {
    print("❌ 密码太弱，不能使用简单的序列")
} catch {
    print("❌ 发生未知错误：\(error)")
}

```

> - throws：在函数声明中标注该函数会抛出错误;
> - try：在调用可能抛出错误的函数时使用.



### 其他的协议

#### CaseIterable

用于获取枚举的属性个数, 从而进行遍历.

```swift
enum Status:CaseIterable{
    case low,middle,high
    
    
    mutating func change(){
        switch self{
        case .high:
            self = .low
        case .low:
            self = .middle
        case .middle:
            self = .high
        }
    }
    
    func ptr(){
        print("Current Status is \(self)")
    }
}

var status = Status.low
for _ in 0..<Status.allCases.count{
    status.ptr()
    status.change()
}
```

> `for _ in 0..<` 当中的 `_`表示忽略遍历时候的循环变量的值.





## 闭包

#### 闭包的简化推导

相当于匿名函数与 `lambda`.  接下来从普通函数的写法开始简化:

- 普通函数: 

  ```swift
  func changeSign(op: Double) -> Double {
      return -op
  }
  
  var operation: (Double) -> Double
  operation = changeSign
  
  let result = operation(4.0) // result = -4.0
  ```

- 将函数的定义下移:

  ```swift
  var operation: (Double) -> Double
  operation = (op:Double) -> Double { return -op}
  
  let result  = operation(4.0)
  ```

- 将`｛` 提前，并在原来的位置添加 `in`

  ```swift
  var operation:(Double) -> Double
  operation = {(op: Double) -> Double  in return -op}
  ...
  ```

- 系统可以推断类型, 所以根据输入的类型简化返回值的类型定义

  ```swift
  var operation:(Double) -> Double
  operation = {(op: Double)   in return -op}
  ...
  ```

- 可以进一步省略传入的类型

  ```swift
  var operation:(Double) -> Double
  operation = { (op) in return -op}
  ...
  ```

- 省略返回的标记 `return`:

  ```swift
  var operation:(Double) -> Double
  operation = { (op) in  -op}
  ...
  ```

最后, 我们可以直接用 `$0`等替代传入的参数, 也就是省略了参数的名称!

```swift
var operation:(Double) -> Double
operation = { -$0 }
...
```



#### 闭包的常见使用

e.g. 

![image-20250412170605776](swift.assets/image-20250412170605776.png)

#### Trailing Closure

当闭包是函数的**最后一个参数**时，

```swift
let result = applyTwice(3, operation: { $0 * 2 })
```

可以改写成:

```swift
let result = applyTwice(3) { $0 * 2 } // result = 12
```



# 慕课学习杂记

#### something

- 去官网学习新出现的技术
  - codeML
  - 张克俊老师?



- 函数也可以赋值给变量 



#### 省略外部参数名

- 外部参数名: 在函数调用的时候使用, 提高可读性;
- 内部参数名: 在函数体的内部使用



如果如此定义:

```swift
func greet(person name: String){
  print("Hello , \(name)")
}
```

那么在调用的时候必须显示声明外部参数名:

```swift
greet( person: "Alice")
```



如果我们希望省略外部参数名, 就可以在定义函数的时候用 `_`来代替:

```swift
func greet(_ name: String){
  ...
}
```



#### 高阶函数

我们可以让函数作为另一个函数的输入参数:

```swift
func addTwoInts( _ a: Int, _ b: Int) -> Int{
  return a+b
}

var mathFunction = addTwoInts

// 高阶函数
func printMathResults（_ mathFunction: (Int, Int）-> Int, _ a: Int, _ b: Int){
  var result = mathFunction(a,b)
  print(result)
}

// 调用
printMathResults（addTwoInts， 3,5）

```

> 注意函数作为参数的时候, 类型的定义就是输入类型和返回类型, 也是用 `,` 来分隔不同的参数.



---



#### 内置的库

`AVFoudation`: 音频播放 



---



- 枚举内部也可以设置方法; 

- 结构体本身不需要构造函数(因为swift存在对于结构体的默认构造) , 但是如果结构体内部的属性存在这样的属性:

  - 它可能是枚举内部的方法, 跟枚举的属性有关, 可能使用了 `switch`来根据枚举属性赋值.

  ```swift
  enum Type{
    case Cike
    case ...
    
    func blood()-> Double{
      switch self{
        case .Cike: return 10
        case .Fashi: return ...
        ...
      }
    }
    
  }
  
  struct Card {
    var country: Country
    vat type: Type
    var blood: Double
    
    init (country: Country, type: Type){
      self.country = country
      self.type = type;
      blood = type.blood
    }
  }
  ```



- 结构体和枚举属于 <u>值类型</u>, 如果赋值的时候进行拷贝操作;
  - 如果结构体声明为 `let`, 即使属性是变量, 那么也无法修改内部的属性,
- 类是引用类型, 赋值的时候使得左值指向了同样的内存区域, 也就是信息保持一致, 更改同步
  - 如果我们将类声明为常量, 相当于cpp的指针常量, 也就是说类内部的属性可以更改, 但是无法修改这个量指向的内存区域.



#### 计算属性

访问的时候动态计算得到.

下面通过一个矩形的例子来说明:

```swift
struct Point {
  var x = 0.0
  var y = 0.0
}

struct Size{
  var width = 0.0
  var height = 0.0
}

struct Rect{
  var origin = Point()
  var size = Size()
  var center: Point{
    get{
      let centerX = origin.x + (size.width / 2)
      let centerY = orgin.y + (size.height /2 )
      return Point(x: centerX, y: centerY)
    }
    set(newCenter){
      origin.x = newCenter.x - (size.width / 2)
      origin.y = newCenter.y - (size.height / 2)
    }
  }
}
```

上述完成了结构体的计算属性的定义, 其中 `get`部分也可以优化为:

```swift
get{
	Point(x: origin.x + (size.width / 2),
        y: orgin.y + (size.height /2 ) )
}
```

> 这是因为, 如果 `get` 部分只存在一个表达式,  就会自动将其返回, 不需要显式声明 `return`关键字.

然后可以如此应用:

```swift
var currCenter = rectElement.center

rectElement.center = Point(x:20, y:50)
```



`set`提供了语法糖, 也就是可以直接访问oldValue 和  newValue, 因此我们可以如此改写:

```swift
set{
  origin.x = newValue.x - (size.width / 2)
  origin.y = newValue.y - (size.height / 2)
}
```



> [!NOTE]
>
> 对于只读的计算属性, 由于不存在 `set`, 我们可以直接在花括号内定义返回内容.



---

- 声明 `static` , 表示这个属性或者方法属于整个类型而非某个实例. 此时相应的, 我们使用 `<tyepName>.<strtic attribute>`的方式来访问.



- 子面量本身是不可修改的, 下面的拓展中, 如果写作  `var someInt = 3.square()`就会报错

```swift
extension Int{
    mutating func square(){
        self = self * self
    }
}

var someInt = 3
someInt.square()
```



---

控制器存在5种状态:

- 未加载
- 将要出现
- 出现
- 将要消失
- 已经消失



APP的状态:

<img src="swift.assets/image-20250413182851979.png" alt="image-20250413182851979" style="zoom:33%;" />

`UiSceneDelegate`用于响应基于 `scene` 的生命周期事件.



可以使用属性的 `didset`来便捷地检测变化并快速修改:

```swift
var score = 0{
  didSet{
    self.gameScoreLabel.text = "Score: \(score)"
  }
}
```



- `viewController`是新的页面;
- `view`是视图, 可以叠加在页面上.
- `UIAlertController`组件相当于警示的弹窗组件



### 作图和绘画

#### CG

- `CGFloat`用于二维坐标系中的坐标数据:

  - `let coor_x = CGFloat(10.5)`

- `CGPoint(x: .. , y:...)`

- `CGSize` 包含width和height属性的结构体;

- `CGRect`包含点和尺寸的矩形

  ```swift
  struct CGRect{
    var origin: CGPoint
    var size: CGSize
  }
  ```

  - 其他属性

  - e.g.

    ```swift
    var minX: CGPoint
    var midY: CGPoint
    intersects(CGRect) -> Bool // 判断是否存在交集
    contains(CGPoint) -> Bool // 是否包含点.
    ```



最小单元是 `Point`而非像素点.

bound表示视图内部允许绘制的区域:

```swift
var bounds: CGRect // 也就是一个矩形
```

frame	视图在父视图中的位置:

```swift
var frame: CGRect
```



#### 自定义视图

绘制自定义视图通常通过创建一个自定义的 UIView 子类，并重写 draw(_:) 方法来实现.

playGround中的实例:

```swift
import UIKit
import PlaygroundSupport

class CustomView: UIView {
    override init(frame: CGRect) {
        super.init(frame: frame)
        self.backgroundColor = .white // 设置背景颜色
    }

    required init?(coder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }

    override func draw(_ rect: CGRect) {
        guard let context = UIGraphicsGetCurrentContext() else { return }

        // 绘制一个圆形
        context.setFillColor(UIColor.blue.cgColor) // 设置填充颜色
        context.fillEllipse(in: rect) // 绘制填充的圆

        // 绘制一个矩形
        context.setStrokeColor(UIColor.black.withAlphaComponent(0.6).cgColor) // 设置能见度
        context.setLineWidth(5)
        context.stroke(rect.insetBy(dx: 10, dy: 10)) // 绘制矩形边框，留出间距
    }
}

// 创建自定义视图实例
let customView = CustomView(frame: CGRect(x: 0, y: 0, width: 200, height: 200))

// 显示在 Playground 的 live view
PlaygroundPage.current.liveView = customView

```



- `touchesBegan`--WWDC
- `SCNVector3`是三维向量
- `DispathchQueue`表示创建一个异步的进程



### 传感器

- 加速度的方向伴随手机的头部旋转保持不变;
  - 以 `g` 为描述单位;
  - 面向使用者的方向是 `z` 轴.
- 陀螺仪
  - 记录对应用三个轴
  - roll, pitch, yaw



### 动画



## 结绳记事

- [操作演示](https://www.icourse163.org/learn/ZJU-1450024180?tid=1474143513#/learn/content?type=detail&id=1262245274&cid=1299836384&contentid=1217938866).

- 按住 `ctrl`将视图中的组件拖放到代码中.
- 右下角的几个功能:
  - 约束;
  - 选择视图 
- `ctrl + option + cmd + enter` 快速显示代码区域
- [组件使用](https://www.icourse163.org/learn/ZJU-1450024180?tid=1474143513#/learn/content?type=detail&id=1262245274&cid=1299836386&contentid=1217092922)
  - 50:00 左右介绍了两种类型和交互

- 按住`optional`然后 hover 在类上, 可以显示对应的基础操作.

- 可选值的本质是枚举类型!

  ```swift
  enum Optional<T>{
    case none
    case some(<T>)
  }
  ```

- [画图和动画](https://www.icourse163.org/learn/ZJU-1450024180?tid=1474143513#/learn/content?type=detail&id=1262245305&sm=1)

- [coreML](https://www.icourse163.org/learn/ZJU-1450024180?tid=1474143513#/learn/content?type=detail&id=1262245309&sm=1)

- 在项目中显示Md格式:

  ```swift
  /*:
  ...
  */
  ```

- 选择在运行时隐藏实际存在的代码:

  ```swift
  //#-hidden-code
  import PlaygroundSupport
  ...
  //#-end-hidden-code
  
  ```

  





# Swift UI

- [慕课的链接](https://www.icourse163.org/learn/ZJU-1450024180?tid=1474143513#/learn/content?type=detail&id=1262245315&cid=1299836440&contentid=1218091551)
- [by now](https://www.hackingwithswift.com/100/swiftui/16)
- SixD: 开箱即用的UI设计等.
- [术语表](https://www.hackingwithswift.com/glossary)



## AR

- [helpful links](https://www.createwithswift.com/creating-an-augmented-reality-app-in-swiftui-using-realitykit-and-arkit/)
- [官方文档](https://developer.apple.com/documentation/realitykit/?ref=createwithswift.com)



## 动画

#### 缩放变换

```swift
NavigationLink{
  BraceletEditor(bracelet)
  .navigationTransitionStyle(
  .zoom(
  	sourceID:bracelet.id,
  	in:braceletList
  	)
  )
}label:{
  BraceletPreview(bracelet)
}
.matchedTansitionSource(
	id:bracelet.id,
  in:braceletList
)
```





## Symbol 6

### 动画

- 使用 `晃动` 在复杂的UI中提示可交互性.
- `旋转`动画来表示正在进行的进程

## 基本语法

#### 磨砂效果

```swift
VStack {
}
.frame(width: 200, height: 200)
.background(.ultraThinMaterial, in: RoundedRectangle(cornerRadius: 20, style: .continuous))
```



#### 搜索栏

- 状态管理:

  ```swift
  // 存储搜索文本
  @State private var searchText = ""
  
  // 可选：跟踪搜索是否处于活动状态
  @State private var isSearching = false
  ```

- 数据过滤模式

  ```swift
  // 基本过滤计算属性模板
  var filteredItems: [ItemType] {
      if searchText.isEmpty {
          return originalItems
      } else {
          return originalItems.filter { item in
              // 根据需要自定义过滤条件
              item.name.localizedCaseInsensitiveContains(searchText) ||
              item.description.localizedCaseInsensitiveContains(searchText)
          }
      }
  }
  
  // 处理嵌套数据结构的过滤模板
  var filteredNestedItems: [ParentType] {
      if searchText.isEmpty {
          return originalParentItems
      } else {
          return originalParentItems.compactMap { parent in
              let matchedChildren = parent.children.filter { child in
                  child.name.localizedCaseInsensitiveContains(searchText)
              }
              
              if matchedChildren.isEmpty {
                  return nil
              } else {
                  // 创建包含匹配子项的新父项
                  return ParentType(id: parent.id, name: parent.name, children: matchedChildren)
              }
          }
      }
  }
  ```

- 搜索UI中的实现:

  ```swift
  NavigationStack {
      List {
          // 使用过滤后的数据源
          ForEach(filteredItems) { item in
              // 列表项视图
          }
      }
      .navigationTitle("标题")
      .searchable(text: $searchText, prompt: "搜索提示文字")
      // 可选：添加搜索建议
      .searchSuggestions {
          ForEach(suggestions, id: \.self) { suggestion in
              Text(suggestion).searchCompletion(suggestion)
          }
      }
  }
  ```





#### 左右适应的外边距

通过 `HStack`与  `space`实现卡片的自适应扩展, 同时利用 `.frame(maxwidth:...)`来设置一个最大的卡片宽度

```swift
HStack{
    Spacer(minLength: 10)
  
    Text(item.description)
        .padding()
        .background(Color(.systemGray6))
        .overlay(
            RoundedRectangle(cornerRadius: 10) // 10为圆角半径，可调整
                .stroke(Color.gray, lineWidth: 1) // 边框颜色和宽度
        )
        .clipShape(RoundedRectangle(cornerRadius: 10))
    // 保证背景和边框都圆角
    .frame(maxWidth: 400) // 最大宽度限制

    Spacer(minLength: 10)
}
```

`Spacer(minLength: 10)` 表示**保证自己不会小于 minLength**. 



上述的 `Spacer`会压缩卡片的内容, 如果希望直接设置卡片在父容器中的左右外边距, 应该在卡片的内部使用 `padding`:

```swift
Text(item.description)
    .padding(.horizontal, 24) // 卡片内容内边距
    .padding(.vertical, 12)
    .background(Color(.systemGray6))
    .overlay(
        RoundedRectangle(cornerRadius: 10)
            .stroke(Color.gray, lineWidth: 2)
    )
    .clipShape(RoundedRectangle(cornerRadius: 10))
    .padding(.horizontal, 20) // 整个卡片距离父视图左右20pt
```



#### 全局统一样式

```swift
@main
struct MyApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
                .buttonStyle(ShadowButtonStyle(radius: 10))
        }
    }
}
```



#### 参数标签和参数名

- 参数标签用于函数调用时;
- 参数名用于函数内部的参数名称.

e.g.:

```swift
func greet(person atName: String) {
    print("Hello, \(atName)!")
}
greet(person: "Alice") // 输出：Hello, Alice!
```



也可以使用 `_`来省略调用时的参数标签:

```swift
func greet(_ name: String) {
    print("Hello, \(name)!")
}
greet("Alice") // 输出：Hello, Alice!
```





#### Alert

swift UI中的弹窗提示同样通过本地的 `@State`变量来实现:

```swift
@State private var showingPaymentAlert = false
...
.alert("Order confirmed", isPresented: $showingPaymentAlert) {
    // add buttons here
} message: {
    Text("Your total was \(totalPrice) – thank you!")
}
```

设置按钮来改变可见的状态:

```swift
Button("Confirm order") {
    showingPaymentAlert.toggle()
}
```



#### 自定义绑定

我们也可以使用 `Binding` 类型手动创建绑定，该类型可以提供自定义 `get` 和 `set` 闭包，以便在读取或写入值时运行。





#### Foreach

```swift
ForEach(item.restrictions) { restriction in
    Text(restriction)
}
```

此时, 要求 `item.restrictions` 具有可唯一标识的 `id` 字段.

- 如果内容本身就是唯一标识, 比如说遍历的内容是字符串数组, 那么可以如此声明:

  ```swift
  ForEach(item.restrictions, id: \.self) { restriction in
      Text(restriction)
  }
  ```



#### Spacer()

用于填充剩余的空间



- 使用 `offset(x:.., y:...)`来调节位置, 左上角是原点.



#### 环境变量

- 作用: 用于存储独立于视图的、长期存在的数据;

- e.g.

  ```swift
  // App.swift
  @StateObject var order = Order()
  ```

- `@StateObject` 属性包装器负责在<u>应用程序的整个生命周期中</u>保持对象处于活动状态。

- 需要在创建视图结构体的时候传递:

  ```swift
  WindowGroup {
      ContentView()
          .environmentObject(order)
  }
  ```

- 为了让swift知道什么时候更新视图, 常用的是声明 `@Published`属性包装器——足以让它更新任何正在监视更改的 SwiftUI 视图.

- 同时声明对应的对象遵循可观测协议: **ObservableObject**.



我们可以使用 `@EnvironmentObject`来访问环境中的共享数据, 也就是传递上一步已经在父视图中创建和管理的对象.

e.g.

```swift
class UserData: ObservableObject {
    @Published var name: String = "John"
}

struct ContentView: View {
    @StateObject private var userData = UserData()

    var body: some View {
        ChildView().environmentObject(userData)
    }
}

struct ChildView: View {
    @EnvironmentObject var userData: UserData

    var body: some View {
        Text(userData.name)
    }
}
```



使用 `@State`来声明简单的本地值——比如整数和字符串.

- 建议将其声明为 `private`, e.g.

  ```swift
  @State private var paymentType = "Cash"
  ```

  

#### 菜单视图

为了将菜单视图存放在一个选项卡当中, 我们需要新建一个视图, 用来作为容器:

```swift
struct MainView: View {
    var body: some View {
        TabView {
            ContentView()
                .tabItem {
                    Label("Menu", systemImage: "list.dash")
                }

            OrderView()
                .tabItem {
                    Label("Order", systemImage: "square.and.pencil")
                }
        }
    }
}
```

> 页面级别的切换.



使用枚举与子页面的内容分区:

```swift
import SwiftUI

struct ContentView: View {
    enum Section {
        case cats
        case dogs
    }

    @State private var selectedTab = Section.cats

    var body: some View {
        TabView(selection: $selectedTab) {
            Tab("Cats", systemImage: "cat", value: .cats) {
                Button("Go to Dogs") {
                    selectedTab = .dogs
                }
            }
            
            Tab("Dogs", systemImage: "dog", value: .dogs) {
                Button("Go to Cats") {
                    selectedTab = .cats
                }
            }
        }
    }
}
```





## 合适的修饰符

- 图像自动调节尺寸:

  ```swift
  Image(item.mainImage)
      .resizable()
      .scaledToFit()
  ```



#### 设置阴影

```swift
.shadow(color: .black.opacity(0.2), 
        radius: 15, x: 0, y: 10)
```



#### 为按钮设置动画

```swift
struct ContentView: View {
    @State private var showingWelcome = false

    var body: some View {
        VStack {
            Toggle("Toggle label", isOn: $showingWelcome.animation())

            if showingWelcome {
                Text("Hello World")
            }
        }
    }
}
```

可以进一步设置, 比如弹簧的渐入渐出:

```swift
Toggle("Toggle label", isOn: $showingWelcome.animation(.spring()))
```



## 基本操作

#### 快捷键

- `ctrl`按住后点击  `VStack`可以快速地将其添加到 `ZStack`当中
  - 颜色的设置需要通过 `ZStack`来实现.
- `option`可以显示当前类的介绍
- 



学习路线:

1. 学习 Swift 基础语法
2. 上手 SwiftUI 基础控件（Text、Button、Image、List、NavigationStack 等）
3. 学会使用 @State、@Binding 等状态管理属性
4. 尝试做一个简单的 App（比如 ToDo List、小笔记本）
5. 慢慢接触更复杂的功能，比如动画、数据持久化（CoreData）、网络请求（URLSession）



#### **第二周：SwiftUI 基础入门**

**目标：**

- 理解 SwiftUI 的基本概念和架构。
- 掌握常用的 SwiftUI 视图和修饰符。

1. **SwiftUI 概述：**
   - 声明式语法与传统 UIKit 的区别
   - SwiftUI 的视图层次结构
2. **常用视图组件：**
   - Text、Image、Button 等基本控件的使用
   - 视图修饰符的应用，如 .font()、.foregroundColor()、.padding() 等
3. **布局系统：**
   - 使用 HStack、VStack 和 ZStack 进行视图布局
   - Spacer 和 Divider 的使用

**推荐资源：**

- **Hacking with Swift 的 100 天 SwiftUI：** [100 Days of SwiftUI](https://www.hackingwithswift.com/100/swiftui)
- **AppCoda 的 SwiftUI 教程：** [AppCoda - Mastering SwiftUI](https://www.appcoda.com/swiftui/)

#### **第三周：状态管理与数据绑定**

**目标：**

- 理解并应用 SwiftUI 的状态管理机制。
- 掌握数据绑定和视图更新的原理。

**学习内容：**

1. **状态管理：**
   - 使用 @State 管理视图内部状态
   - 使用 @Binding 在父子视图之间传递状态
   - 使用 @ObservedObject 和 @EnvironmentObject 管理复杂数据模型
2. **数据绑定：**
   - 双向绑定的实现
   - 数据变化如何驱动视图更新

**推荐资源：**

- **SwiftUI 官方文档：** [SwiftUI Documentation](https://developer.apple.com/documentation/swiftui)
- **Design+Code 的 SwiftUI 教程：** [Design+Code - SwiftUI for iOS 17](https://designcode.io/swiftui)



#### **第四周：导航、列表与高级交互**

**目标：**

- 掌握 SwiftUI 中的导航和列表视图。
- 实现复杂的用户交互和动画效果

**学习内容：**

1. **导航与路由：**
   - 使用 NavigationView 和 NavigationLink 实现页面导航
   - 传递数据到下一个视图
2. **列表视图：**
   - 使用 List 展示数据集合
   - 自定义列表行和支持删除、移动操作
3. **动画与手势：**
   - 添加基本动画效果
   - 处理手势交互，如拖拽、缩放等

**推荐资源：**

- **Raywenderlich 的 SwiftUI 教程：** [Raywenderlich - SwiftUI Apprentice](https://www.raywenderlich.com/books/swiftui-apprentice)
- **Big Mountain Studio 的免费电子书：** [SwiftUI Views Quick Start](https://www.bigmountainstudio.com/free-swiftui-book)

