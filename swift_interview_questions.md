# Common Swift Interview Questions

## Swift Basics

1.  **What are the different data types in Swift? Explain value types vs. reference types.**

    Swift has a rich type system. Data types can be broadly categorized into **value types** and **reference types**.

    *   **Value Types:** When a value type is assigned to a variable or constant, or when it's passed to a function, a copy of its value is created. Each instance keeps a unique copy of its data.
        *   Examples: `Struct`, `Enum`, `Tuple`, and basic data types like `Int`, `Double`, `String`, `Bool`, `Array`, `Dictionary`, `Set`.
        *   **Characteristics:**
            *   Copied on assignment or when passed as an argument.
            *   Stored on the stack (generally, though compiler optimizations can sometimes place them on the heap).
            *   Good for ensuring that changes in one part of your code don't unintentionally affect other parts.

    *   **Reference Types:** When a reference type is assigned to a variable or constant, or when it's passed to a function, a reference (or pointer) to the same existing instance in memory is used. Multiple variables or constants can refer to the same single instance.
        *   Examples: `Class`, `Actor`, `Function`, `Closure`.
        *   **Characteristics:**
            *   A reference to the memory location is copied on assignment or when passed as an argument.
            *   Stored on the heap.
            *   Changes to the instance are visible to all references pointing to it.
            *   Managed by Automatic Reference Counting (ARC).

2.  **What are optionals and why are they important in Swift? How do you safely unwrap them?**

    **Optionals** in Swift are a powerful feature used to handle the absence of a value. An optional variable can either hold a value or be `nil` (no value). They are important because they make it explicit in your code that a value might be missing, forcing you to handle this possibility and preventing runtime crashes caused by unexpected `nil` values (common in languages like Objective-C).

    **Safely unwrapping optionals:**

    *   **Optional Binding (`if let` and `guard let`):**
        *   `if let constantName = optionalValue { // use constantName }`: Checks if the optional contains a value. If it does, it unwraps it and assigns it to a temporary constant (or variable with `if var`), making it available within the `if` block.
        *   `guard let constantName = optionalValue else { // handle nil and exit scope (return, break, continue, throw) }`: Similar to `if let`, but designed for early exits. The unwrapped value is available for the rest of the scope *after* the `guard` statement.

    *   **Nil-Coalescing Operator (`??`):**
        *   `let value = optionalValue ?? defaultValue`: Provides a default value if the optional is `nil`. If `optionalValue` is not `nil`, its unwrapped value is used; otherwise, `defaultValue` is used.

    *   **Optional Chaining (`?.`):**
        *   `let value = optionalInstance?.property?.method()`: Allows you to call properties, methods, and subscripts on an optional that might currently be `nil`. If any link in the chain is `nil`, the entire expression gracefully evaluates to `nil` instead of crashing.

    *   **Force Unwrapping (`!`):** (Use with extreme caution)
        *   `let value = optionalValue!`: Unwraps the optional, assuming it definitely contains a value. If it's `nil` at runtime, your app will crash. This should only be used when you are absolutely certain that the optional will have a value (e.g., an IBOutlet connected in Interface Builder that you know is set up).

3.  **Explain the different control flow statements in Swift (e.g., `if`, `guard`, `switch`, loops). When would you use `guard` over `if`?**

    Swift offers several control flow statements:

    *   **`if` statement:** Executes a block of code only if a condition is true. Can be chained with `else if` and `else` for multiple conditions.
        ```swift
        if temperature > 30 {
            print("It's hot!")
        } else if temperature < 10 {
            print("It's cold!")
        } else {
            print("It's moderate.")
        }
        ```

    *   **`guard` statement:** Used for early exits if a condition is *not* met. It requires an `else` block which must exit the current scope (e.g., using `return`, `break`, `continue`, or `throw`). Values unwrapped using `guard let` are available for the rest of the enclosing scope.
        ```swift
        func process(user: User?) {
            guard let validUser = user else {
                print("User is nil, cannot process.")
                return
            }
            // validUser is available here
            print("Processing \(validUser.name)")
        }
        ```
        **When to use `guard` over `if`:**
        Use `guard` to improve readability by handling validation and error conditions at the beginning of a scope, reducing nesting. It's especially useful for checking preconditions and ensuring that variables are valid before proceeding. `guard let` is preferred over `if let` when you want the unwrapped variable to be available in the rest of the function's scope, not just within a conditional block.

    *   **`switch` statement:** Compares a value against several possible matching patterns. `switch` statements in Swift must be exhaustive (cover all possible values) or include a `default` case. They also support powerful pattern matching features like value binding, `where` clauses, and matching ranges or tuples.
        ```swift
        switch statusCode {
        case 200:
            print("Success")
        case 404:
            print("Not Found")
        case 500...599:
            print("Server error")
        default:
            print("Unknown status code")
        }
        ```

    *   **Loops:**
        *   `for-in` loop: Iterates over a sequence (e.g., array, range, string characters).
            ```swift
            for fruit in ["Apple", "Banana", "Cherry"] {
                print(fruit)
            }
            ```
        *   `while` loop: Executes a block of code as long as a condition is true. The condition is checked *before* each iteration.
            ```swift
            var count = 0
            while count < 5 {
                print(count)
                count += 1
            }
            ```
        *   `repeat-while` loop: Similar to a `while` loop, but the condition is checked *after* each iteration, meaning the loop body is always executed at least once.
            ```swift
            var attempts = 0
            repeat {
                print("Attempting connection...")
                attempts += 1
            } while attempts < 3 && !isConnected
            ```

    *   **Control Transfer Statements:**
        *   `continue`: Skips the current iteration of a loop and proceeds to the next.
        *   `break`: Exits a loop or `switch` statement immediately.
        *   `fallthrough`: In a `switch` statement, causes execution to continue into the next case block (rarely used in Swift due to C-style fallthrough behavior being discouraged).
        *   `return`: Exits a function or method, optionally providing a return value.
        *   `throw`: Used for error handling to signal that an error has occurred.

