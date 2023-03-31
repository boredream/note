start: 2022-08-01

end:  2022-08-02



# Swift 基础

http://www.swift51.com/swift5.1/



## 元组 Tuples

多个值组合 (Int, String, ...)，类似匿名对象类

```swift
// 申明
let http404Error = (404, "Not Found")
let http401Error = (code: 401, msg: "Auth Error")

// 使用
// 申明变量接受，不需要的可以_
let (code, _) = http404Error
print("code is \(code)")
// 角标访问
print("message is \(http404Error.1)")
// 名称访问
print("code is \(http401Error.code)")
```



## 可选类型 Optionals

本质是一个泛型，有值\<T>和无值nil



可能为nil的变量

```swift
// nil 代表缺省值，跟 Java 里代表空指针的 null 不一样
var code: Int? = 404
code = nil

// 默认 nil
var msg: String?
```



### 可选绑定 Optional binding

```swift
// 如果 Int() 返回的可选Int包含值，则赋值且进if。否则 nil 进 else
if let actualNumber = Int(possibleNumber) {
    print("\'\(possibleNumber)\' has an integer value of \(actualNumber)")
} else {
    print("\'\(possibleNumber)\' could not be converted to an integer")
}
```



### 默认值

```swift
let defaultColorName = "red"
var userDefinedColorName: String?   //默认值为 nil

var colorNameToUse = userDefinedColorName ?? defaultColorName
// userDefinedColorName 的值为空，所以 colorNameToUse 的值为 "red"
```



### 隐式解析可选类型 !

可选类型每次判断效率低

```swift
let possibleString: String? = "An optional string."
let forcedString: String = possibleString! // 需要感叹号来获取值

let assumedString: String! = "An implicitly unwrapped optional string."
let implicitString: String = assumedString  // 不需要感叹号
```



## 区间运算符

闭区间运算符 （`a...b`）定义一个包含从 `a` 到 `b`（包括 `a` 和 `b`）的所有值的区间。`a` 的值不能超过 `b`。

半开区间运算符（`a..<b`）定义一个从 `a` 到 `b` 但不包括 `b` 的区间。

单侧区间运算符 （`a..`）（`..<b`）同上，但是一侧无限延伸。

```swift
// 作为索引
for index in 1...5 {
    // 循环1,2,3,4,5
}

// 作为变量
let range = ...5
range.contains(7)   // false
range.contains(4)   // true
range.contains(-1)  // true
```



## 字符串

### 拼接值

\\(xxx)

```swift
let multiplier = 3
let message = "\(multiplier) times 2.5 is \(Double(multiplier) * 2.5)"
```



### 多行字符

三引号分开

```swift
let quotation = """
The White Rabbit put on his spectacles.  "Where shall I begin,
please your Majesty?" he asked.

"Begin at the beginning," the King said gravely, "and go on
till you come to the end; then stop."
"""
```



### 扩展字符串分隔符

您可以将字符串文字放在扩展分隔符中，这样字符串中的特殊字符将会被直接包含而非转义后的效果。将字符串放在引号（`"`）中并用数字符号（`#`）括起来。例如，打印字符串文字 `#"Line 1 \nLine 2"#` 会打印换行符转义序列（`\n`）而不是给文字换行。

```swift
let threeMoreDoubleQuotationMarks = #"""
Here are three more double quotes: """
"""#
```



### 字符串索引

```swift
let greeting = "Guten Tag!"
greeting[greeting.startIndex]
// G
greeting[greeting.index(before: greeting.endIndex)]
// ! endIndex 是最后一位索引后面的位置，所以before: 正好获取最后一位
greeting[greeting.index(after: greeting.startIndex)]
// u
let index = greeting.index(greeting.startIndex, offsetBy: 7)
greeting[index]
// a


// 子字符串，搭配索引、区间运算符等获取
let greeting = "Hello, world!"
let index = greeting.firstIndex(of: ",") ?? greeting.endIndex
let beginning = greeting[..<index]
// beginning 的值为 "Hello"

// 把结果转化为 String 以便长期存储。因为子字符是单独类型SubString，会复用原String内存空间
let newString = String(beginning)
```



### NSAttributedString

类似Java的SpannableString





## 集合

### Arrays

```swift
// 构造
var someInts = [Int]()
print("someInts is of type [Int] with \(someInts.count) items.")
// 打印“someInts is of type [Int] with 0 items.”

var shoppingList: [String] = ["Eggs", "Milk"]
// shoppingList 已经被构造并且拥有两个初始项。

var threeDoubles = Array(repeating: 0.0, count: 3)
// threeDoubles 是一种 [Double] 数组，等价于 [0.0, 0.0, 0.0]

// 通过俩数组拼接新数组，相当于.addAll
var sixDoubles = threeDoubles + anotherThreeDoubles


// 添加
someInts.append(3)
shoppingList += ["Baking Powder"]
shoppingList += ["Chocolate Spread", "Cheese", "Butter"]
shoppingList.insert("Maple Syrup", at: 0)


// 删除
let mapleSyrup = shoppingList.remove(at: 0)
// mapleSyrup 常量的值等于被移除数据项
let apples = shoppingList.removeLast()


// 读取
shoppingList[0] = "Six eggs"
shoppingList[4...6] = ["Bananas", "Apples"]


// 遍历
for item in shoppingList {
    print(item)
}

for (index, value) in shoppingList.enumerated() {
  	// 索引+数据
    print("Item \(String(index + 1)): \(value)")
}

```



### Set

数据必须 Hashable

基本和Arrays一样，特殊点如下：

```swift
// 有序遍历，元素会按照 操作符< 排序后返回
for genre in favoriteGenres.sorted() {
    print("\(genre)")
}
```



提供并集、差集等操作

- 使用 `intersection(_:)` 方法根据两个集合的交集创建一个新的集合。
- 使用 `symmetricDifference(_:)` 方法根据两个集合不相交的值创建一个新的集合。
- 使用 `union(_:)` 方法根据两个集合的所有值创建一个新的集合。
- 使用 `subtracting(_:)` 方法根据不在另一个集合中的值创建一个新的集合。



可以判断不同包含关系

- 使用“是否相等”运算符（`==`）来判断两个集合包含的值是否全部相同。
- 使用 `isSubset(of:)` 方法来判断一个集合中的所有值是否也被包含在另外一个集合中。
- 使用 `isSuperset(of:)` 方法来判断一个集合是否包含另一个集合中所有的值。
- 使用 `isStrictSubset(of:)` 或者 `isStrictSuperset(of:)` 方法来判断一个集合是否是另外一个集合的子集合或者父集合并且两个集合并不相等。
- 使用 `isDisjoint(with:)` 方法来判断两个集合是否不含有相同的值（是否没有交集）。



### Dictionary 字典

相当于 Java 的 Map

