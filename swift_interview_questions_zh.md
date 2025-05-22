# 常见的 Swift 面试题

## Swift 基础

1.  **Swift 中有哪些不同的数据类型？请解释值类型与引用类型。**

    Swift 拥有丰富的类型系统。数据类型可以大致分为**值类型**和**引用类型**。

    *   **值类型 (Value Types):** 当一个值类型被赋给一个变量或常量，或者被传递给一个函数时，它实际的值会被复制。每个实例都保留其数据的唯一副本。
        *   例子: `Struct` (结构体), `Enum` (枚举), `Tuple` (元组), 以及基础数据类型如 `Int`, `Double`, `String`, `Bool`, `Array`, `Dictionary`, `Set`。
        *   **特点:**
            *   赋值或作为参数传递时进行复制。
            *   通常存储在栈 (stack) 上（尽管编译器优化有时可能将它们放在堆上）。
            *   有助于确保代码一部分的更改不会意外影响其他部分。

    *   **引用类型 (Reference Types):** 当一个引用类型被赋给一个变量或常量，或者被传递给一个函数时，实际上传递的是对内存中同一现有实例的引用（或指针）。多个变量或常量可以指向同一个实例。
        *   例子: `Class` (类), `Actor` (参与者), `Function` (函数), `Closure` (闭包)。
        *   **特点:**
            *   赋值或作为参数传递时复制的是内存地址的引用。
            *   存储在堆 (heap) 上。
            *   对实例的更改对所有指向它的引用都是可见的。
            *   由自动引用计数 (ARC) 管理。

2.  **什么是可选类型 (Optionals)，它们为什么在 Swift 中很重要？如何安全地解包它们？**

    Swift 中的**可选类型 (Optionals)** 是一个强大的特性，用于处理值可能缺失的情况。一个可选类型的变量要么包含一个值，要么是 `nil` (没有值)。它们之所以重要，是因为它们在代码中明确指出了一个值可能不存在，从而强制你处理这种可能性，防止因意外的 `nil` 值（在像 Objective-C 这样的语言中很常见）导致的运行时崩溃。

    **安全解包可选类型的方法：**

    *   **可选绑定 (`if let` 和 `guard let`):**
        *   `if let constantName = optionalValue { // 使用 constantName }`: 检查可选类型是否包含值。如果包含，则解包并将其赋给一个临时常量（或使用 `if var` 赋给变量），使其在 `if` 代码块内可用。
        *   `guard let constantName = optionalValue else { // 处理 nil 并退出作用域 (return, break, continue, throw) }`: 与 `if let` 类似，但设计用于提前退出。解包后的值在 `guard` 语句之后的作用域中可用。

    *   **空合运算符 (`??`):**
        *   `let value = optionalValue ?? defaultValue`: 如果可选类型为 `nil`，则提供一个默认值。如果 `optionalValue` 不是 `nil`，则使用其解包后的值；否则，使用 `defaultValue`。

    *   **可选链 (`?.`):**
        *   `let value = optionalInstance?.property?.method()`: 允许你在可能当前为 `nil` 的可选类型上调用属性、方法和下标。如果链中的任何环节为 `nil`，整个表达式会优雅地评估为 `nil` 而不是崩溃。

    *   **强制解包 (`!`):** (极其谨慎使用)
        *   `let value = optionalValue!`: 强制解包可选类型，假定它肯定包含一个值。如果它在运行时为 `nil`，你的应用程序将会崩溃。只有当你绝对确定可选类型会有值时才应使用（例如，在 Interface Builder 中连接的、你知道已设置好的 IBOutlet）。

3.  **解释 Swift 中的不同控制流语句（例如 `if`、`guard`、`switch`、循环）。什么时候应该使用 `guard` 而不是 `if`？**

    Swift 提供了几种控制流语句：

    *   **`if` 语句:** 仅当条件为真时执行代码块。可以与 `else if` 和 `else` 链接以处理多个条件。
        ```swift
        if temperature > 30 {
            print("天气炎热！")
        } else if temperature < 10 {
            print("天气寒冷！")
        } else {
            print("气候温和。")
        }
        ```

    *   **`guard` 语句:** 用于在条件*不*满足时提前退出。它需要一个 `else` 块，该块必须退出当前作用域（例如，使用 `return`、`break`、`continue` 或 `throw`）。使用 `guard let` 解包的值在封闭作用域的其余部分可用。
        ```swift
        func process(user: User?) {
            guard let validUser = user else {
                print("用户为 nil，无法处理。")
                return
            }
            // validUser 在这里可用
            print("正在处理 \(validUser.name)")
        }
        ```
        **何时使用 `guard` 而不是 `if`：**
        使用 `guard` 可以通过在作用域开始处处理验证和错误条件来提高可读性，减少嵌套。它对于检查先决条件和确保变量在继续执行前有效特别有用。当你希望解包的变量在函数作用域的其余部分都可用，而不仅仅是在条件块内时，`guard let` 优于 `if let`。

    *   **`switch` 语句:** 将一个值与几种可能的匹配模式进行比较。Swift 中的 `switch` 语句必须是详尽的（覆盖所有可能的值）或包含 `default` 情况。它们还支持强大的模式匹配功能，如值绑定、`where` 子句以及匹配范围或元组。
        ```swift
        switch statusCode {
        case 200:
            print("成功")
        case 404:
            print("未找到")
        case 500...599:
            print("服务器错误")
        default:
            print("未知状态码")
        }
        ```

    *   **循环:**
        *   `for-in` loop: 遍历序列（例如，数组、范围、字符串字符）。
            ```swift
            for fruit in ["苹果", "香蕉", "樱桃"] {
                print(fruit)
            }
            ```
        *   `while` loop: 只要条件为真就执行代码块。条件在每次迭代*之前*检查。
            ```swift
            var count = 0
            while count < 5 {
                print(count)
                count += 1
            }
            ```
        *   `repeat-while` loop: 与 `while` 循环类似，但条件在每次迭代*之后*检查，这意味着循环体至少执行一次。
            ```swift
            var attempts = 0
            repeat {
                print("尝试连接...")
                attempts += 1
            } while attempts < 3 && !isConnected
            ```

    *   **控制转移语句:**
        *   `continue`: 跳过循环的当前迭代并进入下一次迭代。
        *   `break`: 立即退出循环或 `switch` 语句。
        *   `fallthrough`: 在 `switch` 语句中，使执行继续到下一个 case 块（在 Swift 中很少使用，因为不鼓励 C 风格的贯穿行为）。
        *   `return`: 退出函数或方法，可选地提供返回值。
        *   `throw`: 用于错误处理，以指示已发生错误。