## Memory Management

4.  **What is Automatic Reference Counting (ARC) in Swift? How does it work?**

    **Automatic Reference Counting (ARC)** is Swift's system for managing memory usage in your app. It automatically tracks and manages the memory of class instances (reference types).

    **How it works:**

    1.  **Reference Count:** For every instance of a class, ARC maintains a "reference count," which is the number of properties, constants, and variables that currently have a strong reference to that instance.
    2.  **Incrementing:** When you assign a class instance to a property, constant, or variable, a strong reference is created, and ARC increments the reference count of that instance by one.
    3.  **Decrementing:** When a strong reference is broken (e.g., a variable holding the reference goes out of scope, or is set to `nil`), ARC decrements the reference count by one.
    4.  **Deallocation:** When the reference count of a class instance drops to zero, it means no part of your code is using that instance anymore. ARC deallocates the memory used by that instance, freeing it up for other uses. This happens automatically when the `deinit` method of the class is called.

    ARC generally "just works," so you don't usually need to think about memory management manually. However, it's crucial to understand how it works to avoid **retain cycles**, which can lead to memory leaks.

5.  **What are retain cycles and how can you avoid them? Explain `strong`, `weak`, and `unowned` references.**

    A **retain cycle** (or strong reference cycle) occurs when two or more class instances hold strong references to each other, such that their reference counts never drop to zero, even if no other part of your code is referencing them. This prevents ARC from deallocating these instances, leading to a memory leak.

    **Example of a retain cycle:**
    ```swift
    class Person {
        let name: String
        var apartment: Apartment?
        init(name: String) { self.name = name }
        deinit { print("\(name) is being deinitialized") }
    }

    class Apartment {
        let unit: String
        var tenant: Person? // Change to weak or unowned
        init(unit: String) { self.unit = unit }
        deinit { print("Apartment \(unit) is being deinitialized") }
    }

    var john: Person? = Person(name: "John")
    var unit4A: Apartment? = Apartment(unit: "4A")

    john?.apartment = unit4A // Person has a strong ref to Apartment
    unit4A?.tenant = john   // Apartment has a strong ref to Person (Cycle!)

    john = nil
    unit4A = nil
    // Neither deinit will be called because of the cycle.
    ```

    **How to avoid retain cycles using reference types:**

    Swift provides three types of references to manage relationships between class instances and prevent retain cycles:

    *   **`strong` reference:** (Default) This is a standard reference that keeps a firm hold on the instance it refers to and prevents it from being deallocated as long as the strong reference exists. When a strong reference is created, ARC increments the instance's reference count. This is the type of reference that can lead to retain cycles if not managed carefully.

    *   **`weak` reference:** A `weak` reference does *not* keep a strong hold on the instance it refers to, and thus does not prevent ARC from deallocating the referenced instance.
        *   ARC automatically sets a `weak` reference to `nil` when the instance it refers to is deallocated.
        *   Because of this, `weak` references must always be declared as optional types (e.g., `weak var delegate: MyDelegate?`).
        *   Use `weak` when the referenced instance has a shorter lifetime or when the other instance can exist independently. A common example is a `delegate` property.

    *   **`unowned` reference:** Like a `weak` reference, an `unowned` reference does *not* keep a strong hold on the instance it refers to. However, unlike a `weak` reference, an `unowned` reference is assumed to *always* have a value during its lifetime.
        *   ARC does *not* set an `unowned` reference to `nil` when the instance is deallocated. Accessing an `unowned` reference after the instance it refers to has been deallocated will result in a runtime crash.
        *   Therefore, `unowned` references are not optional.
        *   Use `unowned` when you are certain that the referenced instance will have the same lifetime or a longer lifetime than the instance holding the reference. For example, a child referencing its parent, where the child cannot exist without the parent. Also commonly used in closures that capture `self` when `self` is guaranteed to outlive the closure.

    **Breaking the cycle in the example:**
    To fix the `Person` and `Apartment` example, you could make `Apartment.tenant` a `weak` reference:
    ```swift
    class Apartment {
        let unit: String
        weak var tenant: Person? // Changed to weak
        init(unit: String) { self.unit = unit }
        deinit { print("Apartment \(unit) is being deinitialized") }
    }
    ```
    Now, when `john` and `unit4A` are set to `nil`, the `Person` instance can be deallocated, which breaks its reference to the `Apartment`. Then, the `Apartment` instance's reference count (from `tenant`) becomes zero (or rather, was already zero as `weak` doesn't increment it), allowing it to be deallocated.

## Structs vs. Classes

