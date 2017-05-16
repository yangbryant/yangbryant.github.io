---
layout: post
title: SwiftyJSON 源码探索
date: 2017-05-16 09:14:00 +08:00
tags: 深阅读
---

***

## 一.概述

`SwiftyJSON` 是一个开源的第三方库, 帮助我们方便的在 Swift 语言中处理 JSON 数据. 它的代码我们可以在 `Github` 上面找到. `SwiftyJSON` 的代码可以称得上短小精悍, 只有短短的 1000 多行代码. 但虽然只有这么段的代码, 却涵盖了一个第三方库该有的大部分编码标准. 看完这篇文章后, 你的功力就会大幅提升. 为什么呢?
答案很简单, 就是你可以用 API 设计者的思维来思考问题. 这种思维角度非常的重要.

## 二.了解 SwiftyJSON

* 要开始研究 `SwiftyJSON` 库, 我们首先要得到它的代码. 大家可以访问 `SwiftyJSON` 在 `Github` 上面的主页来得到它的代码:

> [https://github.com/SwiftyJSON/SwiftyJSON][Link_2]

我们将代码下载下来后,就可以开始研究它了.

`SwiftyJSON` 库中,主要对外暴露了一个类, 就是 `JSON` 类. 这个类主要 **负责整体的数据解析和交互逻辑**, 下面我们就来从这个类看起吧.

## 三.JSON 类定义

* 我们来看一下 `JSON` 类的初始化方法, 这里我们对代码格式做了精简化处理, 删除了那些无用的格式.

```Swift
public struct JSON {
    /**
     Creates a JSON using the data.
     - parameter data:  The NSData used to convert to json.Top level object in data is an NSArray or NSDictionary
     - parameter opt:   The JSON serialization reading options. `.AllowFragments` by default.
     - parameter error: The NSErrorPointer used to return the error. `nil` by default.
     - returns: The created JSON
     */
    public init(data: Data, options opt: JSONSerialization.ReadingOptions = .allowFragments, error: NSErrorPointer = nil) {
        do {
            let object: Any = try JSONSerialization.jsonObject(with: data, options: opt)
            self.init(jsonObject: object)
        } catch let aError as NSError {
            if error != nil {
                error?.pointee = aError
            }
            self.init(jsonObject: NSNull())
        }
    }
    /**
     Creates a JSON using the object.
     - parameter object:  The object must have the following properties: All objects are NSString/String, NSNumber/Int/Float/Double/Bool, NSArray/Array, NSDictionary/Dictionary, or NSNull; All dictionary keys are NSStrings/String; NSNumbers are not NaN or infinity.
     - returns: The created JSON
     */
    fileprivate init(jsonObject: Any) {
        self.object = jsonObject
    }
    /**
     Creates a JSON from a [JSON]
     - parameter jsonArray: A Swift array of JSON objects
     - returns: The created JSON
     */
    fileprivate init(array: [JSON]) {
        self.init(array.map { $0.object })
    }
    /**
     Creates a JSON from a [String: JSON]
     - parameter jsonDictionary: A Swift dictionary of JSON objects
     - returns: The created JSON
     */
    fileprivate init(dictionary: [String: JSON]) {
        var newDictionary = [String: Any](minimumCapacity: dictionary.count)
        for (key, json) in dictionary {
            newDictionary[key] = json.object
        }
        self.init(newDictionary)
    }
}
```

* 首先, 我们看到 `JSON` 类是使用 `struct` 来定义的:

```Swift
public struct JSON
```

* `struct` 与 `class` 在 `Swift` 中定义的类型, 都可以包含成员变量和方法, 这点和我们在其他语言中对于 `struct` 的理解略有不同. 在 `Swift` 中, `struct` 与 `class` 的区别是, `struct` 声明的类型的实例, 在传值时是**拷贝传值**, 而 `class` 类型的实例在传值时是**引用传值**.

## 四.构造方法

* `JSON` 类有多个构造方法, 我们逐个来分析, 首先我们来看用 `Data` 作为参数的这个:

```Swift
    public init(data: Data, options opt: JSONSerialization.ReadingOptions = .allowFragments, error: NSErrorPointer = nil) {
        do {
            let object: Any = try JSONSerialization.jsonObject(with: data, options: opt)
            self.init(jsonObject: object)
        } catch let aError as NSError {
            if error != nil {
                error?.pointee = aError
            }
            self.init(jsonObject: NSNull())
        }
    }
```

* 这个构造方法是 `JSON` 类对外的主要构造方法, 它以 `Data` 对象作为参数, 并且后两个参数 `options` 和 `error` 是可选的, 这样我们就可以用这种方式来使用这个构造方法:

```Swift
let jsonObj = JSON(data:someData)
```

* 仔细看一下这个构造方法, 其实它的内部依然用到了 `JSONSerialization` 来解析 `JSON` 数据. 这样我们就大致了解它的运作原理, 原来它也是基于 `JSONSerialization` 的一个封装.

* 接下来, 我们看一下这个方法的实现, 它做了 `do try catch` 的错误处理, 检测 `JSONSerialization` 进行的 `JSON` 解析是否成功. 如果解析成功, 则将解析返回的对象传入 `self.init(jsonObject: object)` 这另外一个构造方法. 如果解析失败, 则传入一个 `NSNull()` 到这个构造方法: `self.init(jsonObject: NSNull())`.

* 那么我们顺利成章, 接下来就看一下 `self.init(jsonObject: object)` 这个构造方法:

```Swift
    fileprivate init(jsonObject: Any) {
        self.object = jsonObject
    }
```

* 我们看到, 这个构造方法的实现也很简单, 只是将传入的 `jsonObject ` 对象赋值给内部的 `self.object` 属性. 那么我们继续看一下 `self.object` 属性的定义.

```Swift
    /// Private object
    fileprivate var rawArray: [Any] = []
    fileprivate var rawDictionary: [String : Any] = [:]
    fileprivate var rawString: String = ""
    fileprivate var rawNumber: NSNumber = 0
    fileprivate var rawNull: NSNull = NSNull()
    fileprivate var rawBool: Bool = false
    /// Private type
    fileprivate var _type: Type = .null
    /// prviate error
    fileprivate var _error: NSError? = nil

    /// Object in JSON
    public var object: Any {
        get {
            switch self.type {
            case .array:
                return self.rawArray
            case .dictionary:
                return self.rawDictionary
            case .string:
                return self.rawString
            case .number:
                return self.rawNumber
            case .bool:
                return self.rawBool
            default:
                return self.rawNull
            }
        }
        set {
            _error = nil
            switch newValue {
            case let number as NSNumber:
                if number.isBool {
                    _type = .bool
                    self.rawBool = number.boolValue
                } else {
                    _type = .number
                    self.rawNumber = number
                }
            case let string as String:
                _type = .string
                self.rawString = string
            case _ as NSNull:
                _type = .null
            case _ as [JSON]:
                _type = .array
            case nil:
                _type = .null
            case let array as [Any]:
                _type = .array
                self.rawArray = array
            case let dictionary as [String : Any]:
                _type = .dictionary
                self.rawDictionary = dictionary
            default:
                _type = .unknown
                _error = NSError(domain: ErrorDomain, code: ErrorUnsupportedType, userInfo: [NSLocalizedDescriptionKey: "It is a unsupported type"])
            }
        }
    }

    /// JSON type
    public var type: Type { get { return _type } }
```

* 我们看到, `object` 属性的定义采用了 `Swift` 的自定义 `set` 和 `get`. 简而言之就是我们可以自己处理属性的设置和获取的逻辑.

* `get` 方法的实现比较简单, 只是简单的按照 `_type` 返回了这个 `object` :

```Swift
        get {
            switch self.type {
            case .array:
                return self.rawArray
            case .dictionary:
                return self.rawDictionary
            case .string:
                return self.rawString
            case .number:
                return self.rawNumber
            case .bool:
                return self.rawBool
            default:
                return self.rawNull
            }
        }
```

* `set` 的方法实现稍微复杂些, `newValue` 是一个特殊变量, 代表 `set` 方法传递进来的值, 比如我们前面的 `self.object = jsonObject` 等于号右边传递进来的值就是 `newValue` 所代表的值. 接下来, 通过一个 `switch` 语句, 判断新传入的值的类型, 把类型信息存储到 `_type` 变量中, 把数据存储到相应的变量中:

```Swift
        set {
            _error = nil
            switch newValue {
            case let number as NSNumber:
                if number.isBool {
                    _type = .bool
                    self.rawBool = number.boolValue
                } else {
                    _type = .number
                    self.rawNumber = number
                }
            case let string as String:
                _type = .string
                self.rawString = string
            case _ as NSNull:
                _type = .null
            case _ as [JSON]:
                _type = .array
            case nil:
                _type = .null
            case let array as [Any]:
                _type = .array
                self.rawArray = array
            case let dictionary as [String : Any]:
                _type = .dictionary
                self.rawDictionary = dictionary
            default:
                _type = .unknown
                _error = NSError(domain: ErrorDomain, code: ErrorUnsupportedType, userInfo: [NSLocalizedDescriptionKey: "It is a unsupported type"])
            }
        }
```

* 还有一个 `default` 分支, 对那些类型不在上述判断内的变量, 把他们设置为未知类型. 分析到现在, 大家是不是还有一个疑问呢, 那就是 `_type` 变量到底是什么东东呢? 我们来看一下 `type` 变量的定义:

```Swift
    fileprivate var _type: Type = .null
```

* 它是一个 `Type` 类型的变量, 那么关于 `Type` 类型, 它的定义又是怎样的呢?

```Swift
public enum Type: Int{
    case number
    case string
    case bool
    case array
    case dictionary
    case null
    case unknown
}
```

* 其实它就是一个枚举, 它定义了 `JSON` 对象中可以出现的数据类型, 这就和我们前面分析的 `switch` 判断对应上了.

* 分析到这里我们是不是对 `JSON` 对象的初始化有了一个整体的认识呢? 基本的步骤就是这样的:
1. 首先我们传递 `Data` 到 `JSON` 对象的构造方法.
2. 这个构造方法会使用 `JSONSerialization` 来解析传入的 `Data`.
3. 如果解析成功, 会将解析好的对象传入 `JSON` 的另外一个接受 `Any` 类型参数的构造方法.
4. 这个构造方法会根据传入对象的类型, 将它保存到自己的 `object` 存储, 并保存它的类型信息, 存储到相应的变量中.
5. 到此 `JSON` 对象的构造流程就完成了.

* 流程明确了, 再看另外两个构造方法:

```Swift
    fileprivate init(array: [JSON]) {
        self.init(array.map { $0.object })
    }
    fileprivate init(dictionary: [String: JSON]) {
        var newDictionary = [String: Any](minimumCapacity: dictionary.count)
        for (key, json) in dictionary {
            newDictionary[key] = json.object
        }
        self.init(newDictionary)
    }
```

* 这两个构造方法, 其实相当于对整体流程的一个补充, 我们先来看第一个, 它的实现只有一条语句:

```Swift
        self.init(array.map { $0.object })
```

* 将 `JSON` 为值类型的数组, 通过 `map` 函数做一个变换, 将他们作为普通值的数组, 传递给 `public init(_ object: Any)` 构造方法.
* 是不是被这么一说感觉有点蒙, 没关系我们举个例子说一下就明白.
* `JSON` 对象其实是对值得一个封装, 他们里面可以封装前面提到的枚举 `Type` 里面描述的任何一种类型的值.
* 假如我们有一个数组, 里面存储了 5 个 `JSON` 类型的变量, 每个 `JSON` 类型都封装了一个 `String` 类型的值.

```Swift
    let array = [JSON("Str1"),JSON("Str2"),JSON("Str3"),JSON("Str4"),JSON("Str5")]
```

* 这时, 我们的数组其实是上述的这种结构. 而 `map` 函数的作用在于对数组中得元素做一个变化, 比如这个调用:

```Swift
        array.map { $0.object }
```

* `$0` 代表当前对象, 我们这个调用就是将所有的值都变化为 `JSON` 对象的 `object` 属性, 那么变换后, 我们的数组结构就类似于这样:

```Swift
    let array = ["Str1","Str2","Str3","Str4","Str5"]
```

* 我们再将这个数组传递给 `JSON` 的构造方法, 实际上就是这个调用:

```Swift
        self.init(array.map { $0.object })
```

* 这下, 到了 `JSON` 的构造方法了, 我们传递一个 `Array` 类型的变量给 `JSON` 的构造方法.

```Swift
    public init(_ object: Any) {
        switch object {
        case let object as [JSON] where object.count > 0:
            self.init(array: object)
        case let object as [String: JSON] where object.count > 0:
            self.init(dictionary: object)
        case let object as Data:
            self.init(data: object)
        default:
            self.init(jsonObject: object)
        }
    }
```

* 根据 `Array` 类型, 传递给 `self.init(jsonObject: object)` 的构造方法.
* 由于 `Array` 类型是 `Type` 枚举中所列举的类型之一, 那么 `JSON` 会将这个 `Array` 类型的变量设置成它的 `object` 属性, 然后将类型信息保存到 `type` 属性中, 存储到相应的变量中.

* 对于 `dictionary` 构造方法, 以让是同样的逻辑:

```Swift
    fileprivate init(dictionary: [String: JSON]) {
        var newDictionary = [String: Any](minimumCapacity: dictionary.count)
        for (key, json) in dictionary {
            newDictionary[key] = json.object
        }

        self.init(newDictionary)
    }
```

* 这次, 我们传入的是一个 `JSON` 为值类型的字典, 我们首先也做了一次转换工作, 就是将字典中 `JSON` 类型的值, 转换成了 `JSON` 对象中所包含的具体的 `object` 值. 随后我们将这个转换后的字典传入 `JSON` 对象的构造方法. 这样 `JSON` 对象的整体构造流程我们就描述完了.

## 五.参考链接

此文参考于 [Swift Cafe][Link_1],十分感谢.  
所有引用内容版权归原作者所有.  
使用 [知识共享“署名-非商业性使用-相同方式共享 3.0 中国大陆”许可协议][Lisence] 授权.

`SwiftyJSON` 项目 `Github` 主页: 
> [https://github.com/SwiftyJSON/SwiftyJSON][Link_2]

***

[Lisence]: https://creativecommons.org/licenses/by-nc-sa/3.0/cn/

[Link_1]: https://swiftcafe.io/2015/07/28/swiftyJSON-explore/
[Link_2]: https://github.com/SwiftyJSON/SwiftyJSON
[Link_3]: https://en.wikipedia.org/wiki/YAML
[Link_4]: http://yaml.org/YAML_for_ruby.html