## 内存管理

4.  **什么是 Swift 中的自动引用计数 (ARC)？它是如何工作的？**

    **自动引用计数 (ARC)** 是 Swift 用于管理应用程序内存使用的系统。它自动跟踪和管理类实例（引用类型）的内存。

    **工作原理：**

    1.  **引用计数 (Reference Count):** 对于每个类的实例，ARC 维护一个“引用计数”，即当前有多少属性、常量和变量对此实例持有强引用。
    2.  **增加计数:** 当你将类实例分配给属性、常量或变量时，会创建一个强引用，ARC 会将该实例的引用计数加一。
    3.  **减少计数:** 当强引用被断开时（例如，持有引用的变量超出作用域，或被设置为 `nil`），ARC 会将引用计数减一。
    4.  **释放 (Deallocation):** 当类实例的引用计数降至零时，意味着代码的任何部分都不再使用该实例。ARC 会释放该实例使用的内存，使其可用于其他用途。这在类的 `deinit` 方法被调用时自动发生。

    ARC 通常“就这样工作”，所以你通常不需要手动考虑内存管理。然而，理解其工作原理对于避免**保留环 (retain cycles)** 至关重要，保留环可能导致内存泄漏。

5.  **什么是保留环 (retain cycles)，如何避免它们？请解释 `strong`、`weak` 和 `unowned` 引用。**

    **保留环**（或强引用循环）发生在两个或多个类实例相互持有强引用，导致它们的引用计数永远不会降至零，即使代码的其他部分不再引用它们。这会阻止 ARC 释放这些实例，从而导致内存泄漏。

    **保留环示例：**
    ```swift
    class Person {
        let name: String
        var apartment: Apartment?
        init(name: String) { self.name = name }
        deinit { print("\(name) 正在被反初始化") }
    }

    class Apartment {
        let unit: String
        var tenant: Person? // 改为 weak 或 unowned
        init(unit: String) { self.unit = unit }
        deinit { print("公寓 \(unit) 正在被反初始化") }
    }

    var john: Person? = Person(name: "John")
    var unit4A: Apartment? = Apartment(unit: "4A")

    john?.apartment = unit4A // Person 对 Apartment 有强引用
    unit4A?.tenant = john   // Apartment 对 Person 有强引用 (循环!)

    john = nil
    unit4A = nil
    // 由于循环，两个 deinit 都不会被调用。
    ```

    **如何使用引用类型避免保留环：**

    Swift 提供了三种类型的引用来管理类实例之间的关系并防止保留环：

    *   **`strong` 引用 (默认):** 这是一种标准引用，它牢固地持有其引用的实例，并防止只要强引用存在，该实例就被释放。当创建强引用时，ARC 会增加实例的引用计数。如果管理不当，这种类型的引用可能导致保留环。

    *   **`weak` 引用:** `weak` 引用*不*会牢固地持有它引用的实例，因此不会阻止 ARC 释放被引用的实例。
        *   当其引用的实例被释放时，ARC 会自动将 `weak` 引用设置为 `nil`。
        *   因此，`weak` 引用必须始终声明为可选类型（例如，`weak var delegate: MyDelegate?`）。
        *   当被引用的实例具有较短的生命周期，或者当另一个实例可以独立存在时，使用 `weak`。一个常见的例子是 `delegate` 属性。

    *   **`unowned` 引用:** 与 `weak` 引用类似，`unowned` 引用也*不*会牢固地持有它引用的实例。但是，与 `weak` 引用不同，`unowned` 引用假定在其生命周期内*始终*具有值。
        *   当实例被释放时，ARC *不*会将 `unowned` 引用设置为 `nil`。在它引用的实例被释放后访问 `unowned` 引用将导致运行时崩溃。
        *   因此，`unowned` 引用不是可选类型。
        *   当你确定被引用的实例将具有与持有引用的实例相同或更长的生命周期时，使用 `unowned`。例如，一个子对象引用其父对象，子对象不能没有父对象而存在。也常用于捕获 `self` 的闭包中，当 `self` 保证比闭包生命周期更长时。

    **打破示例中的循环：**
    要修复 `Person` 和 `Apartment` 的示例，你可以将 `Apartment.tenant` 设为 `weak` 引用：
    ```swift
    class Apartment {
        let unit: String
        weak var tenant: Person? // 改为 weak
        init(unit: String) { self.unit = unit }
        deinit { print("公寓 \(unit) 正在被反初始化") }
    }
    ```
    现在，当 `john` 和 `unit4A` 被设置为 `nil` 时，`Person` 实例可以被释放，这会打破它对 `Apartment` 的引用。然后，`Apartment` 实例的引用计数（来自 `tenant`）变为零（或者说，由于 `weak` 不增加它，所以已经是零），允许它被释放。

## 结构体 (Structs) vs. 类 (Classes)