6.  **What are the main differences between structs and classes in Swift? When would you choose one over the other?**

    | Feature          | Struct                                     | Class                                          |
    | ---------------- | ------------------------------------------ | ---------------------------------------------- |
    | **Type**         | Value Type                                 | Reference Type                                 |
    | **Memory**       | Stored on Stack (generally)                | Stored on Heap (managed by ARC)                |
    | **Copying**      | Copied by value (new instance created)     | Copied by reference (points to same instance)  |
    | **Inheritance**  | No inheritance                             | Supports inheritance from other classes        |
    | **Deinitializers**| No `deinit` method                         | Can have a `deinit` method to clean up         |
    | **Mutability**   | `mutating` keyword for methods that modify properties if the instance is a `let` constant. Structs declared with `let` are fully immutable. | Methods can modify properties even if the instance is a `let` constant (if properties are `var`). The reference itself is constant, not the instance's content. |
    | **Identity**     | No intrinsic identity (compared by value)  | Has an intrinsic identity (can use `===` or `!==` to check if two references point to the exact same instance) |
    | **Use Cases**    | Simple data structures, values where uniqueness is not required, immutable objects. Examples: `Point`, `Size`, `Color`. | Objects with identity, shared mutable state, when Objective-C interoperability is needed, when inheritance is required. Examples: `ViewController`, `NetworkManager`. |

    **When to choose one over the other:**

    *   **Choose `struct` by default if:**
        *   The primary purpose is to encapsulate a few relatively simple data values.
        *   You expect that instances will be copied rather than referenced.
        *   Any properties that store value types will also be copied.
        *   You don’t need to inherit properties or behavior from another existing type.
        *   You don't need to control the identity of the instance.

    *   **Choose `class` if:**
        *   You need Objective-C interoperability (e.g., subclassing `NSObject` or working with Objective-C frameworks).
        *   You need to control the identity of the instance. You want to check if two references point to the exact same object using `===` or `!==`.
        *   You need inheritance to model a hierarchy of objects.
        *   You expect instances to be shared and modified by multiple parts of your code (reference semantics are desired).
        *   You need a `deinit` method to perform cleanup when an instance is deallocated.
        *   You need to manage shared, mutable state.

    Apple generally recommends preferring value types (structs and enums) where possible because they are simpler to reason about (no side effects from shared references) and can offer performance benefits due to stack allocation and no ARC overhead for the struct itself (though ARC still applies to any reference type properties within the struct).

## Protocols

7.  **What are protocols in Swift? How can they be used for polymorphism and code reusability?**

    A **protocol** in Swift defines a blueprint of methods, properties, and other requirements that suit a particular task or piece of functionality. Protocols don't provide implementations themselves (except for default implementations via protocol extensions); they only describe what an implementing type (class, struct, or enum) *must* provide.

    **Key features of protocols:**

    *   **Define a contract:** They specify a set of requirements that conforming types must adhere to.
    *   **Abstract interface:** They allow you to write code that works with any type that conforms to the protocol, without needing to know the specific concrete type.
    *   **Adoption:** Classes, structures, and enumerations can *adopt* one or more protocols.
    *   **Requirements:** Can include instance properties, type properties, instance methods, type methods, initializers, and subscripts.

    **How protocols enable polymorphism:**

    Polymorphism allows you to treat objects of different types in a uniform way if they share a common superclass or, in Swift, conform to a common protocol.

    ```swift
    protocol Drawable {
        func draw()
    }

    struct Circle: Drawable {
        func draw() {
            print("Drawing a Circle")
        }
    }

    struct Square: Drawable {
        func draw() {
            print("Drawing a Square")
        }
    }

    func render(shapes: [Drawable]) { // The 'shapes' array can hold any type conforming to Drawable
        for shape in shapes {
            shape.draw() // Calls the correct draw() method for Circle or Square
        }
    }

    let shapes: [Drawable] = [Circle(), Square()]
    render(shapes: shapes)
    // Output:
    // Drawing a Circle
    // Drawing a Square
    ```
    In this example, `render(shapes:)` can work with an array of `Drawable` objects. It doesn't care if the actual objects are `Circle`s or `Square`s; it only knows they can `draw()`. This is polymorphism.

    **How protocols enable code reusability:**

    *   **Abstracting Behavior:** You can define a behavior in a protocol and then have multiple, unrelated types implement that behavior.
    *   **Protocol Extensions:** You can provide default implementations for protocol methods and computed properties directly in a protocol extension. This allows conforming types to get this functionality "for free" or to provide their own custom implementation if needed.
        ```swift
        protocol Loggable {
            var logPrefix: String { get }
            func log(_ message: String)
        }

        extension Loggable {
            // Default implementation
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
            // Can also provide its own log method if needed
        }

        let product = Product(id: 123)
        product.log("Created") // Uses default implementation

        let user = User(username: "admin")
        user.log("Logged in") // Uses default implementation
        ```
    *   **Generic Constraints:** Protocols are heavily used with generics to specify capabilities required by type parameters.