```swift
// 构造
var namesOfIntegers = [Int: String]()
// namesOfIntegers 是一个空的 [Int: String] 字典

var airports: [String: String] = ["YYZ": "Toronto Pearson", "DUB": "Dublin"]


// 访问
airports["LHR"] = "London"

if let airportName = airports["DUB"] {
    print("The name of the airport is \(airportName).")
} else {
    print("That airport is not in the airports dictionary.")
}


// 修改
namesOfIntegers[16] = "sixteen"
// namesOfIntegers 现在包含一个键值对
namesOfIntegers = [:]
// namesOfIntegers 又成为了一个 [Int: String] 类型的空字典

if let oldValue = airports.updateValue("Dublin Airport", forKey: "DUB") {
    print("The old value for DUB was \(oldValue).")
}


// 删除
airports["APL"] = nil

if let removedValue = airports.removeValue(forKey: "DUB") {
    print("The removed airport's name is \(removedValue).")
} else {
    print("The airports dictionary does not contain a value for DUB.")
}


// 遍历
for (airportCode, airportName) in airports {
    print("\(airportCode): \(airportName)")
}

for airportCode in airports.keys {
    print("Airport code: \(airportCode)")
}

for airportName in airports.values {
    print("Airport name: \(airportName)")
}

```



## 控制流

### for

```swift
// 正常循环一个数组
for index in 0..<cardButtons.count {
  
}

// 以上等价于
for index in cardButtons.indices {
  
}
```



正常for只能以1的step循环。如果要自定义step，需要用到stride

```swift
for i in stride(from: 0.5, through: 15.25, by:0.3) {
  
}
// 类似java的 for(i=0.5; i<=15.25; i+=0.3)
```



### while

```swift
// 类似 do while
repeat {
    statements
} while condition
```



### Switch

#### 多语句 , 隔开，而不是多case

```swift
let anotherCharacter: Character = "a"
switch anotherCharacter {
case "a": // 无效，这个分支下面没有语句
case "A":
    print("The letter A")
default:
    print("Not the letter A")
}
// 这段代码会报编译错误

switch anotherCharacter {
case "a", "A":
    print("The letter A")
default:
    print("Not the letter A")
}
// 输出“The letter A”

// 如果想做到和Java一样贯穿case，可以使用关键字 fallthrough
let integerToDescribe = 5
var description = "The number \(integerToDescribe) is"
switch integerToDescribe {
case 2, 3, 5, 7, 11, 13, 17, 19:
    description += " a prime number, and also"
    fallthrough
default:
    description += " an integer."
}
print(description)
// 输出“The number 5 is a prime number, and also an integer.”
```



#### 区间匹配

```swift
let approximateCount = 62
let countedThings = "moons orbiting Saturn"
let naturalCount: String
switch approximateCount {
case 0:
    naturalCount = "no"
case 1..<5:
    naturalCount = "a few"
case 5..<12:
    naturalCount = "several"
case 12..<100:
    naturalCount = "dozens of"
case 100..<1000:
    naturalCount = "hundreds of"
default:
    naturalCount = "many"
}
print("There are \(naturalCount) \(countedThings).")
// 输出“There are dozens of moons orbiting Saturn.”
```



#### 元组匹配

支持元组，且可以用通配符忽略某字段_

```swift
let somePoint = (1, 1)
switch somePoint {
case (0, 0):
    print("\(somePoint) is at the origin")
case (_, 0):
    print("\(somePoint) is on the x-axis")
case (0, _):
    print("\(somePoint) is on the y-axis")
case (-2...2, -2...2):
    print("\(somePoint) is inside the box")
default:
    print("\(somePoint) is outside of the box")
}
// 输出“(1, 1) is inside the box”
```



#### 值绑定

case 里绑定变量

```swift
let anotherPoint = (2, 0)
switch anotherPoint {
case (let x, 0):
    print("on the x-axis with an x value of \(x)")
case (0, let y):
    print("on the y-axis with a y value of \(y)")
case let (x, y):
    print("somewhere else at (\(x), \(y))")
}
// 输出“on the x-axis with an x value of 2”


// where 用于新增额外条件
let yetAnotherPoint = (1, -1)
switch yetAnotherPoint {
case let (x, y) where x == y:
    print("(\(x), \(y)) is on the line x == y")
case let (x, y) where x == -y:
    print("(\(x), \(y)) is on the line x == -y")
case let (x, y):
    print("(\(x), \(y)) is just some arbitrary point")
}
// 输出“(1, -1) is on the line x == -y”
```



#### guard

类似if，但else里必须包含中断语句return / break / continue / throw

```swift
func greet(person: [String: String]) {
    guard let name = person["name"] else {
        return
    }

    print("Hello \(name)!")

    guard let location = person["location"] else {
        print("I hope the weather is nice near you.")
        return
    }

    print("I hope the weather is nice in \(location).")
}

greet(person: ["name": "John"])
// 输出“Hello John!”
// 输出“I hope the weather is nice near you.”
greet(person: ["name": "Jane", "location": "Cupertino"])
// 输出“Hello Jane!”
// 输出“I hope the weather is nice in Cupertino.”
```



## 函数

### 申明

```swift
func greet(person: String) -> String {
    let greeting = "Hello, " + person + "!"
    return greeting
}
```



### 参数默认值

函数调用时，可以用标签指定参数，也意味着调用函数时某些参数不用传入，因此也可以给参数加上默认值

```swift
func someFunction(paramWithoutDefault: Int, paramWithDefault: Int = 12) {
    // 如果你在调用时候不传第二个参数，paramWithDefault 会值为 12 传入到函数体中。
}
someFunction(paramWithoutDefault: 3, paramWithDefault: 6) // paramWithDefault = 6
someFunction(paramWithoutDefault: 4) // paramWithDefault = 12
```



### 多标签参数

可以给一个变量指定俩名称 [外部调用名称 函数内部使用名称: 类型]，空格隔开。

```Sw
func greet(person: String, from hometown: String) -> String {
    return "Hello \(person)!  Glad you could visit from \(hometown)."
}
print(greet(person: "Bill", from: "Cupertino"))
// 打印“Hello Bill!  Glad you could visit from Cupertino.”
```



### inout 输入输出参数

函数参数默认不可修改，但可以 inout 申明之代表可修改，且函数结束后，调用者传入参数的值也会修改。

```swift
func swapTwoInts(_ a: inout Int, _ b: inout Int) {
    let temporaryA = a
    a = b
    b = temporaryA
}

var someInt = 3
var anotherInt = 107
swapTwoInts(&someInt, &anotherInt)
print("someInt is now \(someInt), and anotherInt is now \(anotherInt)")
// 打印“someInt is now 107, and anotherInt is now 3”
```



### 函数类型

函数，也可以作为一个类型

```swift
var mathFunction: (Int, Int) -> Int = addTwoInts
```

这段代码可以被解读为：

”定义一个叫做 `mathFunction` 的变量，类型是‘一个有两个 `Int` 型的参数并返回一个 `Int` 型的值的函数’，并让这个新变量指向 `addTwoInts` 函数”。



因此，函数也可以作为另一个函数的参数

```swift
func addTwoInts(_ a: Int, _ b: Int) -> Int {
    return a + b
}

func printMathResult(_ mathFunction: (Int, Int) -> Int, _ a: Int, _ b: Int) {
    print("Result: \(mathFunction(a, b))")
}
printMathResult(addTwoInts, 3, 5)
// 打印“Result: 8”
```