6.  **Swift 中结构体和类之间的主要区别是什么？什么时候应该选择使用其中一种？**

    | 特性             | 结构体 (Struct)                                  | 类 (Class)                                          |
    | ---------------- | ------------------------------------------------ | --------------------------------------------------- |
    | **类型**         | 值类型                                           | 引用类型                                            |
    | **内存**         | 通常存储在栈上                                   | 存储在堆上 (由 ARC 管理)                            |
    | **复制**         | 按值复制 (创建新实例)                             | 按引用复制 (指向同一实例)                           |
    | **继承**         | 不支持继承                                       | 支持从其他类继承                                    |
    | **反初始化器**   | 没有 `deinit` 方法                               | 可以有 `deinit` 方法进行清理                        |
    | **可变性**       | 如果实例是 `let` 常量，则修改属性的方法需用 `mutating` 关键字。用 `let` 声明的结构体是完全不可变的。 | 即使实例是 `let` 常量（如果属性是 `var`），方法也可以修改属性。引用本身是常量，而不是实例的内容。 |
    | **身份**         | 没有固有身份 (按值比较)                           | 具有固有身份 (可以使用 `===` 或 `!==` 检查两个引用是否指向完全相同的实例) |
    | **使用场景**     | 简单数据结构，不需要唯一性的值，不可变对象。例如：`Point`, `Size`, `Color`。 | 具有身份的对象，共享可变状态，需要 Objective-C 互操作性时，需要继承时。例如：`ViewController`, `NetworkManager`。 |

    **何时选择其中一种：**

    *   **默认情况下选择 `struct`，如果：**
        *   主要目的是封装一些相对简单的数据值。
        *   你期望实例被复制而不是被引用。
        *   任何存储值类型的属性也将被复制。
        *   你不需要从另一个现有类型继承属性或行为。
        *   你不需要控制实例的身份。

    *   **选择 `class`，如果：**
        *   你需要 Objective-C 互操作性（例如，子类化 `NSObject` 或使用 Objective-C 框架）。
        *   你需要控制实例的身份。你想使用 `===` 或 `!==` 检查两个引用是否指向完全相同的对象。
        *   你需要继承来为对象建模层次结构。
        *   你期望实例被代码的多个部分共享和修改（需要引用语义）。
        *   你需要 `deinit` 方法在实例被释放时执行清理。
        *   你需要管理共享的可变状态。

    Apple 通常建议尽可能优先使用值类型（结构体和枚举），因为它们更容易推理（没有共享引用的副作用），并且由于栈分配和结构体本身没有 ARC 开销（尽管 ARC 仍适用于结构体内的任何引用类型属性），因此可以提供性能优势。

## 协议 (Protocols)

7.  **Swift 中的协议是什么？它们如何用于实现多态和代码复用？**

    Swift 中的**协议 (protocol)** 定义了一个蓝图，规定了方法、属性和其他要求，以适应特定的任务或功能。协议本身不提供实现（除了通过协议扩展提供的默认实现）；它们只描述实现类型（类、结构体或枚举）*必须*提供什么。

    **协议的主要特性：**

    *   **定义契约:** 它们指定了遵循类型必须遵守的一组要求。
    *   **抽象接口:** 它们允许你编写适用于任何遵循该协议的类型的代码，而无需知道具体的类型。
    *   **采纳 (Adoption):** 类、结构体和枚举可以*采纳*一个或多个协议。
    *   **要求:** 可以包括实例属性、类型属性、实例方法、类型方法、初始化器和下标。

    **协议如何实现多态：**

    多态允许你以统一的方式处理不同类型的对象，前提是它们共享一个共同的超类，或者在 Swift 中，遵循一个共同的协议。

    ```swift
    protocol Drawable {
        func draw()
    }

    struct Circle: Drawable {
        func draw() {
            print("绘制圆形")
        }
    }

    struct Square: Drawable {
        func draw() {
            print("绘制正方形")
        }
    }

    func render(shapes: [Drawable]) { // 'shapes' 数组可以包含任何遵循 Drawable 协议的类型
        for shape in shapes {
            shape.draw() // 为 Circle 或 Square 调用正确的 draw() 方法
        }
    }

    let shapes: [Drawable] = [Circle(), Square()]
    render(shapes: shapes)
    // 输出:
    // 绘制圆形
    // 绘制正方形
    ```
    在这个例子中，`render(shapes:)` 可以处理一个 `Drawable` 对象的数组。它不关心实际对象是 `Circle` 还是 `Square`；它只知道它们可以 `draw()`。这就是多态。

    **协议如何实现代码复用：**

    *   **抽象行为:** 你可以在协议中定义一个行为，然后让多个不相关的类型实现该行为。
    *   **协议扩展 (Protocol Extensions):** 你可以直接在协议扩展中为协议方法和计算属性提供默认实现。这允许遵循类型“免费”获得此功能，或者在需要时提供自己的自定义实现。
        ```swift
        protocol Loggable {
            var logPrefix: String { get }
            func log(_ message: String)
        }

        extension Loggable {
            // 默认实现
            func log(_ message: String) {
                print("\(logPrefix): \(message)")
            }
        }

        struct Product: Loggable {
            let id: Int
            var logPrefix: String { return "Product[\(id)]" }
        }

        class User: Loggable {
            let username: String
            var logPrefix: String { return "User[\(username)]" }
            // 如果需要，也可以提供自己的 log 方法
        }

        let product = Product(id: 123)
        product.log("已创建") // 使用默认实现

        let user = User(username: "admin")
        user.log("已登录") // 使用默认实现
        ```
    *   **泛型约束 (Generic Constraints):** 协议与泛型一起大量使用，以指定类型参数所需的功能。