8.  **Explain protocol-oriented programming. How does it differ from object-oriented programming?**

    **Protocol-Oriented Programming (POP)** is a programming paradigm, heavily emphasized in Swift, that focuses on designing your application's architecture by defining and composing protocols. Instead of relying primarily on class inheritance (as in traditional Object-Oriented Programming), POP encourages the use of protocols to define abstract interfaces and then extending these protocols or having types conform to them.

    **Key principles of POP:**

    1.  **Focus on "what it can do" (protocols) rather than "what it is" (classes).**
    2.  **Code Reusability through Protocol Extensions:** Provide default implementations for methods in protocol extensions, allowing many types (structs, enums, classes) to share common behavior without inheritance.
    3.  **Composition over Inheritance:** Build complex types by composing multiple smaller protocols rather than creating deep class hierarchies. This offers more flexibility and avoids issues like the "fragile base class" problem.
    4.  **Value Types and Protocols:** POP works exceptionally well with value types (structs and enums), allowing them to gain rich functionality without the overhead and complexities of reference types and inheritance.
    5.  **Improved Testability:** Code designed around protocols is often easier to test because you can create mock objects that conform to the protocol for testing purposes.

    **Differences from Object-Oriented Programming (OOP):**

    | Feature           | Protocol-Oriented Programming (POP)                                  | Object-Oriented Programming (OOP)                                       |
    | ----------------- | -------------------------------------------------------------------- | ----------------------------------------------------------------------- |
    | **Primary Focus** | Behavior and capabilities (defined by protocols).                    | Identity and state (defined by classes and objects).                    |
    | **Inheritance**   | Favors composition and protocol extensions over class inheritance. Multi-protocol conformance is common. | Relies heavily on class inheritance for code reuse and polymorphism. Multiple inheritance is often restricted or complex. |
    | **Type System**   | Works seamlessly with both value types (structs, enums) and reference types (classes). | Traditionally more focused on reference types (classes).                |
    | **Flexibility**   | More flexible in adding functionality to diverse types. Can "retrofit" protocols to existing types. | Can be less flexible; adding new shared behavior might require refactoring deep class hierarchies. |
    | **"Is-a" vs. "Has-a" / "Can-do"** | Emphasizes "can-do" (conforming to a protocol) or "has-a" (composition). | Often models "is-a" relationships through inheritance.                  |
    | **Single Source of Truth** | Default implementations in protocol extensions provide shared functionality. | Shared functionality is often in base classes.                          |

    **Is POP a replacement for OOP?**
    No, not entirely. Swift is a multi-paradigm language. POP is a powerful tool in the Swift developer's arsenal, but it doesn't mean abandoning OOP concepts entirely. Classes and inheritance still have their place, especially when dealing with identity or when subclassing frameworks that are class-based (like UIKit or AppKit). POP often complements OOP by providing better ways to achieve code reuse, abstraction, and polymorphism, particularly with value types.

## Closures