同理函数也可以作为另一个函数的返回



### 嵌套函数

```swift
func chooseStepFunction(backward: Bool) -> (Int) -> Int {
    func stepForward(input: Int) -> Int { return input + 1 }
    func stepBackward(input: Int) -> Int { return input - 1 }
    return backward ? stepBackward : stepForward
}
```



## 闭包

http://www.swift51.com/swift5.1/02_language_guide/07_Closures.html

类似匿名函数Lambdas

```swift
{ (parameters) -> return type in
    statements
}
```



```swift
// 正常函数作为参数用法
func backward(_ s1: String, _ s2: String) -> Bool {
    return s1 > s2
}
var reversedNames = names.sorted(by: backward)

// 闭包写法
var reversedNames = names.sorted(by: { (s1: String, s2: String) -> Bool in
    return s1 > s2
})

// 进一步优化写法 根据上下文推断类型
var reversedNames = names.sorted(by: { s1, s2 in return s1 > s2 } )

// 进一步优化写法 隐式返回
var reversedNames = names.sorted(by: { s1, s2 in s1 > s2 } )

// 进一步优化写法 参数名称缩写
var reversedNames = names.sorted(by: { $0 > $1 } )

```



### 尾随闭包

如果将很长的闭包表达式作为最后一个参数传递给函数，可以把这个闭包替换成为尾随闭包的形式。尾随闭包是一个书写在函数圆括号之后的闭包表达式，函数支持将其作为最后一个参数调用。在使用尾随闭包时，你不用写出它的参数标签：

```swift
func someFunctionThatTakesAClosure(closure: () -> Void) {
    // 函数体部分
}

// 以下是不使用尾随闭包进行函数调用
someFunctionThatTakesAClosure(closure: {
    // 闭包主体部分
})

// 以下是使用尾随闭包进行函数调用
someFunctionThatTakesAClosure() {
    // 闭包主体部分
}

// 调用时，结合之前的优化，最终可以写为
var reversedNames = names.sorted { $0 > $1 }
```



### 值捕获

闭包可以在其被定义的上下文中捕获常量或变量。即使定义这些常量和变量的原作用域已经不存在，闭包仍然可以在闭包函数体内引用和修改这些值。

```swift
func makeIncrementer(forIncrement amount: Int) -> () -> Int {
    var runningTotal = 0
    func incrementer() -> Int {
        runningTotal += amount
        return runningTotal
    }
    return incrementer
}

// 使用
let incrementByTen = makeIncrementer(forIncrement: 10)
incrementByTen()
incrementByTen()
incrementByTen()
// 返回值30

// 如果创建了另一个函数，则它会有自己的引用，全新的一套
let incrementBySeven = makeIncrementer(forIncrement: 7)
incrementBySeven()
// 返回的值为7

// 闭包是引用类型，所以如果指针指向同一个，闭包里的值会继续
let alsoIncrementByTen = incrementByTen
alsoIncrementByTen()
// 返回值50
```



### 闭包逃逸

比如函数中有个参数是闭包，但闭包会在函数执行结束后才执行，如回调。则需要特殊标记。

```swift
var completionHandlers: [() -> Void] = []
func someFunctionWithEscapingClosure(completionHandler: @escaping () -> Void) {
    completionHandlers.append(completionHandler)
}
```



### 自动闭包 



## 枚举

```swift
enum CompassPoint {
    case north
    case south
    case east
    case west
}

enum Planet {
    case mercury, venus, earth, mars, jupiter, saturn, uranus, neptune
}

// 申明一次类型，已知后，可以省略枚举类
var directionToHead = CompassPoint.west
directionToHead = .south

// 支持switch
switch directionToHead {
case .north:
    print("Lots of planets have a north")
case .south:
    print("Watch out for penguins")
case .east:
    print("Where the sun rises")
case .west:
    print("Where the skies are blue")
}
// 打印“Watch out for penguins”
```



### 遍历

.allCases

```swift
enum Beverage: CaseIterable {
    case coffee, tea, juice
}
let numberOfChoices = Beverage.allCases.count
print("\(numberOfChoices) beverages available")
// 打印“3 beverages available”

for beverage in Beverage.allCases {
    print(beverage)
}
```



### 关联值

可以给预设值添加额外字段信息，且不同预设值支持不同数量类型的值

```swift
enum Barcode {
    case upc(Int, Int, Int, Int)
    case qrCode(String)
}

// 虽然预设值的关联值可以不同，但作为预设值，赋值给某个变量值只能是个整体
var productBarcode = Barcode.upc(8, 85909, 51226, 3)
productBarcode = .qrCode("ABCDEFGHIJKLMNOP")

switch productBarcode {
case .upc(numberSystem, manufacturer, product, check):
    print("UPC: \(numberSystem), \(manufacturer), \(product), \(check).")
case .qrCode(productCode):
    print("QR code: \(productCode).")
}
// 打印“QR code: ABCDEFGHIJKLMNOP.”
```



### 原始值

预设值可以是一种类型，可以手动设置原始值。如果不设置系统也会默认 

Int 按顺序递增1，第一个默认0，如果第一个默认设置为3，则后面依次3/4/5...

String 默认对应字符串

```swift
enum ASCIIControlCharacter: Character {
    case tab = "\t"
    case lineFeed = "\n"
    case carriageReturn = "\r"
}

enum Planet: Int {
    case mercury = 1, venus, earth, mars, jupiter, saturn, uranus, neptune
}
let earthsOrder = Planet.earth.rawValue
// earthsOrder 值为 3

enum CompassPoint: String {
    case north, south, east, west
}
let sunsetDirection = CompassPoint.west.rawValue
// sunsetDirection 值为 "west"
```



**使用原始值初始化枚举实例**

如果在定义枚举类型的时候使用了原始值，那么将会自动获得一个初始化方法，这个方法接收一个叫做 `rawValue` 的参数，参数类型即为原始值类型，返回值则是枚举成员或 `nil`。你可以使用这个初始化方法来创建一个新的枚举实例。

这个例子利用原始值 `7` 创建了枚举成员 `Uranus`：

```swift
let possiblePlanet = Planet(rawValue: 7)
// possiblePlanet 类型为 Planet? 值为 Planet.uranus
```



### 递归枚举

```swift
enum ArithmeticExpression {
    case number(Int)
    indirect case addition(ArithmeticExpression, ArithmeticExpression)
    indirect case multiplication(ArithmeticExpression, ArithmeticExpression)
}

func evaluate(_ expression: ArithmeticExpression) -> Int {
    switch expression {
    case let .number(value):
        return value
    case let .addition(left, right):
        return evaluate(left) + evaluate(right)
    case let .multiplication(left, right):
        return evaluate(left) * evaluate(right)
    }
}

let product = ArithmeticExpression.multiplication(sum, ArithmeticExpression.number(2))
print(evaluate(product))
// 打印“18”
```



## 类和结构体

### 结构体和类对比

Swift 中结构体和类有很多共同点。两者都可以：