8.  **解释面向协议编程 (Protocol-Oriented Programming)。它与面向对象编程 (Object-Oriented Programming) 有何不同？**

    **面向协议编程 (POP)** 是一种编程范式，在 Swift 中得到高度强调，它侧重于通过定义和组合协议来设计应用程序的架构。与主要依赖类继承（如传统面向对象编程中）不同，POP 鼓励使用协议来定义抽象接口，然后扩展这些协议或让类型遵循它们。

    **POP 的关键原则：**

    1.  **关注“它能做什么”（协议）而不是“它是什么”（类）。**
    2.  **通过协议扩展实现代码复用：** 在协议扩展中提供方法的默认实现，允许许多类型（结构体、枚举、类）共享共同行为而无需继承。
    3.  **组合优于继承：** 通过组合多个较小的协议来构建复杂类型，而不是创建深层类层次结构。这提供了更大的灵活性，并避免了诸如“脆弱基类”问题。
    4.  **值类型与协议：** POP 与值类型（结构体和枚举）配合得非常好，使它们能够获得丰富的功能，而没有引用类型和继承的开销和复杂性。
    5.  **改进的可测试性：** 围绕协议设计的代码通常更容易测试，因为你可以创建遵循协议的模拟对象进行测试。

    **与面向对象编程 (OOP) 的区别：**

    | 特性             | 面向协议编程 (POP)                                     | 面向对象编程 (OOP)                                         |
    | ----------------- | ------------------------------------------------------- | ----------------------------------------------------------- |
    | **主要关注点**   | 行为和能力 (由协议定义)。                               | 身份和状态 (由类和对象定义)。                               |
    | **继承**         | 倾向于组合和协议扩展而非类继承。多协议遵循很常见。         | 严重依赖类继承来实现代码复用和多态。多重继承通常受限或复杂。 |
    | **类型系统**     | 与值类型（结构体、枚举）和引用类型（类）无缝协作。       | 传统上更侧重于引用类型（类）。                               |
    | **灵活性**       | 在向不同类型添加功能方面更灵活。可以“改造”现有类型以遵循协议。 | 可能不太灵活；添加新的共享行为可能需要重构深层类层次结构。     |
    | **"是一个" vs. "有一个" / "能做"** | 强调“能做”（遵循协议）或“有一个”（组合）。             | 通常通过继承来建模“是一个”关系。                             |
    | **单一事实来源** | 协议扩展中的默认实现提供共享功能。                       | 共享功能通常位于基类中。                                     |

    **POP 是 OOP 的替代品吗？**
    不完全是。Swift 是一种多范式语言。POP 是 Swift 开发人员工具箱中的强大工具，但这并不意味着完全放弃 OOP 概念。类和继承仍然有其地位，特别是在处理身份或子类化基于类的框架（如 UIKit 或 AppKit）时。POP 通常通过提供更好的方式来实现代码复用、抽象和多态（尤其对于值类型）来补充 OOP。

## 闭包 (Closures)

9.  **Swift 中的闭包是什么？提供一个闭包的例子并解释其语法。**

    Swift 中的**闭包 (Closures)** 是可以被传递和在代码中使用的自包含功能块。它们可以捕获并存储其定义上下文中任何常量和变量的引用（这被称为“闭合”这些值，因此得名）。

    闭包类似于 C 和 Objective-C 中的代码块 (blocks)，以及其他编程语言中的 lambda 表达式。

    **闭包可以采取以下三种形式之一：**

    1.  **全局函数 (Global functions):** 是具有名称且不捕获任何值的闭包。
    2.  **嵌套函数 (Nested functions):** 是具有名称且可以从其封闭函数捕获值的闭包。
    3.  **闭包表达式 (Closure expressions):** 是用轻量级语法编写的未命名闭包，可以从其周围上下文中捕获值。

    当人们在 Swift 中谈论“闭包”时，他们通常指的是*闭包表达式*。

    **闭包表达式的语法：**

    通用语法是：
    ```swift
    { (参数) -> 返回类型 in
        // 语句
    }
    ```

    *   `{ }`: 大括号包围闭包的主体。
    *   `(参数)`: 闭包接受的参数列表。每个参数都有名称和类型。
    *   `-> 返回类型`: 闭包返回的值的类型。如果闭包不返回值，则可以省略此部分（以及 `->`）（隐式为 `Void`）。
    *   `in`: 分隔参数/返回类型声明与闭包主体的关键字。
    *   `语句`: 调用闭包时要执行的代码。

    **示例：**

    假设我们有一个名称数组，我们想对它们进行排序。数组上的 `sorted(by:)` 方法接受一个定义排序逻辑的闭包。

    ```swift
    let names = ["Chris", "Alex", "Ewa", "Barry", "Daniella"]

    // 完整闭包语法:
    let sortedNames = names.sorted(by: { (s1: String, s2: String) -> Bool in
        return s1 < s2
    })
    print(sortedNames) // 输出: ["Alex", "Barry", "Chris", "Daniella", "Ewa"]
    ```

    **Swift 对闭包的语法优化：**

    *   **推断参数和返回类型：**
        ```swift
        let sortedNames = names.sorted(by: { s1, s2 in return s1 < s2 })
        ```
    *   **单表达式闭包的隐式返回：** 如果闭包主体是单个表达式，则可以省略 `return` 关键字。
        ```swift
        let sortedNames = names.sorted(by: { s1, s2 in s1 < s2 })
        ```
    *   **速记实际参数名称：** Swift 为闭包的参数提供速记实际参数名称（`$0`, `$1`, `$2` 等）。
        ```swift
        let sortedNames = names.sorted(by: { $0 < $1 })
        ```
    *   **尾随闭包语法：** 如果闭包表达式是函数的*最后一个*参数，则可以将其写在函数调用括号的外部。
        ```swift
        let sortedNames = names.sorted { $0 < $1 }
        ```
    *   **运算符方法：** 如果闭包只是调用现有运算符，则可以直接使用该运算符（如果其类型与闭包的签名匹配）。
        ```swift
        let sortedNames = names.sorted(by: <)
        ```