9.  **What are closures in Swift? Provide an example of a closure and explain its syntax.**

    **Closures** in Swift are self-contained blocks of functionality that can be passed around and used in your code. They can capture and store references to any constants and variables from the context in which they are defined (this is known as "closing over" those values, hence the name).

    Closures are similar to blocks in C and Objective-C, and lambdas in other programming languages.

    **Closures can take one of three forms:**

    1.  **Global functions:** These are closures that have a name and do not capture any values.
    2.  **Nested functions:** These are closures that have a name and can capture values from their enclosing function.
    3.  **Closure expressions:** These are unnamed closures written in a lightweight syntax that can capture values from their surrounding context.

    When people talk about "closures" in Swift, they often mean *closure expressions*.

    **Syntax of Closure Expressions:**

    The general syntax is:
    ```swift
    { (parameters) -> returnType in
        // statements
    }
    ```

    *   `{ }`: Curly braces surround the closure's body.
    *   `(parameters)`: The list of parameters the closure accepts. Each parameter has a name and a type.
    *   `-> returnType`: The type of the value returned by the closure. If the closure doesn't return a value, this (and the `->`) can be omitted (implicitly `Void`).
    *   `in`: Keyword that separates the parameter/return type declaration from the closure's body.
    *   `statements`: The code to be executed when the closure is called.

    **Example:**

    Let's say we have an array of names and we want to sort them. The `sorted(by:)` method on arrays takes a closure that defines the sorting logic.

    ```swift
    let names = ["Chris", "Alex", "Ewa", "Barry", "Daniella"]

    // Full closure syntax:
    let sortedNames = names.sorted(by: { (s1: String, s2: String) -> Bool in
        return s1 < s2
    })
    print(sortedNames) // Output: ["Alex", "Barry", "Chris", "Daniella", "Ewa"]
    ```

    **Swift's syntax optimizations for closures:**

    *   **Inferring Parameter and Return Types:**
        ```swift
        let sortedNames = names.sorted(by: { s1, s2 in return s1 < s2 })
        ```
    *   **Implicit Returns from Single-Expression Closures:** If the closure body is a single expression, the `return` keyword can be omitted.
        ```swift
        let sortedNames = names.sorted(by: { s1, s2 in s1 < s2 })
        ```
    *   **Shorthand Argument Names:** Swift provides shorthand argument names (`$0`, `$1`, `$2`, etc.) for the closure's parameters.
        ```swift
        let sortedNames = names.sorted(by: { $0 < $1 })
        ```
    *   **Trailing Closure Syntax:** If a closure expression is the *last* argument to a function, it can be written outside the function call's parentheses.
        ```swift
        let sortedNames = names.sorted { $0 < $1 }
        ```
    *   **Operator Methods:** If the closure simply calls an existing operator, you can use the operator directly (if its type matches the closure's signature).
        ```swift
        let sortedNames = names.sorted(by: <)
        ```

10. **What are escaping and non-escaping closures?**

    In Swift, when you pass a closure as an argument to a function, the closure can either be **non-escaping** or **escaping**.

    *   **Non-Escaping Closure (`@nonescaping` - default for function parameters):**
        *   A non-escaping closure is guaranteed to be executed *before* the function it's passed into returns.
        *   The closure's lifecycle is tied to the function call. It doesn't need to be stored beyond the function's execution.
        *   This is the default behavior for closure parameters in Swift.
        *   **Benefits:** The compiler can make certain performance optimizations because it knows the closure won't outlive the function call. For example, it can avoid some memory management overhead for captured variables. You can also refer to `self` implicitly within a non-escaping closure without creating a retain cycle risk (though you still need to be careful if `self` could be deallocated by another thread during the function's execution).

        ```swift
        func performSyncOperation(completion: () -> Void) {
            print("Starting operation...")
            completion() // Closure is called before function returns
            print("Operation finished.")
        }

        performSyncOperation {
            print("Completion handler called.")
        }
        ```

    *   **Escaping Closure (`@escaping`):**
        *   An escaping closure is a closure that is called *after* the function it was passed into returns. This means the closure "escapes" the scope of the calling function.
        *   This typically happens when the closure is stored in a property, instance variable, or is passed to another function that will execute it later (e.g., in an asynchronous operation like a network request callback).
        *   You must explicitly mark closure parameters with the `@escaping` attribute if they can escape.
        *   **Implications:**
            *   **Memory Management:** You need to be careful about retain cycles, especially when the closure captures `self`. You often need to use a capture list like `[weak self]` or `[unowned self]` to avoid strong reference cycles.
            *   The compiler cannot make the same optimizations as with non-escaping closures.

        ```swift
        var completionHandlers: [() -> Void] = []

        func performAsyncOperation(completion: @escaping () -> Void) {
            print("Starting async operation...")
            // Store the closure to be called later
            completionHandlers.append(completion)
            print("Async operation initiated, will complete later.")
        }

        performAsyncOperation {
            print("Async completion handler called!")
        }

        // Sometime later...
        if !completionHandlers.isEmpty {
            completionHandlers.first?() // Call the stored escaping closure
        }
        ```
        **Common use cases for escaping closures:**
        *   Asynchronous operations (network requests, timers).
        *   Storing closures in properties to be called later.
        *   Dispatching closures to a different queue (e.g., using Grand Central Dispatch).

## Generics

11. **What are generics in Swift? How do they help in writing flexible and reusable code? Provide an example.**

    **Generics** in Swift allow you to write flexible and reusable functions, structures, classes, and enums that can work with *any type*, subject to requirements you define, without sacrificing type safety. Instead of writing separate code for each specific type, you write code once using placeholders (called *type parameters*) for the types.

    **How generics help:**

    1.  **Code Reusability:** You can write a function or data structure once and use it with many different types. For example, Swift's `Array` and `Dictionary` are generic collections. An `Array<Element>` can store elements of any type `Element`.
    2.  **Type Safety:** Generics provide compile-time type checking. You still get strong type safety, as the compiler ensures that only appropriate types are used with your generic code. This prevents runtime errors.
    3.  **Abstraction:** They allow you to abstract away the specific types being operated on, focusing on the algorithms and logic.
    4.  **Flexibility:** You can define constraints on the type parameters (e.g., requiring a type parameter to conform to a specific protocol), which allows you to call protocol-specific methods on generic types.

    **Example: A Generic Function**

    Let's write a generic function to swap two values of any type.

    ```swift
    // Non-generic functions (would need one for each type)
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

    // Generic function using a type parameter 'T'
    func swapTwoValues<T>(_ a: inout T, _ b: inout T) {
        let temporaryA = a
        a = b
        b = temporaryA
    }

    var someInt = 3
    var anotherInt = 107
    swapTwoValues(&someInt, &anotherInt) // T is inferred as Int
    print("someInt is now \(someInt), and anotherInt is now \(anotherInt)")

    var someString = "hello"
    var anotherString = "world"
    swapTwoValues(&someString, &anotherString) // T is inferred as String
    print("someString is now \(someString), and anotherString is now \(anotherString)")
    ```
    In `swapTwoValues<T>`, `T` is a *type parameter*. It's a placeholder for an actual type (like `Int` or `String`) that will be provided when `swapTwoValues` is called.

    **Example: A Generic Data Structure (Stack)**

    ```swift
    struct Stack<Element> { // 'Element' is the type parameter
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

    // Using the generic Stack with Ints
    var intStack = Stack<Int>()
    intStack.push(1)
    intStack.push(2)
    print(intStack.pop() ?? -1) // Output: 2

    // Using the generic Stack with Strings
    var stringStack = Stack<String>()
    stringStack.push("apple")
    stringStack.push("banana")
    print(stringStack.peek() ?? "empty") // Output: banana
    ```
    This `Stack` can store elements of any type specified by `Element`.

    **Generic Constraints:**
    You can require type parameters to conform to certain protocols or be a subclass of a specific class.
    ```swift
    func findIndex<T: Equatable>(of valueToFind: T, in array:[T]) -> Int? {
        for (index, value) in array.enumerated() {
            if value == valueToFind { // '==' requires T to conform to Equatable
                return index
            }
        }
        return nil
    }
    ```
    Here, `T: Equatable` means that `T` can be any type that conforms to the `Equatable` protocol (which provides the `==` operator).