- 定义属性用于存储值
- 定义方法用于提供功能
- 定义下标操作用于通过下标语法访问它们的值
- 定义构造器用于设置初始值
- 通过扩展以增加默认实现之外的功能
- 遵循协议以提供某种标准功能

与结构体相比，类还有如下的附加功能：

- 继承允许一个类继承另一个类的特征
- 类型转换允许在运行时检查和解释一个类实例的类型
- 析构器允许一个类实例释放任何其所被分配的资源
- 引用计数允许对一个类的多次引用



主要区别：

* 结构体是值类型，类是引用类型
* 结构体无继承，类有



### 结构体和枚举是值类型

因此拷贝时，不是公用一个对象，而是复制值过去。

```swift
let hd = Resolution(width: 1920, height: 1080)
var cinema = hd

cinema.width = 2048

print("cinema is now  \(cinema.width) pixels wide")
// 打印 "cinema is now 2048 pixels wide"

print("hd is still \(hd.width) pixels wide")
// 打印 "hd is still 1920 pixels wide"
```



### 恒等运算符

===：引用地址相同。类似java的==

==：值相同。类似java的equals

- 相同（`===`）
- 不相同（`!==`）



## 属性

类或结构体的成员变量都叫属性

* var实例 and var变量可修改
* 其他情况 let实例 or let变量都不可修改



### 延时加载存储属性

*延时加载存储属性* 是指当第一次被调用的时候才会计算其初始值的属性。在属性声明前使用 `lazy` 来标示一个延时加载存储属性。

必须是 var

不能添加观察者如 { didSet { ... } }



### 计算属性

除存储属性外，类、结构体和枚举可以定义*计算属性*。计算属性不直接存储值，而是提供一个 getter 和一个可选的 setter，来间接获取和设置其他属性或变量的值。必须是 var

```swift
struct Point {
    var x = 0.0, y = 0.0
}
struct Size {
    var width = 0.0, height = 0.0
}
struct Rect {
    var origin = Point()
    var size = Size()
    var center: Point {
        get {
            let centerX = origin.x + (size.width / 2)
            let centerY = origin.y + (size.height / 2)
            return Point(x: centerX, y: centerY)
        }
        set(newCenter) {
            origin.x = newCenter.x - (size.width / 2)
            origin.y = newCenter.y - (size.height / 2)
        }
    }
}
var square = Rect(origin: Point(x: 0.0, y: 0.0),
    size: Size(width: 10.0, height: 10.0))
let initialSquareCenter = square.center
square.center = Point(x: 15.0, y: 15.0)
print("square.origin is now at (\(square.origin.x), \(square.origin.y))")
// 打印“square.origin is now at (10.0, 10.0)”


// getter setter优化写法
        get {
            Point(x: origin.x + (size.width / 2),
                  y: origin.y + (size.height / 2))
        }
        set {
            origin.x = newValue.x - (size.width / 2)
            origin.y = newValue.y - (size.height / 2)
        }
```



### 只读计算属性

只读计算属性的声明可以去掉 `get` 关键字和花括号：

```swift
struct Cuboid {
    var width = 0.0, height = 0.0, depth = 0.0
    var volume: Double {
        return width * height * depth
    }
}
let fourByFiveByTwo = Cuboid(width: 4.0, height: 5.0, depth: 2.0)
print("the volume of fourByFiveByTwo is \(fourByFiveByTwo.volume)")
// 打印“the volume of fourByFiveByTwo is 40.0”
```



### 属性观察器

可以为属性添加其中一个或两个观察器：

- `willSet` 在新的值被设置之前调用，参数默认newValue
- `didSet` 在新的值被设置之后调用，参数默认oldValue



### 属性包装器

为了一些特殊属性规则的复用，封装起来

```swift
@propertyWrapper
struct TwelveOrLess {
    private var number = 0
    var wrappedValue: Int {
        get { return number }
        set { number = min(newValue, 12) }
    }
}

struct SmallRectangle {
    @TwelveOrLess var height: Int
    @TwelveOrLess var width: Int
}

var rectangle = SmallRectangle()
print(rectangle.height)
// 打印 "0"

rectangle.height = 10
print(rectangle.height)
// 打印 "10"

rectangle.height = 24
print(rectangle.height)
// 打印 "12"


struct SmallRectangle {
    private var _height = TwelveOrLess()
    private var _width = TwelveOrLess()
    var height: Int {
        get { return _height.wrappedValue }
        set { _height.wrappedValue = newValue }
    }
    var width: Int {
        get { return _width.wrappedValue }
        set { _width.wrappedValue = newValue }
    }
}
```



### 设置被包装属性的初始值

```swift
@propertyWrapper
struct SmallNumber {
    private var maximum: Int
    private var number: Int

    var wrappedValue: Int {
        get { return number }
        set { number = min(newValue, maximum) }
    }

    init() {
        maximum = 12
        number = 0
    }
    init(wrappedValue: Int) {
        maximum = 12
        number = min(wrappedValue, maximum)
    }
    init(wrappedValue: Int, maximum: Int) {
        self.maximum = maximum
        number = min(wrappedValue, maximum)
    }
}


// 普通创建
struct ZeroRectangle {
    @SmallNumber var height: Int
    @SmallNumber var width: Int
}
var zeroRectangle = ZeroRectangle()
print(zeroRectangle.height, zeroRectangle.width)
// 打印 "0 0"


// 单值初始化
struct UnitRectangle {
    @SmallNumber var height: Int = 1
    @SmallNumber var width: Int = 1
}
var unitRectangle = UnitRectangle()
print(unitRectangle.height, unitRectangle.width)
// 打印 "1 1"


// 多个值构造初始化，可以结合上面单值初始化方式一起
struct NarrowRectangle {
    @SmallNumber(wrappedValue: 2, maximum: 5) var height: Int
    @SmallNumber(wrappedValue: 3, maximum: 4) var width: Int
}

var narrowRectangle = NarrowRectangle()
print(narrowRectangle.height, narrowRectangle.width)
// 打印 "2 3"

narrowRectangle.height = 100
narrowRectangle.width = 100
print(narrowRectangle.height, narrowRectangle.width)
// 打印 "5 4"
```



### 从属性包装器中呈现一个值

除了被包装值，属性包装器可以通过定义被呈现值暴露出其他功能。访问是以 $ 开头的属性。

```swift
@propertyWrapper
struct SmallNumber {
    private var number = 0
    var projectedValue = false
    var wrappedValue: Int {
        get { return number }
        set {
            if newValue > 12 {
                number = 12
                projectedValue = true
            } else {
                number = newValue
                projectedValue = false
            }
        }
    }
}
struct SomeStructure {
    @SmallNumber var someNumber: Int
}
var someStructure = SomeStructure()

someStructure.someNumber = 4
print(someStructure.$someNumber)
// 打印 "false"

someStructure.someNumber = 55
print(someStructure.$someNumber)
// 打印 "true"
```



### 全局变量和局部变量

计算属性和观察属性所描述的功能也可以用于*全局变量*和*局部变量*。

* 全局变量是在函数、方法、闭包或任何类型之外定义的变量。
* 局部变量是在函数、方法或闭包内部定义的变量。