10. **什么是逃逸闭包和非逃逸闭包？**

    在 Swift 中，当你将闭包作为参数传递给函数时，该闭包可以是**非逃逸 (non-escaping)** 或**逃逸 (escaping)**。

    *   **非逃逸闭包 (`@nonescaping` - 函数参数的默认行为):**
        *   非逃逸闭包保证在其传递到的函数返回*之前*执行。
        *   闭包的生命周期与函数调用绑定。它不需要在函数执行之外存储。
        *   这是 Swift 中闭包参数的默认行为。
        *   **优点：** 编译器可以进行某些性能优化，因为它知道闭包不会比函数调用活得更久。例如，它可以避免捕获变量的一些内存管理开销。你也可以在非逃逸闭包中隐式引用 `self` 而不会产生保留环风险（尽管如果 `self` 可能在函数执行期间被另一个线程释放，你仍需小心）。

        ```swift
        func performSyncOperation(completion: () -> Void) {
            print("开始操作...")
            completion() // 闭包在函数返回前调用
            print("操作完成。")
        }

        performSyncOperation {
            print("完成处理程序已调用。")
        }
        ```

    *   **逃逸闭包 (`@escaping`):**
        *   逃逸闭包是在其传递到的函数返回*之后*调用的闭包。这意味着闭包“逃逸”了调用函数的作用域。
        *   这通常发生在闭包存储在属性、实例变量中，或传递给稍后将执行它的另一个函数时（例如，在异步操作（如网络请求回调）中）。
        *   如果闭包参数可能逃逸，你必须使用 `@escaping` 属性明确标记它们。
        *   **影响：**
            *   **内存管理：** 你需要小心保留环，尤其是在闭包捕获 `self` 时。你通常需要使用捕获列表（如 `[weak self]` 或 `[unowned self]`）来避免强引用循环。
            *   编译器无法进行与非逃逸闭包相同的优化。

        ```swift
        var completionHandlers: [() -> Void] = []

        func performAsyncOperation(completion: @escaping () -> Void) {
            print("开始异步操作...")
            // 存储闭包以供稍后调用
            completionHandlers.append(completion)
            print("异步操作已启动，稍后将完成。")
        }

        performAsyncOperation {
            print("异步完成处理程序已调用！")
        }

        // 一段时间后...
        if !completionHandlers.isEmpty {
            completionHandlers.first?() // 调用存储的逃逸闭包
        }
        ```
        **逃逸闭包的常见用例：**
        *   异步操作（网络请求、计时器）。
        *   将闭包存储在属性中以供稍后调用。
        *   将闭包分派到不同的队列（例如，使用 Grand Central Dispatch）。

## 泛型 (Generics)

11. **Swift 中的泛型是什么？它们如何帮助编写灵活和可复用的代码？提供一个例子。**

    Swift 中的**泛型 (Generics)** 允许你编写灵活且可复用的函数、结构体、类和枚举，这些代码可以与*任何类型*一起工作（受你定义的约束），而不会牺牲类型安全。你无需为每种特定类型编写单独的代码，而是使用占位符（称为*类型参数*）一次性编写代码。

    **泛型如何提供帮助：**

    1.  **代码复用性：** 你可以一次性编写一个函数或数据结构，并将其用于许多不同的类型。例如，Swift 的 `Array` 和 `Dictionary` 就是泛型集合。`Array<Element>` 可以存储任何类型 `Element` 的元素。
    2.  **类型安全：** 泛型提供编译时类型检查。你仍然可以获得强类型安全性，因为编译器会确保只有适当的类型与你的泛型代码一起使用。这可以防止运行时错误。
    3.  **抽象化：** 它们允许你抽象掉正在操作的具体类型，专注于算法和逻辑。
    4.  **灵活性：** 你可以为类型参数定义约束（例如，要求类型参数遵循特定协议），这允许你在泛型类型上调用特定于协议的方法。

    **示例：一个泛型函数**

    让我们编写一个泛型函数来交换任意类型的两个值。

    ```swift
    // 非泛型函数 (每种类型都需要一个)
    func swapTwoInts(_ a: inout Int, _ b: inout Int) {
        let temporaryA = a
        a = b
        b = temporaryA
    }

    func swapTwoStrings(_ a: inout String, _ b: inout String) {
        let temporaryA = a
        a = b
        b = temporaryA
    }

    // 使用类型参数 'T' 的泛型函数
    func swapTwoValues<T>(_ a: inout T, _ b: inout T) {
        let temporaryA = a
        a = b
        b = temporaryA
    }

    var someInt = 3
    var anotherInt = 107
    swapTwoValues(&someInt, &anotherInt) // T 被推断为 Int
    print("someInt 现在是 \(someInt)，anotherInt 现在是 \(anotherInt)")

    var someString = "hello"
    var anotherString = "world"
    swapTwoValues(&someString, &anotherString) // T 被推断为 String
    print("someString 现在是 \(someString)，anotherString 现在是 \(anotherString)")
    ```
    在 `swapTwoValues<T>` 中，`T` 是一个*类型参数*。它是一个实际类型（如 `Int` 或 `String`）的占位符，在调用 `swapTwoValues` 时将提供该类型。

    **示例：一个泛型数据结构 (栈)**

    ```swift
    struct Stack<Element> { // 'Element' 是类型参数
        private var items: [Element] = []

        mutating func push(_ item: Element) {
            items.append(item)
        }

        mutating func pop() -> Element? {
            return items.popLast()
        }

        func peek() -> Element? {
            return items.last
        }

        var isEmpty: Bool {
            return items.isEmpty
        }

        var count: Int {
            return items.count
        }
    }

    // 将泛型 Stack 用于 Int 类型
    var intStack = Stack<Int>()
    intStack.push(1)
    intStack.push(2)
    print(intStack.pop() ?? -1) // 输出: 2

    // 将泛型 Stack 用于 String 类型
    var stringStack = Stack<String>()
    stringStack.push("apple")
    stringStack.push("banana")
    print(stringStack.peek() ?? "empty") // 输出: banana
    ```
    这个 `Stack` 可以存储由 `Element` 指定的任何类型的元素。

    **泛型约束：**
    你可以要求类型参数遵循某些协议或成为特定类的子类。
    ```swift
    func findIndex<T: Equatable>(of valueToFind: T, in array:[T]) -> Int? {
        for (index, value) in array.enumerated() {
            if value == valueToFind { // '==' 要求 T 遵循 Equatable 协议
                return index
            }
        }
        return nil
    }
    ```
    这里，`T: Equatable` 意味着 `T` 可以是任何遵循 `Equatable` 协议（提供 `==` 运算符）的类型。