## Error Handling

12. **How is error handling done in Swift? Explain `try`, `catch`, `throws`, and `do-catch` statements.**

    Swift provides a first-class error handling model that allows you to represent, throw, propagate, catch, and manipulate recoverable errors in your program.

    **Key Components:**

    1.  **Representing Errors (`Error` protocol):**
        Errors in Swift are represented by values of types that conform to the empty `Error` protocol. Enums are often a good choice for modeling a group of related error conditions.
        ```swift
        enum MyError: Error {
            case networkUnavailable
            case invalidInput(reason: String)
            case fileNotFound(path: String)
        }
        ```

    2.  **Throwing Errors (`throws`, `throw`):**
        *   A function or method that can potentially produce an error is marked with the `throws` keyword in its declaration (e.g., `func processData() throws -> String`).
        *   Inside a throwing function, you use the `throw` statement to signal that an error has occurred.
        ```swift
        func loadFile(atPath path: String) throws -> Data {
            if path.isEmpty {
                throw MyError.invalidInput(reason: "Path cannot be empty")
            }
            // ... attempt to load file ...
            // if file_does_not_exist {
            //     throw MyError.fileNotFound(path: path)
            // }
            // return fileData
            // Placeholder:
            guard let data = Data(base64Encoded: "SGVsbG8=") else { // "Hello"
                 throw MyError.networkUnavailable // Just an example
            }
            return data
        }
        ```

    3.  **Calling Throwing Functions (`try`):**
        When you call a function that can throw an error, you must preface the call with the `try` keyword. Swift requires this to make it clear that an error might be thrown at this point.

    4.  **Propagating Errors:**
        If a throwing function calls another throwing function, it can propagate the error up the call stack. The calling function must also be marked `throws`.

    5.  **Handling Errors (`do-catch`):**
        The `do-catch` statement provides a way to handle errors by running a block of code that contains one or more calls to throwing functions.
        ```swift
        do {
            // Code that might throw an error
            let data = try loadFile(atPath: "example.txt")
            print("File loaded successfully: \(String(data: data, encoding: .utf8) ?? "")")
        } catch MyError.networkUnavailable {
            print("Error: Network is unavailable.")
        } catch MyError.invalidInput(let reason) {
            print("Error: Invalid input - \(reason)")
        } catch MyError.fileNotFound(let path) {
            print("Error: File not found at \(path)")
        } catch { // Generic catch block, 'error' is an implicit constant
            print("An unexpected error occurred: \(error)")
        }
        ```
        *   A `do` block contains code that can throw errors (marked with `try`).
        *   If an error is thrown inside the `do` block, program control immediately transfers to the `catch` blocks.
        *   Each `catch` block can specify a pattern to match against the thrown error. If a match occurs, that `catch` block is executed.
        *   A general `catch` block (without a pattern, or `catch let error`) can catch any error that conforms to the `Error` protocol. The error is available as a local constant (implicitly named `error` if not specified).
        *   If an error is thrown and not caught by any `catch` block in the current scope, it propagates to the surrounding scope. If it's not caught anywhere, it will lead to a runtime crash.

    **Other ways to handle errors:**

    *   **Converting Errors to Optional Values (`try?`):**
        If you want to handle an error by converting it to an optional value, you can use `try?`. If the throwing expression succeeds, `try?` returns an optional containing the value. If it throws an error, `try?` returns `nil`.
        ```swift
        let data = try? loadFile(atPath: "nonexistent.txt")
        // data is now an optional Data. It will be nil if loadFile threw an error.
        if let actualData = data {
            print("Loaded data: \(actualData)")
        } else {
            print("Failed to load data, but we handled it as nil.")
        }
        ```

    *   **Disabling Error Propagation (`try!`):** (Use with caution)
        If you are certain that a throwing function will not actually throw an error at runtime, you can use `try!`. This effectively disables error propagation. If an error *is* thrown, your app will crash.
        ```swift
        // Only use try! if you are absolutely sure loadImage will not throw.
        // For example, if "icon.png" is a guaranteed app resource.
        // let image = try! loadImage(named: "icon.png")
        ```