前面章节提到的全局或局部变量都属于*存储型变量*，跟存储属性类似，它为特定类型的值提供存储空间，并允许读取和写入。

另外，在全局或局部范围都可以定义*计算型变量*和为存储型变量定义观察器。计算型变量跟计算属性一样，返回一个计算结果而不是存储值，声明格式也完全一样。

> 注意
>
> 全局的常量或变量都是延迟计算的，跟 [延时加载存储属性](http://www.swift51.com/swift5.1/02_language_guide/10_Properties.html#lazy-stored-properties) 相似，不同的地方在于，全局的常量或变量不需要标记 `lazy` 修饰符。
>
> 局部范围的常量和变量从不延迟计算。



### 类型属性

static修饰，和java一样，普通的属性每个实例一份，static的属性结构体、枚举、类下面的属性都是同一个。



### 单例

```swift
struct Profile {
    var username: String
    static let `default` = Profile(username: "g_kumar")
}

// 使用
var profile = Profile.default
```



## 方法

### 实例方法

self代表所属实例，类似java的this



### 在实例方法中修改值类型

结构体和枚举是*值类型*。默认情况下，值类型的属性不能在它的实例方法中被修改。

但是，如果你确实需要在某个特定的方法中修改结构体或者枚举的属性，你可以为这个方法选择 `可变（mutating）`行为，然后就可以从其方法内部改变它的属性；并且这个方法做的任何改变都会在方法执行结束时写回到原始结构中。方法还可以给它隐含的 `self` 属性赋予一个全新的实例，这个新实例在方法结束时会替换现存实例。

要使用 `可变`方法，将关键字 `mutating` 放到方法的 `func` 关键字之前就可以了：

```swift
struct Point {
    var x = 0.0, y = 0.0
    mutating func moveBy(x deltaX: Double, y deltaY: Double) {
        x += deltaX
        y += deltaY
    }
}
var somePoint = Point(x: 1.0, y: 1.0)
somePoint.moveBy(x: 2.0, y: 3.0)
print("The point is now at (\(somePoint.x), \(somePoint.y))")
// 打印“The point is now at (3.0, 4.0)”
```



### 类型方法

实例方法是被某个类型的实例调用的方法。你也可以定义在类型本身上调用的方法，这种方法就叫做*类型方法*。在方法的 `func` 关键字之前加上关键字 `static`，来指定类型方法。类还可以用关键字 `class` 来指定，从而允许子类重写父类该方法的实现。

```swift
class SomeClass {
    class func someTypeMethod() {
        // 在这里实现类型方法
    }
}
SomeClass.someTypeMethod()
```



## 下标

可以 someArray[index] 位置，也可以 someDict[key] 关键字



### 定义下标

关键字subscript

```swift
subscript(index: Int) -> Int {
    get {
      // 返回一个适当的 Int 类型的值
    }
    set(newValue) {
      // 执行适当的赋值操作
    }
}


struct TimesTable {
    let multiplier: Int
    subscript(index: Int) -> Int {
        return multiplier * index
    }
}
let threeTimesTable = TimesTable(multiplier: 3)
print("six times three is \(threeTimesTable[6])")
// 打印“six times three is 18”
```



### 下标选项

下标可以接受任意数量的入参，并且这些入参可以是任意类型。下标的返回值也可以是任意类型。类型包括基础类型Int、String或者自定义类。下标可以使用可变参数，但是不能使用 in-out 参数以及不能提供默认参数。

```swift
struct Matrix {
    let rows: Int, columns: Int
    var grid: [Double]
    init(rows: Int, columns: Int) {
        self.rows = rows
        self.columns = columns
        grid = Array(repeating: 0.0, count: rows * columns)
    }
    func indexIsValid(row: Int, column: Int) -> Bool {
        return row >= 0 && row < rows && column >= 0 && column < columns
    }
    subscript(row: Int, column: Int) -> Double {
        get {
            assert(indexIsValid(row: row, column: column), "Index out of range")
            return grid[(row * columns) + column]
        }
        set {
            assert(indexIsValid(row: row, column: column), "Index out of range")
            grid[(row * columns) + column] = newValue
        }
    }
}

var matrix = Matrix(rows: 2, columns: 2)

matrix[0, 1] = 1.5
matrix[1, 0] = 3.2
```



## 构造过程

init

构造函数调用时，必须标签指明参数否则会报错，除非该标签还有个 _ 

```swift
struct Celsius {
    var temperatureInCelsius: Double
    init(fromFahrenheit fahrenheit: Double) {
        temperatureInCelsius = (fahrenheit - 32.0) / 1.8
    }
    init(fromKelvin kelvin: Double) {
        temperatureInCelsius = kelvin - 273.15
    }
    init(_ celsius: Double){
        temperatureInCelsius = celsius
    }
}

let bodyTemperature = Celsius(37.0)
// bodyTemperature.temperatureInCelsius 为 37.0
```



### 默认构造器

默认空参数和全部参数的构造器。

且全部参数的构造器，如果某参数有默认值或可选的，则构造时可以不传入该参数。

```swift
struct Size {
    var width = 0.0, height = 0.0
}

let twoByTwo = Size(width: 2.0, height: 2.0)

let zeroByTwo = Size(height: 2.0)
print(zeroByTwo.width, zeroByTwo.height)
// 打印 "0.0 2.0"
```



### 指定构造器和便利构造器

* 常规构造器是指定的，尽可能少。
* 便利构造器一般指向常规构造器，类似java里的带this(...)语句的构造函数。

类构造器函数前需要加 convenience 修饰。

```swift
class Food {
    var name: String
    init(name: String) {
        self.name = name
    }

    convenience init() {
        self.init(name: "[Unnamed]")
    }
}
```



### 可失败的构造器

```swift
struct Animal {
    let species: String
    init?(species: String) {
        if species.isEmpty {
            return nil
        }
        self.species = species
    }
}
```



枚举也适用

```swift
enum TemperatureUnit {
    case Kelvin, Celsius, Fahrenheit
    init?(symbol: Character) {
        switch symbol {
        case "K":
            self = .Kelvin
        case "C":
            self = .Celsius
        case "F":
            self = .Fahrenheit
        default:
            return nil
        }
    }
}
```



### 必要构造器

在类的构造器前添加 `required` 修饰符表明所有该类的子类都必须实现该构造器：

```swift
class SomeClass {
    required init() {
        // 构造器的实现代码
    }
}
```

在子类重写父类的必要构造器时，必须在子类的构造器前也添加 `required` 修饰符，表明该构造器要求也应用于继承链后面的子类。在重写父类中必要的指定构造器时，不需要添加 `override` 修饰符：

```swift
class SomeSubclass: SomeClass {
    required init() {
        // 构造器的实现代码
    }
}
```

> 注意
>
> 如果子类继承的构造器能满足必要构造器的要求，则无须在子类中显式提供必要构造器的实现。



## 析构过程

*析构器*只适用于类类型，当一个类的实例被释放之前，析构器会被立即调用。析构器用关键字 `deinit` 来标示，类似于构造器要用 `init` 来标示。Swift 会自动释放不再需要的实例以释放资源。

每个类只能有一个。



## 可选链式调用

java里如果有类嵌套，则非空判断经常会出现如下情况

```java
if(obj != null) {
  if(obj.child != null) {
    if(obj.child.child != null) {
      ...
    }
  }
}
```

而Swift的可选问题可以解决



```swift
class Person {
    var residence: Residence?
}

class Residence {
    var numberOfRooms = 1
}

let roomCount = john.residence!.numberOfRooms
// 这会引发运行时错误

if let roomCount = john.residence?.numberOfRooms {
    print("John's residence has \(roomCount) room(s).")
} else {
    print("Unable to retrieve the number of rooms.")
}
// 打印“Unable to retrieve the number of rooms.”
```

在 `residence` 后面添加问号之后，Swift 就会在 `residence` 不为 `nil` 的情况下访问 `numberOfRooms`。

因为访问 `numberOfRooms` 有可能失败，可选链式调用会返回 `Int?` 类型，把可选的特性链了下去。



除了属性，同理还能可选链式调用方法、下标。



## 错误处理

定义，要继承Error

```swift
enum VendingMachineError: Error {
    case invalidSelection                     //选择无效
    case insufficientFunds(coinsNeeded: Int) //金额不足
    case outOfStock                             //缺货
}
```



throw 抛出

```swift
throw VendingMachineError.insufficientFunds(coinsNeeded: 5)
```



处理。和java比，try换成do，函数前添加try。

也可以 try函数后继续让所在函数throws抛到上一层。

```swift
do {
    try canThrowAnError()
    // 没有错误消息抛出
} catch {
    // 有一个错误消息抛出
}

do {
    try makeASandwich()
    eatASandwich()
} catch SandwichError.outOfCleanDishes {
    washDishes()
} catch SandwichError.missingIngredients(let ingredients) {
    buyGroceries(ingredients)
}
```



### 将错误转换成可选值

可以使用 `try?` 通过将错误转换成一个可选值来处理错误。如果是在计算 `try?` 表达式时抛出错误，该表达式的结果就为 `nil`。例如，在下面的代码中，`x` 和 `y` 有着相同的数值和等价的含义：

```swift
func someThrowingFunction() throws -> Int {
    // ...
}

let x = try? someThrowingFunction()

// 和上面 let x 意思相同
let y: Int?
do {
    y = try someThrowingFunction()
} catch {
    y = nil
}
```



也可以禁用错误传递，如果真报错了直接运行时异常

```swift
let photo = try! loadImage(atPath: "./Resources/John Appleseed.jpg")
```



### Defer

类似java的finally，但defer定义和执行的代码位置不在一起。

```swift
func processFile(filename: String) throws {
    if exists(filename) {
        let file = open(filename)
        defer {
            close(file)
        }
        while let line = try file.readline() {
            // 处理文件。
        }
        // close(file) 会在这里被调用，即作用域的最后。
    }
}
```



## 类型转换

一般自动处理，类型判断可以用 `is` 关键字。

### 向下转型

某类型的一个常量或变量可能在幕后实际上属于一个子类。当确定是这种情况时，你可以尝试用*类型转换操作符*（`as?` 或 `as!`）向下转到它的子类型。

因为向下转型可能会失败，类型转型操作符带有两种不同形式。条件形式 `as?` 返回一个你试图向下转成的类型的可选值。强制形式 `as!` 把试图向下转型和强制解包转换结果结合为一个操作。

```swift
class MediaItem {
    var name: String
    init(name: String) {
        self.name = name
    }
}

class Movie: MediaItem {
    var director: String
    init(name: String, director: String) {
        self.director = director
        super.init(name: name)
    }
}

class Song: MediaItem {
    var artist: String
    init(name: String, artist: String) {
        self.artist = artist
        super.init(name: name)
    }
}

// library系统会判定类型为 MediaItem
let library = [
    Movie(name: "Casablanca", director: "Michael Curtiz"),
    Song(name: "Blue Suede Shoes", artist: "Elvis Presley"),
    Movie(name: "Citizen Kane", director: "Orson Welles"),
    Song(name: "The One And Only", artist: "Chesney Hawkes"),
    Song(name: "Never Gonna Give You Up", artist: "Rick Astley")
]

for item in library {
    if let movie = item as? Movie {
        print("Movie: \(movie.name), dir. \(movie.director)")
    } else if let song = item as? Song {
        print("Song: \(song.name), by \(song.artist)")
    }
}

// Movie: Casablanca, dir. Michael Curtiz
// Song: Blue Suede Shoes, by Elvis Presley
// Movie: Citizen Kane, dir. Orson Welles
// Song: The One And Only, by Chesney Hawkes
// Song: Never Gonna Give You Up, by Rick Astley
```



### Any 和 AnyObject 的类型转换

Swift 为不确定类型提供了两种特殊的类型别名：

- `Any` 可以表示任何类型，包括函数类型。
- `AnyObject` 可以表示任何类类型的实例。



## 扩展

*扩展* extension 可以给一个现有的类，结构体，枚举，还有协议添加新的功能。它还拥有不需要访问被扩展类型源代码就能完成扩展的能力（即*逆向建模*）。

Swift 中的扩展可以：

- 添加计算型实例属性和计算型类属性
- 定义实例方法和类方法
- 提供新的构造器
- 定义下标
- 定义和使用新的嵌套类型
- 使已经存在的类型遵循（conform）一个协议



### 计算型属性

扩展可以给现有类型添加计算型实例属性和计算型类属性。

```swift
extension Double {
    var km: Double { return self * 1_000.0 }
    var m: Double { return self }
    var cm: Double { return self / 100.0 }
    var mm: Double { return self / 1_000.0 }
    var ft: Double { return self / 3.28084 }
}
let oneInch = 25.4.mm
print("One inch is \(oneInch) meters")
// 打印“One inch is 0.0254 meters”
let threeFeet = 3.ft
print("Three feet is \(threeFeet) meters")
// 打印“Three feet is 0.914399970739201 meters”
```



### 构造器

```swift
extension Rect {
    init(center: Point, size: Size) {
        let originX = center.x - (size.width / 2)
        let originY = center.y - (size.height / 2)
        self.init(origin: Point(x: originX, y: originY), size: size)
    }
}
```



### 方法

```swift
extension Int {
    func repetitions(task: () -> Void) {
        for _ in 0..<self {
            task()
        }
    }
}

3.repetitions {
    print("Hello!")
}
// Hello!
// Hello!
// Hello!
```



### 可变实例方法

通过扩展添加的实例方法同样也可以修改（或 *mutating（改变）*）实例本身。结构体和枚举的方法，若是可以修改 `self` 或者它自己的属性，则必须将这个实例方法标记为 `mutating`，就像是改变了方法的原始实现。

```swift
extension Int {
    mutating func square() {
        self = self * self
    }
}
var someInt = 3
someInt.square()
// someInt 现在是 9
```



### 下标

```swift
extension Int {
    subscript(digitIndex: Int) -> Int {
        var decimalBase = 1
        for _ in 0..<digitIndex {
            decimalBase *= 10
        }
        return (self / decimalBase) % 10
    }
}
746381295[0]
// 返回 5
746381295[1]
// 返回 9
746381295[2]
// 返回 2
746381295[8]
// 返回 7
```



### 类扩展协议

```swift
extension SomeType: SomeProtocol, AnotherProtocol {
  // 协议所需要的实现写在这里
}
```



## 嵌套类型

扩展可以给现有的类，结构体，还有枚举添加新的嵌套类型：

```swift
extension Int {
    enum Kind {
        case negative, zero, positive
    }
    var kind: Kind {
        switch self {
        case 0:
            return .zero
        case let x where x > 0:
            return .positive
        default:
            return .negative
        }
    }
}
```

```swift
func printIntegerKinds(_ numbers: [Int]) {
    for number in numbers {
        switch number.kind {
        case .negative:
            print("- ", terminator: "")
        case .zero:
            print("0 ", terminator: "")
        case .positive:
            print("+ ", terminator: "")
        }
    }
    print("")
}
printIntegerKinds([3, 19, -27, 0, -6, 0, 7])
// 打印“+ + - 0 - 0 + ”
```



## 协议

http://www.swift51.com/swift5.1/02_language_guide/21_Protocols.html

类似java的interface接口

```swift
protocol SomeProtocol {
    var mustBeSettable: Int { get set }
    var doesNotNeedToBeSettable: Int { get }
    func random() -> Double
}
```



### 构造器要求

协议可以要求遵循协议的类型实现指定的构造器。你可以像编写普通构造器那样，在协议的定义里写下构造器的声明，但不需要写花括号和构造器的实体：

```swift
protocol SomeProtocol {
    init(someParameter: Int)
}

class SomeClass: SomeProtocol {
  	// 必须带 required
    required init(someParameter: Int) {
        // 这里是构造器的实现部分
    }
}
```



## 泛型

和java一样 < T >

```swift
func swapTwoValues<T>(_ a: inout T, _ b: inout T) {
    let temporaryA = a
    a = b
    b = temporaryA
}
```



### 类型约束语法

在一个类型参数名后面放置一个类名或者协议名，并用冒号进行分隔，来定义类型约束。下面将展示泛型函数约束的基本语法（与泛型类型的语法相同）：

```swift
func someFunction<T: SomeClass, U: SomeProtocol>(someT: T, someU: U) {
    // 这里是泛型函数的函数体部分
}
```



## 不透明类型

你可以认为不透明类型和泛型相反。泛型允许调用一个方法时，为这个方法的形参和返回值指定一个与实现无关的类型。

```swift
struct Square: Shape {
    var size: Int
    func draw() -> String {
        let line = String(repeating: "*", count: size)
        let result = Array<String>(repeating: line, count: size)
        return result.joined(separator: "\n")
    }
}

func makeTrapezoid() -> some Shape {
    let top = Triangle(size: 2)
    let middle = Square(size: 2)
    let bottom = FlippedShape(shape: top)
    let trapezoid = JoinedShape(
        top: top,
        bottom: JoinedShape(top: middle, bottom: bottom)
    )
    return trapezoid
}
let trapezoid = makeTrapezoid()
print(trapezoid.draw())
// *
// **
// **
// **
// **
// *
```

trapezoid 类型是 some Shape ，未暴露具体类型。

类似Java里多态，父类引用指向子类对象。



## 访问控制

- *open* 和 *public* 级别可以让实体被同一模块源文件中的所有实体访问，在模块外也可以通过导入该模块来访问源文件里的所有实体。通常情况下，你会使用 open 或 public 级别来指定框架的外部接口。open 和 public 的区别在后面会提到。
- *internal* 级别让实体被同一模块源文件中的任何实体访问，但是不能被模块外的实体访问。通常情况下，如果某个接口只在应用程序或框架内部使用，就可以将其设置为 internal 级别。
- *fileprivate* 限制实体只能在其定义的文件内部访问。如果功能的部分实现细节只需要在文件内使用时，可以使用 fileprivate 来将其隐藏。
- *private* 限制实体只能在其定义的作用域，以及同一文件内的 extension 访问。如果功能的部分细节只需要在当前作用域内使用时，可以使用 private 来将其隐藏。





# Combine 

是一个框架，可以让Swift实现响应式编程





# Swift UI

https://developer.apple.com/tutorials/swiftui

申明式，用状态控制

兼容传统的命令式 UIKit  https://developer.apple.com/tutorials/swiftui/interfacing-with-uikit



状态式，所以可以很好的实时preview结果，也可以点击运行测试交互。

点击组件右侧有组件信息，可以代码修改，可以组件信息里直接修改



## 架构

MVVM

[斯坦福大学课程MVVM讲解](https://www.bilibili.com/video/BV1EV411C77B?p=2&vd_source=50818d58b15659f611880803da5f8e4f)

<img src="/Users/lcy/Documents/code/boredream.github.io/_posts/swift.assets/image-20220811113051260.png" alt="image-20220811113051260" style="zoom: 33%;" />



### V = Views 视图

继承自View的SwiftUI组件，一般搭配 Preview 实时预览



#### @State

状态，修改会引起SwiftUI重绘



#### @Binding 

修改也会引起SwiftUI重绘，且 @Binding 标记的变量，在改变时也会双向返回来修改数据源。

如果是编辑模式+数据绑定属性，有可能编辑模式会撤回，应该在done之后才修改数据，因此一般还要copy一份数据修改之，完成后再赋值给目标对象。

注意，如果是@Binding的数据，在 PreviewProvider 中需要 .constant(XXXParam) 传入值，代表这是个常量，因为preview不care数据变化后的反馈



#### Optional State

由于Swift有Option，所以当 optional的@State作为参数传入其它View作为@Binding时，就会有一些问题

https://www.reddit.com/r/SwiftUI/comments/pcpc75/how_to_bind_an_optional_state_var_to_a_view_that/



### M = Model 数据

#### 实体类

通常需要实现 : Hashable, Codable, Identifiable



#### 数据提供类

提供数据来源，从本地、或网络请求



数据观察者

```swift
// 数据类-观察者里提供数据
final class ModelData: ObservableObject {
    @Published var landmarks: [Landmark] = load("landmarkData.json")
}

// 视图中使用数据，@EnviromentObject类似依赖注入
@EnvironmentObject var modelData: ModelData
modelData.landmarks
// 也可以正常新建方式创建
ModelData()

// 可以在App里@StateObject申明，代表全局单例
@main
struct swiftUIdemoApp: App {
    @StateObject private var modelData = ModelData()
    var body: some Scene {
        WindowGroup {
            ContentView().environmentObject(modelData)
        }
    }
}
```



#### 环境变量

https://developer.apple.com/documentation/swiftui/environment

可以理解为全局UI树的@State状态变量

@EnviromentObjec t类似依赖注入

@Environment(\\.key) 键值对维护的多个环境变量，系统自带部分key，如editMode编辑模式等





## 常用组件

### Stacks

容器，类似安卓Layout

* VStack。垂直方向 类似LineaLayout 
* HStack。水平方向
* 



Spacer 

填充空间组件，类似安卓 weight=1 的view



参数：

* alignment 对齐方式



### ImageView

默认不可修改大小，frame前需要resizable

如果需要保持比例，要.frame前调用 .aspectRatio / .scaleToFillorFit



### List

```swift
struct LandmarkList: View {
    var body: some View {
        List(landmarks) { landmark in
            // landmark 类需要继承 Identifiable 协议，或者手动 List(landmarks, id: \.id) 
            LandmarkRow(landmark: landmark)
        }
    }
}
```



自带margin、padding

可以根据需要手动去除

```swift
List {
    ForEach(modelData.categories.keys.sorted(), id: \.self) { key in
        CategoryRow(categoryName: key, items: modelData.categories[key]!)
    }
    .listRowInsets(EdgeInsets()) // 去掉padding
}
.listStyle(PlainListStyle()) // 去掉margin
```





## 通用属性

注意，属性的调用顺序是有不同效果的

* 宽高 .frame(width: 100, height: 100)



## 自定义组件

新建SwiftUI文件





## 动画

view.animate 绑定动画，动画的执行根据状态自行补件

如按钮由俩状态，则.animate设置后，每次切换状态都会唤起动画

```swift
Button {
  showDetail.toggle()
} label: {
  Label("Graph", systemImage: "chevron.right.circle")
  	.labelStyle(.iconOnly)
  	.imageScale(.large)
  	.rotationEffect(.degrees(showDetail ? 90 : 0))
  	.animation(nil, value: showDetail)
  	.scaleEffect(showDetail ? 1.5 : 1)
  	.padding()
  	.animation(.spring(), value: showDetail)
}
```



或者withAnimation包裹view

```swift
Button {
  withAnimation(.easeInOut(duration: 4)) {
    showDetail.toggle()
  }
} label: {
}
```



复杂的动画，一般 extension 封装下，然后view调用之

```swift
extension AnyTransition {
    static var moveAndFade: AnyTransition {
        .asymmetric(
            insertion: .move(edge: .trailing).combined(with: .opacity),
            removal: .scale.combined(with: .opacity)
        )
    }
}

HikeDetail(hike: hike).transition(.moveAndFade)
```







## 路由管理

### 页面跳转

NavigationView -> NavigationLink

```swift
NavigationView {
  List(landmarks) { landmark in
		NavigationLink {
       LandmarkDetail(landmark: landmark)
    } label: {
       LandmarkRow(landmark: landmark)
    }
  }
  .navigationTitle("Landmarks")
}
```

NavigationLink 包裹的 Button、Image会有显示问题，需要加上 .renderingMode(.origin)



### 弹窗

NavigationView -> .sheet(isPresented: Bool)

```swift
NavigationView {
    List {
        ForEach(modelData.categories.keys.sorted(), id: \.self) { key in
            CategoryRow(categoryName: key, items: modelData.categories[key]!)
        }
        .listRowInsets(EdgeInsets())
    }
    .toolbar {
        Button {
            showingProfile.toggle()
        } label: {
            Label("User Profile", systemImage: "person.crop.circle")
        }
    }
    .sheet(isPresented: $showingProfile) {
        ProfileHost().environmentObject(modelData)
    }
}
```





## 状态管理

@State private var xxxx 维护一个状态

然后在View里，$xxxx 绑定使用属性值



调试SwiftUI时，可以修改某个状态，然后点击preview下面的📌，固定当前状态的初始样式。



## 绘制





# SwiftUI 问题

问题：@State 列表数据，item作为@Binding传入子view后，及时修改了，列表这个@State 还是没变化，所以不会刷新？

回答：可以使用 ObservableObject + @Published Array



问题：@State 列表，一般是修改其中某个item，这个item要单独提取引用为@State对象吗？如果提取的话，修改这个对象，不会引起列表变化？

回答：可以只记录选择的index，使用和修改数据时用index去列表取



# UIKit

创建项目时，XCode需要你选择语言 OC/Swift，用户界面也会让你选择 StoryBorad/SwiftUI，前者就是UIKit

https://developer.apple.com/documentation/uikit

<img src="https://docs-assets.developer.apple.com/published/4e7c26b6ad/ff7aa08f-4857-44ce-88d5-7dacbef84509.png" alt="ff7aa08f-4857-44ce-88d5-7dacbef84509" style="zoom: 67%;" />



[斯坦福课程MVC](https://www.bilibili.com/video/BV1rb411C7eN?p=2&vd_source=50818d58b15659f611880803da5f8e4f)

<img src="/Users/lcy/Documents/code/boredream.github.io/_posts/swift.assets/image-20220818102959660.png" alt="image-20220818102959660" style="zoom: 33%;" />



## 和SwiftUI的区别

* SwiftUI是MVMM。UIKit是MVC
* SwiftUI的View是值类型，只是对UI的描述。UIKit里的视图更接近真实UI，包含各种生命周期回调方法



## 架构

### C：UIViewController

类似安卓Activity，操作页面



### V: UIView

类似安卓的layout视图



## Application

跟安卓类似。

UIApplication是程序类。程序跟入口则是UIApplicationDelegate

iOS一般还有个 XXDelegate用于处理回调通知事件等，>=iOS13中使用 UISceneDelete类



生命周期



## Scene

场景。类似安卓的一个Activity任务栈？



## 启动页

<img src="https://docs-assets.developer.apple.com/published/dd1b7996c3/rendered2x-1635759039.png" alt="启动页生命周期" style="zoom: 50%;" />





## Delegate





## 通知

https://developer.apple.com/documentation/uikit/uiapplication/1623078-registerforremotenotifications



# UIKit -> SwiftUI

如果想在SwiftUI中使用UIKit组件，可以使用 UIViewControllerRepresentable/UIViewRepresentable。

* makeUIViewController 创建视图。按需创建个UIKit返回。生命周期内只会调用一次
* updateUIView 更新视图。State等变化时，引起刷新。可以利用@Binding讲数据从SwiftUI传给UIKit
* makeCoordinator 协调器。用于SwiftUI从UIKit中抓取信息和事件

```swift
import SwiftUI
import UIKit

struct PageViewController<Page: View>: UIViewControllerRepresentable {
    var pages: [Page]

    func makeCoordinator() -> Coordinator {
        Coordinator(self)
    }

    func makeUIViewController(context: Context) -> UIPageViewController {
        let pageViewController = UIPageViewController(
            transitionStyle: .scroll,
            navigationOrientation: .horizontal)

        return pageViewController
    }

    func updateUIViewController(_ pageViewController: UIPageViewController, context: Context) {
        pageViewController.setViewControllers(
            [UIHostingController(rootView: pages[0])], direction: .forward, animated: true)
    }

    class Coordinator: NSObject {
        var parent: PageViewController

        init(_ pageViewController: PageViewController) {
            parent = pageViewController
        }
    }
}
```



为什么要有协调器？

继承自UIViewRepresentable的是一个结构体，而如果想做继承等类才有的功能，就需要用到Coordinator