## 错误处理

12. **Swift 中如何进行错误处理？解释 `try`、`catch`、`throws` 和 `do-catch` 语句。**

    Swift 提供了一流的错误处理模型，允许你在程序中表示、抛出、传播、捕获和操作可恢复的错误。

    **主要组成部分：**

    1.  **表示错误 (`Error` 协议):**
        Swift 中的错误由遵循空 `Error` 协议的类型的值表示。枚举通常是为一组相关的错误条件建模的好选择。
        ```swift
        enum MyError: Error {
            case networkUnavailable
            case invalidInput(reason: String)
            case fileNotFound(path: String)
        }
        ```

    2.  **抛出错误 (`throws`, `throw`):**
        *   可能产生错误的函数或方法在其声明中使用 `throws` 关键字标记（例如，`func processData() throws -> String`）。
        *   在抛出函数内部，你使用 `throw` 语句来指示已发生错误。
        ```swift
        func loadFile(atPath path: String) throws -> Data {
            if path.isEmpty {
                throw MyError.invalidInput(reason: "路径不能为空")
            }
            // ... 尝试加载文件 ...
            // if file_does_not_exist {
            //     throw MyError.fileNotFound(path: path)
            // }
            // return fileData
            // 占位符:
            guard let data = Data(base64Encoded: "SGVsbG8=") else { // "Hello"
                 throw MyError.networkUnavailable // 仅为示例
            }
            return data
        }
        ```

    3.  **调用抛出函数 (`try`):**
        当你调用可能抛出错误的函数时，必须在调用前加上 `try` 关键字。Swift 要求这样做是为了明确表明此时可能会抛出错误。

    4.  **传播错误:**
        如果一个抛出函数调用另一个抛出函数，它可以将错误向上传播到调用堆栈。调用函数也必须标记为 `throws`。

    5.  **处理错误 (`do-catch`):**
        `do-catch` 语句提供了一种处理错误的方法，方法是运行一个包含对抛出函数的一个或多个调用的代码块。
        ```swift
        do {
            // 可能抛出错误的代码
            let data = try loadFile(atPath: "example.txt")
            print("文件加载成功: \(String(data: data, encoding: .utf8) ?? "")")
        } catch MyError.networkUnavailable {
            print("错误: 网络不可用。")
        } catch MyError.invalidInput(let reason) {
            print("错误: 无效输入 - \(reason)")
        } catch MyError.fileNotFound(let path) {
            print("错误: 在 \(path) 未找到文件")
        } catch { // 通用 catch 块，'error' 是一个隐式常量
            print("发生意外错误: \(error)")
        }
        ```
        *   `do` 块包含可能抛出错误的代码（用 `try` 标记）。
        *   如果在 `do` 块内抛出错误，程序控制立即转移到 `catch` 块。
        *   每个 `catch` 块都可以指定一个模式来匹配抛出的错误。如果发生匹配，则执行该 `catch` 块。
        *   一个通用的 `catch` 块（没有模式，或 `catch let error`）可以捕获任何遵循 `Error` 协议的错误。该错误作为局部常量可用（如果未指定，则隐式命名为 `error`）。
        *   如果抛出错误并且当前作用域中的任何 `catch` 块都未捕获它，则它会传播到周围的作用域。如果它在任何地方都未被捕获，将导致运行时崩溃。

    **其他处理错误的方法：**

    *   **将错误转换为可选值 (`try?`):**
        如果你想通过将错误转换为可选值来处理错误，可以使用 `try?`。如果抛出表达式成功，`try?` 返回包含该值的可选值。如果它抛出错误，`try?` 返回 `nil`。
        ```swift
        let data = try? loadFile(atPath: "nonexistent.txt")
        // data 现在是可选的 Data。如果 loadFile 抛出错误，它将为 nil。
        if let actualData = data {
            print("加载的数据: \(actualData)")
        } else {
            print("加载数据失败，但我们已将其作为 nil 处理。")
        }
        ```

    *   **禁用错误传播 (`try!`):** (谨慎使用)
        如果你确定一个抛出函数在运行时实际上不会抛出错误，可以使用 `try!`。这有效地禁用了错误传播。如果*确实*抛出了错误，你的应用程序将会崩溃。
        ```swift
        // 仅当你绝对确定 loadImage 不会抛出错误时才使用 try!。
        // 例如，如果 "icon.png" 是一个保证存在的应用程序资源。
        // let image = try! loadImage(named: "icon.png")
        ```