13. **What is the difference between `try?` and `try!`?**

    Both `try?` and `try!` are ways to call functions or methods that can `throw` errors, but they handle potential errors differently.

    *   **`try?` (Optional Try):**
        *   **Purpose:** To convert a potential error into an optional value.
        *   **Behavior:**
            *   If the throwing expression (e.g., `try? someThrowingFunction()`) successfully returns a value, `try?` returns that value wrapped in an optional (e.g., `Optional<ReturnType>`).
            *   If the throwing expression throws an error, `try?` gracefully handles the error by returning `nil`. The error itself is discarded.
        *   **Return Type:** Always an optional type of the original function's return type (e.g., `ReturnType?`). If the original function returns `Void`, `try?` returns `Void?`.
        *   **Use Case:** When you want to attempt an operation that might fail, but you don't need to know the specific reason for the failure, and you're okay with handling a `nil` result instead. It simplifies error handling when you just want to know if it succeeded or not.

        ```swift
        func mightThrow() throws -> Int {
            // ... could throw an error or return an Int
            if Bool.random() { return 42 }
            else { enum SomeError: Error { case anError }; throw SomeError.anError }
        }

        let result = try? mightThrow()
        // result is of type Int?
        if let value = result {
            print("Succeeded with value: \(value)")
        } else {
            print("Failed, but we got nil instead of a crash.")
        }
        ```

    *   **`try!` (Forced Try):**
        *   **Purpose:** To call a throwing function when you are absolutely certain it will *not* throw an error at runtime.
        *   **Behavior:**
            *   If the throwing expression (e.g., `try! someThrowingFunction()`) successfully returns a value, `try!` returns that value directly (unwrapped).
            *   If the throwing expression *does* throw an error, your program will immediately crash with a runtime error. This is similar to force-unwrapping an optional that is `nil`.
        *   **Return Type:** The same as the original function's return type (e.g., `ReturnType`).
        *   **Use Case:** Only when you have a very strong guarantee that an error will not occur. For example, loading a resource that is known to be bundled with the application. Using `try!` inappropriately can lead to app crashes.

        ```swift
        func definitelyWontThrow() throws -> String {
            return "Success" // This function, in this example, never actually throws
        }

        // Only use if you are 100% sure no error will be thrown
        let message = try! definitelyWontThrow()
        // message is of type String
        print(message) // Output: Success

        // Example of misuse (if it could throw):
        // let riskyResult = try! mightThrow() // This will crash if mightThrow() throws
        // print(riskyResult)
        ```

    **Summary:**

    | Feature        | `try?`                                     | `try!`                                           |
    | -------------- | ------------------------------------------ | ------------------------------------------------ |
    | **Error Handling** | Converts error to `nil`. Error is discarded. | Disables error propagation. Crashes if error occurs. |
    | **Return Value** | Optional (`Value?` or `nil`)               | Non-optional (`Value`) or crashes.               |
    | **Safety**     | Safer; avoids crashes from expected errors. | Unsafe if assumption about no error is wrong.    |
    | **Use When**   | You can handle `nil` and don't need error details. | You are certain no error will be thrown.         |

    In general, prefer `do-catch` for robust error handling where you need to understand and react to specific errors. Use `try?` when a simple success/failure (`value`/`nil`) is sufficient. Use `try!` very sparingly and only when you have absolute certainty.

## Concurrency

14. **How does Swift handle concurrency? Explain `async/await`.**

    Swift provides modern concurrency features designed to make it easier to write safe and efficient concurrent code. The core of this system is the **`async/await`** pattern, along with structured concurrency constructs and actors.

    **Traditional Concurrency Challenges (before `async/await`):**
    *   **Callback Hell (Pyramid of Doom):** Deeply nested completion handlers for asynchronous operations, making code hard to read and maintain.
    *   **Complex Error Handling:** Error handling across multiple asynchronous calls can be cumbersome.
    *   **Difficult State Management:** Managing shared mutable state across concurrent tasks is prone to race conditions and deadlocks.
    *   **Manual Thread Management:** Directly managing threads or dispatch queues can be complex.

    **Swift's Modern Concurrency Model Aims to Solve These:**

    *   **`async/await`:**
        *   **`async`:** You mark a function or method with the `async` keyword to indicate that it can perform asynchronous work. An `async` function can suspend its execution without blocking the thread it's running on.
            ```swift
            func fetchDataFromServer() async throws -> Data {
                // ... some asynchronous network call ...
                // For example, using URLSession's async methods:
                let url = URL(string: "https://api.example.com/data")!
                let (data, _) = try await URLSession.shared.data(from: url)
                return data
            }
            ```
        *   **`await`:** You use the `await` keyword when calling an `async` function. This indicates that your code might pause (suspend) at this point if the `async` function needs to wait for its asynchronous operation to complete. While suspended, the thread is freed up to do other work, improving responsiveness. Once the `async` function finishes, execution resumes after the `await` keyword.
            ```swift
            func processData() async {
                print("Starting to process data...")
                do {
                    let data = try await fetchDataFromServer() // Potential suspension point
                    print("Data received: \(data.count) bytes")
                    // ... process the data ...
                } catch {
                    print("Error fetching data: \(error)")
                }
                print("Data processing finished.")
            }
            ```

    **Key benefits of `async/await`:**

    1.  **Readability:** Asynchronous code looks much more like synchronous code, making it easier to follow the logical flow.
    2.  **Simplified Error Handling:** You can use standard `do-catch` blocks with `try await` to handle errors from asynchronous functions, just like with synchronous throwing functions.
    3.  **Structured Concurrency:** `async/await` is part of a larger "structured concurrency" system. This means that the lifetime of concurrent tasks is tied to a specific scope. If a task has child tasks, it won't complete until all its child tasks are complete (or cancelled). This helps prevent tasks from leaking or running indefinitely.
        *   **`Task`:** You can create new concurrent tasks using `Task { ... }`.
        *   **Task Groups (`async let`, `withTaskGroup`):** Allow for running multiple child tasks concurrently and collecting their results.
    4.  **Cooperative Thread Management:** Swift's concurrency model manages threads cooperatively. When a task suspends, it yields the thread back to the system, which can then use it for other tasks. This is more efficient than blocking threads.

    **Example of starting a task:**
    ```swift
    func initiateProcessing() {
        Task { // Creates a new asynchronous task
            await processData()
        }
        print("initiateProcessing function has returned, but processData might still be running.")
    }
    ```

    **`async let` for parallel operations:**
    You can use `async let` to declare constants that will be computed by an asynchronous call, allowing multiple asynchronous operations to start and run in parallel. You then `await` their results when needed.
    ```swift
    func fetchMultipleResources() async throws -> (Data, String) {
        async let imageData = fetchDataFromServer() // Starts fetching image
        async let metadata = fetchMetadataFromServer() // Starts fetching metadata

        // Both imageData and metadata fetches run concurrently.
        // Execution suspends here until imageData is available (if not already)
        let image = try await imageData
        // Execution suspends here until metadata is available (if not already)
        let meta = try await metadata

        return (image, meta)
    }

    // Helper for the example
    func fetchMetadataFromServer() async throws -> String {
        try await Task.sleep(nanoseconds: 1_000_000_000) // Simulate 1 sec delay
        return "Important Metadata"
    }
    ```

    Swift's concurrency model also includes **actors** for managing shared mutable state safely, which works hand-in-hand with `async/await`.

