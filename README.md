## Paperless Post iOS Team Swift Style and Conventions

### Table of Contents

1. [Intent](#intent)
1. [Whitespace](#whitespace)
1. [Conditionals](#conditional-bindings)
    * [Bindings](#bindings)
    * [If/Else vs. Guard](#ifelse-vs-guard)
1. [Optionals](#optionals)
    * [Force-Unwrapping of Optionals](#force-unwrapping-of-optionals)
    * [Implicitly Unwrapped Optionals](#implicitly-unwrapped-optionals)
1. [Definitions & Declarations](#definitions--declarations)
    * [Getters & Setters](#getters--setters)
    * [Top-Level Access Control Definitions](#top-level-access-control-definitions)
    * [Private Variable Prefixing](#private-variable-prefixing)
    * [Conditional Assignment](#conditional-assignment)
    * [Type Specification](#type-specification)
    * [Type Parameters](#type-parameters)
    * [Reference to Self](#reference-to-self)
    * [Protocols First](#protocols-first)
    * [Structs vs. Classes](#structs-vs-classes)
    * [Protocol Conformance](#protocol-conformance)
    * [Enum Cases](#enum-cases)
    * [Final by Default](#final-by-default)
    * [Operator Definitions](#operator-definitions)
    * [Implement `CustomStringConvertible`](#implement-customstringconvertible)

#### _Acknowledgments:_

* [Github Swift Style Guide](https://github.com/github/swift-style-guide)
* [raywenderlich.com Swift Style Guide](https://github.com/raywenderlich/swift-style-guide)

#### _Contributing:_

To contribute to this style guide, open an issue and a pull request that tracks the issue and addresses the change you would like to see. If a consensus is made to institute the change you may merge your request. 

-----

### Intent

This is an attempt to encourage patterns that accomplish the following goals (in
rough priority order):

1. Increased rigor, and decreased likelihood of programmer error
1. Increased clarity of intent
1. Reduced verbosity
1. Fewer debates about aesthetics

------

### Whitespace

* Spaces not tabs.
    * 4 spaces.
    * Spaces are uniform, tabs not always
* End files with a newline.
* Make liberal use of vertical whitespace to divide code into logical chunks.
* Don’t leave trailing whitespace.
    * Not even leading indentation on blank lines.
* Always include 1 space after a colon.
    * var myType: MyType = getMyType(fromString: "MyType")

-----

### Conditionals

#### Bindings

Prefer let-bindings over var-bindings wherever possible.

Use `let foo = …` over `var foo = …` wherever possible (and when in doubt). Only use `var` if you absolutely have to (i.e. you *know* that the value might change, e.g. when using the `weak` storage modifier).

_Rationale:_ The intent and meaning of both keywords is clear, but *let-by-default* results in safer and clearer code.

A `let`-binding guarantees and *clearly signals to the programmer* that its value will never change. Subsequent code can thus make stronger assumptions about its usage.

It becomes easier to reason about code. Had you used `var` while still making the assumption that the value never changed, you would have to manually check that.

Accordingly, whenever you see a `var` identifier being used, assume that it will change and ask yourself why.

#### If/Else vs. Guard

When you have to meet certain criteria to continue execution, try to exit early. So, instead of this:

When you have to meet certain criteria to continue execution, try to exit early. So, instead of this:

```swift
if n.isNumber {
    // Use n here
} else {
    return
}
```

use this:
```swift
guard n.isNumber else {
    return
}
// Use n here
```

You can also do it with `if` statement, but using `guard` is preferred, because `guard` statement without `return`, `break` or `continue` produces a compile-time error, so exit is guaranteed.

-----

### Optionals

#### Force-Unwrapping of Optionals

Avoid Using force-unwrapping of optionals.

If you have an identifier `foo` of type `FooType?` or `FooType!`, don't force-unwrap it to get to the underlying value (`foo!`) if possible.

Instead, prefer this:

```swift
if let foo = foo {
    // Use unwrapped `foo` value in here
} else {
    // If appropriate, handle the case where the optional is nil
}
```

Alternatively, you might want to use Swift's Optional Chaining in some of these cases, such as:

```swift
// Call the function if `foo` is not nil. If `foo` is nil, ignore we ever tried to make the call
foo?.callSomethingIfFooIsNotNil()
```

_Rationale:_ Explicit `if let`-binding of optionals results in safer code. Force unwrapping is more prone to lead to runtime crashes.

#### Implicitly Unwrapped Optionals

Avoid using implicitly unwrapped optionals

Where possible, use `let foo: FooType?` instead of `let foo: FooType!` if `foo` may be nil (Note that in general, `?` can be used instead of `!`).

_Rationale:_ Explicit optionals result in safer code. Implicitly unwrapped optionals have the potential of crashing at runtime.

-----

### Definitions & Declarations

#### Getters & Setters

Prefer implicit getters on read-only properties and subscripts.

When possible, omit the `get` keyword on read-only computed properties and
read-only subscripts.

So, write these:

```swift
var myGreatProperty: Int {
    return 4
}

subscript(index: Int) -> T {
    return objects[index]
}
```

… not these:

```swift
var myGreatProperty: Int {
    get {
        return 4
    }
}

subscript(index: Int) -> T {
    get {
        return objects[index]
    }
}
```

_Rationale:_ The intent and meaning of the first version is clear, and results in less code.

#### Top-Level Access Control Definitions

Always specify access control explicitly for top-level definitions.

Top-level functions, types, and variables should always have explicit access control specifiers:

```swift
public var whoopsGlobalState: Int
internal struct TheFez {}
private func doTheThings(things: [Thing]) {}
```

However, definitions within those can leave access control implicit, where appropriate:

```swift
internal struct TheFez {
    var owner: Person = Joshaber()
}
```

_Rationale:_ It's rarely appropriate for top-level definitions to be specifically `internal`, and being explicit ensures that careful thought goes into that decision. Within a definition, reusing the same access control specifier is just duplicative, and the default is usually reasonable.

#### Private Variable Prefixing

Prefix private attributes and methods with an underscore `_`.

```swift
internal struct MyDemoStruct {
    
    private let _name = "Demo"

    private func _printName() -> Void {
        print(_name)
    }
}
```

_Rationale:_ We can recognize the access specification of attributes and methods inline resulting in clearer code.

#### Conditional Assignment

Prefer immutability and closures for conditional assignments.

```swift
// Bad!
var isCompleteText: String

if self.isComplete {
    isCompleteText = "Complete!"
} else {
    isCompleteText = "Incomplete!"
}

// Good!
let isCompleteText = {
    if self.isComplete {
        return "Complete!"
    } else {
        return "Incomplete!"
    }
}()
```

_Rationale:_ Immutable values are safer than their mutable comrades. Closures create visual blocks for conditional assignments, improving readability and reducing clutter.

#### Type Specification

When specifying a type, always associate the colon with the identifier.

When specifying the type of an identifier, always put the colon immediately
after the identifier, followed by a space and then the type name.

```swift
class SmallBatchSustainableFairtrade: Coffee { ... }

let timeToCoffee: NSTimeInterval = 2

func makeCoffee(type: CoffeeType) -> Coffee { ... }
```

_Rationale:_ The type specifier is saying something about the _identifier_ so
it should be positioned with it.

Also, when specifying the type of a dictionary, always put the colon immediately
after the key type, followed by a space and then the value type.

```swift
let capitals: [Country: City] = [ Sweden: Stockholm ]
```

#### Type Parameters

Omit type parameters where possible.

Methods of parameterized types can omit type parameters on the receiving type when they’re identical to the receiver’s. For example:

```swift
struct Composite<T> {
    …
    func compose(other: Composite<T>) -> Composite<T> {
        return Composite<T>(self, other)
    }
}
```

could be rendered as:

```swift
struct Composite<T> {
    …
    func compose(other: Composite) -> Composite {
        return Composite(self, other)
    }
}
```

_Rationale:_ Omitting redundant type parameters clarifies the intent, and makes it obvious by contrast when the returned type takes different type parameters.

#### Reference to Self

Only explicitly refer to `self` when required.

When accessing properties or methods on `self`, leave the reference to `self` implicit by default:

```swift
private class History {
    var events: [Event]

    func rewrite() {
        events = []
    }
}
```

Only include the explicit keyword when required by the language—for example, in a closure, or when parameter names conflict:

```swift
extension History {
    init(events: [Event]) {
        self.events = events
    }

    var whenVictorious: () -> () {
        return {
            self.rewrite()
        }
    }
}
```

_Rationale:_ This makes the capturing semantics of `self` stand out more in closures, and avoids verbosity elsewhere.

#### Protocols First

Prefer protocols over structs and classes when possible.

Abstractions of functionality should start with a protocol. As described in [this WWDC talk](https://developer.apple.com/videos/play/wwdc2015/408/), protocol-oriented programming and value-type semantics are the heart of Swift's design.

For example this traditional OOP structure:

```swift
class Ordered {
    func precedes(other: Ordered) -> Bool { 
        fatalError("implement me!") 
    }
}

class Number : Ordered {
    var value: Double = 0
    override func precedes(other: Ordered) -> Bool {
        return value < (other as! Number).value
    }
}
```

Becomes much more robust and can be applied retroactively to existing entities through a protocol:

```swift
protocol Ordered {
    func precedes(other: Self) -> Bool
}

struct Number : Ordered {
    var value: Double = 0
    func precedes(other: Number) -> Bool {
        return self.value < other.value
    }
}
```

_Rationale:_ Using protocols helps us circumvent object-oriented issues such implicit sharing, inheritance issues, and lost type relationships. Furthermore protocols allow us to use value types, create static type relationships, and perform retroactive modeling.

#### Structs vs. Classes

Prefer structs over classes.

Unless you require functionality that can only be provided by a class (like identity or deinitializers), implement a struct instead.

Note that inheritance is (by itself) usually _not_ a good reason to use classes, because polymorphism can be provided by protocols, and implementation reuse can be provided through composition.

For example, this class hierarchy:

```swift
class Vehicle {
    let numberOfWheels: Int

    init(numberOfWheels: Int) {
        self.numberOfWheels = numberOfWheels
    }

    func maximumTotalTirePressure(pressurePerWheel: Float) -> Float {
        return pressurePerWheel * Float(numberOfWheels)
    }
}

class Bicycle: Vehicle {
    init() {
        super.init(numberOfWheels: 2)
    }
}

class Car: Vehicle {
    init() {
        super.init(numberOfWheels: 4)
    }
}
```

could be refactored into these definitions:

```swift
protocol Vehicle {
    var numberOfWheels: Int { get }
}

func maximumTotalTirePressure(vehicle: Vehicle, pressurePerWheel: Float) -> Float {
    return pressurePerWheel * Float(vehicle.numberOfWheels)
}

struct Bicycle: Vehicle {
    let numberOfWheels = 2
}

struct Car: Vehicle {
    let numberOfWheels = 4
}
```

_Rationale:_ Value types are simpler, easier to reason about, and behave as expected with the `let` keyword.

#### Protocol Conformance

When possible define protocol conformance in an extension.

For example:

```
class MyAwesomeClass {}

extension MyAwesomeClass: UITableViewDelegate {
    // implemented functionality
}

extension MyAwesomeClass: UITableViewDatasource {
    // implemented functionality
}
```

_Rationale:_ Protocol conformances in separate per conformance extensions separates code in logical chunks based on responsibilities. 

#### Enum Cases

Never provide a default case for type switching on enumerations unless absolutely intentional/necessary.

For example:

```swift
enum MyAmazingType {
    case Amazing
    case DoubleAmazing
}

var amazingType = MyAmazingType.Amazing

// Bad!
switch amazingType {

    case .Amazing:
        // ...
    case .DoubleAmazing
        // ...
    default:
        // ...
}

// Good!
switch amazingType {

    case .Amazing:
        // ...
    case .DoubleAmazing
        // ...
}
```
_Rationale:_ Providing a default case doesn't take advantage of of the compiler's ability to detect new types and alert you that they're not handled properly.

#### Final by Default

Make classes `final` by default.

Classes should start as `final`, and only be changed to allow subclassing if a valid need for inheritance has been identified. Even in that case, as many definitions as possible _within_ the class should be `final` as well, following the same rules.

_Rationale:_ Composition is usually preferable to inheritance, and opting _in_ to inheritance hopefully means that more thought will be put into the decision.

#### Operator Definitions

Use whitespace around operator definitions.

Use whitespace around operators when defining them. Instead of:

```swift
func <|(lhs: Int, rhs: Int) -> Int
func <|<<A>(lhs: A, rhs: A) -> A
```

write:

```swift
func <| (lhs: Int, rhs: Int) -> Int
func <|< <A>(lhs: A, rhs: A) -> A
```

_Rationale:_ Operators consist of punctuation characters, which can make them difficult to read when immediately followed by the punctuation for a type or value parameter list. Adding whitespace separates the two more clearly.

#### Implement `CustomStringConvertible`

If your type requires a `String` representation, adopt `CustomStringConvertible`.  Instead of:

```swift
@objc public enum Foo: Int {
   case Baz: 0
   case Bap
   case Bar
   
   func asString() -> String {
      switch self {
         case .Baz:
            return "Baz"
         case .Bap:
            return "Bap"
         case .Bar:
            return "Bar"
   }
}

print("the enum value is \(Foo.Bar.asString())") // prints "the enum value is Bar"
```

write:

```swift

private struct Constants {
   static let Baz = "Baz"
   static let Bap = "Bap"
   static let Bar = "Bar"
}

@objc public enum Foo: Int {
   case Baz: 0
   case Bap
   case Bar
}

extension Foo: CustomStringConvertible {
   public var description: String {
      switch self {
         case .Baz:
            return Constants.Baz
         case .Bap:
            return Constants.Bap
         case .Bar:
            return Constants.Bar
   }
}

print("the enum value is \(Foo.Bar)") // prints "the enum value is Bar"
```

_Rationale:_ Swift has inherited the `description` (and `debugDescription`) method API from Objective-C, which is useful for providing a meaningful `String` description of instances.  These API are available in `CustomStringConvertible` and `CustomDebugStringConvertible`.