13. **`try?` 和 `try!` 有什么区别？**

    `try?` 和 `try!` 都是调用可能 `throw` 错误的函数或方法的方式，但它们处理潜在错误的方式不同。

    *   **`try?` (可选 Try):**
        *   **目的：** 将潜在错误转换为可选值。
        *   **行为：**
            *   如果抛出表达式（例如 `try? someThrowingFunction()`）成功返回值，`try?` 返回包装在可选类型中的该值（例如 `Optional<ReturnType>`）。
            *   如果抛出表达式抛出错误，`try?` 通过返回 `nil` 来优雅地处理错误。错误本身被丢弃。
        *   **返回类型：** 始终是原始函数返回类型的可选类型（例如 `ReturnType?`）。如果原始函数返回 `Void`，`try?` 返回 `Void?`。
        *   **用例：** 当你想要尝试一个可能会失败的操作，但你不需要知道失败的具体原因，并且可以接受处理 `nil` 结果时。当你只想知道操作是否成功时，它简化了错误处理。

        ```swift
        func mightThrow() throws -> Int {
            // ... 可能会抛出错误或返回一个 Int
            if Bool.random() { return 42 }
            else { enum SomeError: Error { case anError }; throw SomeError.anError }
        }

        let result = try? mightThrow()
        // result 的类型是 Int?
        if let value = result {
            print("成功，值为: \(value)")
        } else {
            print("失败，但我们得到了 nil 而不是崩溃。")
        }
        ```

    *   **`try!` (强制 Try):**
        *   **目的：** 当你绝对确定它在运行时*不*会抛出错误时，调用一个抛出函数。
        *   **行为：**
            *   如果抛出表达式（例如 `try! someThrowingFunction()`）成功返回值，`try!` 直接返回该值（未包装）。
            *   如果抛出表达式*确实*抛出错误，你的程序将立即因运行时错误而崩溃。这类似于强制解包一个为 `nil` 的可选类型。
        *   **返回类型：** 与原始函数的返回类型相同（例如 `ReturnType`）。
        *   **用例：** 仅当你非常确信不会发生错误时。例如，加载已知与应用程序捆绑在一起的资源。不当使用 `try!` 可能导致应用程序崩溃。

        ```swift
        func definitelyWontThrow() throws -> String {
            return "成功" // 此函数在此示例中实际上从不抛出错误
        }

        // 仅当你 100% 确定不会抛出错误时才使用
        let message = try! definitelyWontThrow()
        // message 的类型是 String
        print(message) // 输出: 成功

        // 滥用示例 (如果它可能抛出错误):
        // let riskyResult = try! mightThrow() // 如果 mightThrow() 抛出错误，这将崩溃
        // print(riskyResult)
        ```

    **总结：**

    | 特性           | `try?`                                     | `try!`                                           |
    | -------------- | ------------------------------------------ | ------------------------------------------------ |
    | **错误处理**   | 将错误转换为 `nil`。错误被丢弃。             | 禁用错误传播。如果发生错误则崩溃。                 |
    | **返回值**     | 可选 (`Value?` 或 `nil`)                   | 非可选 (`Value`) 或崩溃。                          |
    | **安全性**     | 更安全；避免因预期错误而崩溃。               | 如果关于没有错误的假设是错误的，则不安全。         |
    | **使用时机**   | 你可以处理 `nil` 并且不需要错误详细信息时。   | 你确定不会抛出错误时。                             |

    通常，对于需要理解和响应特定错误的健壮错误处理，优先选择 `do-catch`。当简单的成功/失败（`value`/`nil`）就足够时，使用 `try?`。非常谨慎地使用 `try!`，并且仅在你绝对确定时才使用。

## 并发 (Concurrency)

14. **Swift 如何处理并发？解释 `async/await`。**

    Swift 提供了现代并发功能，旨在使编写安全高效的并发代码更容易。该系统的核心是 **`async/await`** 模式，以及结构化并发构造和 actor。

    **传统并发挑战 (在 `async/await` 之前):**
    *   **回调地狱 (Pyramid of Doom):** 异步操作的深度嵌套完成处理程序，使代码难以阅读和维护。
    *   **复杂的错误处理：** 跨多个异步调用的错误处理可能很麻烦。
    *   **困难的状态管理：** 在并发任务之间管理共享可变状态容易出现竞争条件和死锁。
    *   **手动线程管理：** 直接管理线程或分派队列可能很复杂。

    **Swift 的现代并发模型旨在解决这些问题：**

    *   **`async/await`:**
        *   **`async`:** 你使用 `async` 关键字标记函数或方法，以指示它可以执行异步工作。`async` 函数可以暂停其执行而不会阻塞其运行的线程。
            ```swift
            func fetchDataFromServer() async throws -> Data {
                // ... 一些异步网络调用 ...
                // 例如，使用 URLSession 的异步方法:
                let url = URL(string: "https://api.example.com/data")!
                let (data, _) = try await URLSession.shared.data(from: url)
                return data
            }
            ```
        *   **`await`:** 你在调用 `async` 函数时使用 `await` 关键字。这表示如果 `async` 函数需要等待其异步操作完成，你的代码可能会在此时暂停（挂起）。挂起时，线程被释放以执行其他工作，从而提高响应能力。一旦 `async` 函数完成，执行将在 `await` 关键字之后恢复。
            ```swift
            func processData() async {
                print("开始处理数据...")
                do {
                    let data = try await fetchDataFromServer() // 潜在的挂起点
                    print("收到数据: \(data.count) 字节")
                    // ... 处理数据 ...
                } catch {
                    print("获取数据时出错: \(error)")
                }
                print("数据处理完成。")
            }
            ```

    **`async/await` 的主要优点：**

    1.  **可读性：** 异步代码看起来更像同步代码，使其更容易理解逻辑流程。
    2.  **简化的错误处理：** 你可以像处理同步抛出函数一样，使用标准的 `do-catch` 块和 `try await` 来处理异步函数的错误。
    3.  **结构化并发：** `async/await` 是更大的“结构化并发”系统的一部分。这意味着并发任务的生命周期与特定作用域绑定。如果一个任务有子任务，它在其所有子任务完成（或取消）之前不会完成。这有助于防止任务泄漏或无限期运行。
        *   **`Task`:** 你可以使用 `Task { ... }` 创建新的并发任务。
        *   **任务组 (`async let`, `withTaskGroup`):** 允许并发运行多个子任务并收集它们的结果。
    4.  **协作式线程管理：** Swift 的并发模型协作管理线程。当任务挂起时，它会将线程交还给系统，系统随后可以将其用于其他任务。这比阻塞线程更有效。

    **启动任务示例：**
    ```swift
    func initiateProcessing() {
        Task { // 创建一个新的异步任务
            await processData()
        }
        print("initiateProcessing 函数已返回，但 processData 可能仍在运行。")
    }
    ```

    **`async let` 用于并行操作：**
    你可以使用 `async let` 来声明将由异步调用计算的常量，从而允许多个异步操作并行启动和运行。然后在需要时 `await` 它们的结果。
    ```swift
    func fetchMultipleResources() async throws -> (Data, String) {
        async let imageData = fetchDataFromServer() // 开始获取图像
        async let metadata = fetchMetadataFromServer() // 开始获取元数据

        // imageData 和 metadata 的获取同时运行。
        // 如果 imageData 尚不可用，执行将在此处暂停
        let image = try await imageData
        // 如果 metadata 尚不可用，执行将在此处暂停
        let meta = try await metadata

        return (image, meta)
    }

    // 示例辅助函数
    func fetchMetadataFromServer() async throws -> String {
        try await Task.sleep(nanoseconds: 1_000_000_000) // 模拟 1 秒延迟
        return "重要的元数据"
    }
    ```

    Swift 的并发模型还包括用于安全管理共享可变状态的 **actor**，它与 `async/await` 协同工作。