15. **What are actors in Swift and how do they help prevent data races?**

    **Actors** are a fundamental part of Swift's modern concurrency model, designed to protect shared mutable state from data races. A data race occurs when multiple threads access the same piece of mutable data concurrently, and at least one of those accesses is a write, without proper synchronization. This can lead to unpredictable behavior, crashes, and corrupted data.

    **How Actors Work:**

    1.  **Reference Type for State Isolation:** An `actor` is a special kind of reference type (like a class). Its primary purpose is to encapsulate mutable state that needs to be accessed concurrently.

    2.  **Synchronization Mechanism:** Actors ensure that only one task can access their mutable state at a time. They achieve this by serializing access to their properties and methods. All calls to an actor's methods or property accesses from *outside* the actor are implicitly asynchronous.

    3.  **Actor-Isolated Context:** Inside an actor's own methods, properties, and initializers, you can access its state synchronously (without `await`) because you are already within its isolated context.

    4.  **`await` for External Access:** When code *outside* an actor (e.g., from another actor, a class, a struct, or a simple function) needs to interact with an actor's properties or call its methods, it must do so asynchronously using `await`. This `await` signifies a potential suspension point where the calling task might pause if the actor is busy serving another request. The actor processes incoming requests one at a time from its "mailbox."

    **Example:**

    ```swift
    actor Counter {
        private var value: Int = 0 // Mutable state, protected by the actor

        // This method can be called from outside the actor
        func increment() {
            value += 1
        }

        // This method can also be called from outside
        func getValue() -> Int {
            return value
        }

        // An internal method, can access 'value' synchronously
        func reset() {
            value = 0
            print("Counter reset internally.")
        }
    }

    func useCounter() async {
        let counter = Counter() // Create an instance of the actor

        // To call methods on the actor from outside, you must use 'await'
        await counter.increment()
        await counter.increment()

        let currentValue = await counter.getValue()
        print("Current counter value: \(currentValue)") // Output: Current counter value: 2

        // Example of multiple tasks trying to access the actor
        Task {
            await counter.increment()
            print("Task 1 incremented. Value: \(await counter.getValue())")
        }

        Task {
            await counter.increment()
            print("Task 2 incremented. Value: \(await counter.getValue())")
        }
    }

    // To run this:
    // Task {
    //    await useCounter()
    // }
    ```

    **How Actors Prevent Data Races:**

    *   **Mutual Exclusion:** The core mechanism is that an actor allows only one piece of code (one "hop" of an async task) to run on its state at any given time. If multiple tasks try to call `counter.increment()` concurrently, the `Counter` actor will process these calls sequentially, ensuring that `value += 1` is atomic with respect to other accesses to `value` through the actor.
    *   **No Direct External Access to Mutable State:** You cannot directly access `counter.value` from outside the actor because it's `private`. Even if it weren't private, if `value` were mutable, access would require `await`. For `let` constants with immutable data, synchronous access might be allowed if the data is `Sendable`.
    *   **Compiler Enforcement:** The Swift compiler enforces these rules. If you try to access actor-isolated state from outside without `await`, you'll get a compile-time error.

    **`nonisolated` Keyword:**
    Sometimes, an actor might have properties or methods that don't actually access its mutable state (e.g., a constant property or a method that only operates on its input parameters). You can mark these with `nonisolated` to allow synchronous access from outside the actor, bypassing the `await` requirement. This is an optimization for cases where the actor's protection isn't needed.

    ```swift
    actor ConfigurationManager {
        let serverURL: URL // Immutable, Sendable
        private var currentSettings: [String: String] = [:]

        nonisolated let version: String = "1.0" // Can be accessed synchronously

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

    Actors are a powerful tool for writing safe concurrent Swift code by making it much easier to manage shared mutable state without manual locking or other complex synchronization primitives. They are a key part of Swift's structured concurrency approach.
```