15. **Swift 中的 actor 是什么，它们如何帮助防止数据竞争？**

    **Actor** 是 Swift 现代并发模型的基本组成部分，旨在保护共享可变状态免受数据竞争。当多个线程同时访问同一块可变数据，并且至少有一次访问是写入操作，而没有适当的同步时，就会发生数据竞争。这可能导致不可预测的行为、崩溃和数据损坏。

    **Actor 的工作原理：**

    1.  **用于状态隔离的引用类型：** `actor` 是一种特殊的引用类型（如类）。其主要目的是封装需要并发访问的可变状态。

    2.  **同步机制：** Actor 确保一次只有一个任务可以访问其可变状态。它们通过序列化对其属性和方法的访问来实现这一点。从 actor *外部*对其方法的所有调用或属性访问都是隐式异步的。

    3.  **Actor 隔离的上下文：** 在 actor 自己的方法、属性和初始化器内部，你可以同步访问其状态（无需 `await`），因为你已经在其隔离的上下文中。

    4.  **外部访问的 `await`：** 当 actor *外部*的代码（例如，来自另一个 actor、类、结构体或简单函数）需要与 actor 的属性交互或调用其方法时，它必须使用 `await` 异步执行。此 `await` 表示一个潜在的挂起点，如果 actor 正忙于处理另一个请求，调用任务可能会在此暂停。Actor 从其“邮箱”中一次处理一个传入请求。

    **示例：**

    ```swift
    actor Counter {
        private var value: Int = 0 // 可变状态，受 actor 保护

        // 此方法可以从 actor 外部调用
        func increment() {
            value += 1
        }

        // 此方法也可以从外部调用
        func getValue() -> Int {
            return value
        }

        // 一个内部方法，可以同步访问 'value'
        func reset() {
            value = 0
            print("计数器内部已重置。")
        }
    }

    func useCounter() async {
        let counter = Counter() // 创建 actor 实例

        // 要从外部调用 actor 上的方法，必须使用 'await'
        await counter.increment()
        await counter.increment()

        let currentValue = await counter.getValue()
        print("当前计数器值: \(currentValue)") // 输出: 当前计数器值: 2

        // 多个任务尝试访问 actor 的示例
        Task {
            await counter.increment()
            print("任务 1 已递增。值: \(await counter.getValue())")
        }

        Task {
            await counter.increment()
            print("任务 2 已递增。值: \(await counter.getValue())")
        }
    }

    // 运行此代码:
    // Task {
    //    await useCounter()
    // }
    ```

    **Actor 如何防止数据竞争：**

    *   **互斥：** 核心机制是 actor 在任何给定时间只允许一段代码（异步任务的一个“跳跃”）在其状态上运行。如果多个任务尝试同时调用 `counter.increment()`，`Counter` actor 将按顺序处理这些调用，确保 `value += 1` 相对于通过 actor 对 `value` 的其他访问是原子的。
    *   **禁止直接从外部访问可变状态：** 你不能从 actor 外部直接访问 `counter.value`，因为它是 `private` 的。即使它不是 `private`，如果 `value` 是可变的，访问也需要 `await`。对于具有不可变数据的 `let` 常量，如果数据是 `Sendable` 的，则可能允许同步访问。
    *   **编译器强制执行：** Swift 编译器强制执行这些规则。如果你尝试在没有 `await` 的情况下从外部访问 actor 隔离的状态，你将收到编译时错误。

    **`nonisolated` 关键字：**
    有时，actor 可能具有实际上不访问其可变状态的属性或方法（例如，常量属性或仅对其输入参数进行操作的方法）。你可以使用 `nonisolated` 标记这些属性或方法，以允许从 actor 外部进行同步访问，从而绕过 `await` 要求。这对于不需要 actor 保护的情况是一种优化。

    ```swift
    actor ConfigurationManager {
        let serverURL: URL // 不可变, Sendable
        private var currentSettings: [String: String] = [:]

        nonisolated let version: String = "1.0" // 可以同步访问

        init(serverURL: URL) {
            self.serverURL = serverURL
        }

        func updateSetting(key: String, value: String) {
            currentSettings[key] = value
        }

        nonisolated func getDocumentationLink() -> String {
            return "https://example.com/docs/\(version)"
        }
    }
    ```

    Actor 是编写安全的并发 Swift 代码的强大工具，它通过使管理共享可变状态变得更加容易，而无需手动锁定或其他复杂的同步原语。它们是 Swift 结构化并发方法的关键部分。
```
