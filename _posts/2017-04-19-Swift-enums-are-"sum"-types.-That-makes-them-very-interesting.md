<script src='https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.0/MathJax.js?config=TeX-MML-AM_CHTML'></script>

# Algebraic types - what are they?

Algebraic types... seems like the kind of math-y work Haskell programmers
would use. But in reality you don't need to know type theory or have a
PhD in mathematics to understand them. Algebraic types arent *new* types. They are simply a new way of thinking
about the types you already know about.

There are many different algebraic types - in fact - all of the types
you currently use are algebraic as well. Here, we'll two basic algebraic
types
- Product types
- Sum types

So let's start with the familiar stuff.

## Product types

Product types are nothing more that Swift `struct` types, or Java `class`
types. <br/>
They are called *product* types because the number of possible values they
can have is the product of the number of possible values of their
constituent parts

```swift
struct ProductExample {
  let a: Bool
  let b: Bool
}
```

So a `Bool` type can have 2 possible values. `ProductExample` has 2 `Bool`
types. We can get the number of possible values of `ProductExample` by
multiplying the number of possible values of `Bool` `a` with the number
of possible values of `Bool` `b`. So the number of possible values of
`ProductExample` is $$2 \times 2$$ which is $$4$$

This is evident with all the possible instances of the type:

```swift
let first = ProductExample(a: true, b: true)
let second = ProductExample(a: true, b: false)
let third = ProductExample(a: false, b: true)
let fourth = ProductExample(a: false, b: false)
```

Let's look at another example:

```swift
struct ProductExampleTwo {
  let a: Bool
  let b: Int8
}
```

Now our type `ProductExampleTwo` has the number of possible values which
is a multiple of `Bool` and `Int8`. `Int8` has $$256$$ possible values, `Bool` has $$2$$ possible values. So our `ProductExampleTwo` has $$512$$
possible values.

In general, without going into type theory notation, we can define a function $$N_{pv}$$ which for a given type - returns the number of possible
values that type has. So:

{% raw %}
\[
N_{pv}(Bool) = 2
\]
\[
N_{pv}(Int8) = 256
\]
\[
N_{pv}(String) = \infty
\]
{% endraw %}

The conclusion that $$N_{pv}(String) = \infty$$ has certain implications in
the way we think about types, but those can be omitted for the current
article without compromising the general message

If we use the $$N_{pv}$$ function - we can express a general case for all product types.

Let's assume there exists type $$T$$ which has constituent parts $$T_1, T_2, T_3, ... , T_n$$. $$T$$ can be considered a product type if:

\\[
N_{pv}(T) = N_{pv}(T_1) \times N_{pv}(T_2) \times N_{pv}(T_3) \times ... \times N_{pv}(T_n)
\\]

Or in a more proper notation:

\[
N_{pv}(T) = \prod_{i = 1}^{n}N_{pv}(T_i)
\]

## Sum types

If you're, by any chance, unfamiliar with enum syntax - there is a quick
and dirty intro at the bottom of the article.


So if `struct`-s were product types - then what are sum types? <br/>
Simple - **Sum types (in swift) are enums!**

**Number of possible values of a sum type is the sum of the number of
possible values of it's constituent parts**

Now that we know how to deal with the algebraic view of types, and have
our $$N_{pv}$$ function handy - let's explore the world of sum types with
examples.

```swift
enum SumExample {
  case a(Bool)
  case b(Bool)
}
```

Let's see all the different ways we can instantiate the `SumExample` enum.

```swift
let first = SumExample.a(true)
let second = SumExample.b(true)
let third = SumExample.a(false)
let fourth = SumExample.b(false)
```

There are $$4$$ ways to instantiate the `SumExample` enum. This number comes
from the fact that $$N_{pv}(Bool) = 2$$ and that `SumExample` contains two
`Bool` types and that $$2 \times 2 = 4$$

Let's examine a different example:

```swift
enum SumExampleTwo {
  case a(Bool)
  case b(Int8)
}
```

Now what is the number of possible values of `SumExampleTwo`? It's the
*sum* of possible values of `Bool` and `Int8`. So

\[
N_{pv}(Bool) = 2 \\
N_{pv}(Int8) = 256 \\
N_{pv}(SumExampleTwo) = N_{pv}(Bool) + N_{pv}(Int8) \\
N_{pv}(SumExampleTwo) = 2 + 256 \\
N_{pv}(SumExampleTwo) = 258
\]

Expressing a general case:

Let's assume there exists a type $$T$$ with constituent parts $$T_1, T_2, T_3, ... , T_n$$ . $$T$$ can be considered a sum type if:

\[
N_{pv}(T) = N_{pv}(T_1) + N_{pv}(T_2) + N_{pv}(T_3) + ... + N_{pv}(T_n) \\
\]

or in a more proper notation:

\[
N_{pv}(T) = \sum_{i = 1}^{n}N_{pv}(T_i)
\]

## How can I use it to write better code?

All this theoretical stuff is fine and dandy - but let's see a little bit
of practical examples.

So in general you wan't to follow the mantra: <br/>
The number of possible values of your type should be equal to the number
of possible values of your use case.


### 1. Result enum

So what does this mean?

Let's assume you're making a REST call and you're getting back some `String` as a result. A bad way to write this would be:

```swift
typealias Handler = (String?, Error?) -> Void

func getUser(from: URL, completionHandler: Handler) {
  // function implementation
}

getUser(from: someUrl) { result, error in
  if let result = result {
    // Handle result
  }
  if let error = error {
    // Handle error
  }
}
```
Why is this a bad option? Because our *use case* has two possible values:
- Success - result was fetched from the server
- Error - Something went wrong during the process

And our *implementation* has four possible values:
```swift
result = nil, error = not nil // Case 1
result = not nil, error = nil  // Case 2
result = not nil, error = not nil // Case 3
result = nil, error = nil // Case 4
```

We have to think about the semantics of this approach. We are actually
counting only on two cases the `Case 1` and `Case 2`. What does it mean
when both result and error are `nil`? Or when they're both not `nil`.

The problem is - we're using a product type where we should be using a
sum type. The solution?

```swift
enum Result {
    case success(String)
    case error(Error)
}

typealias Handler = (Result) -> Void

func getUser(from: URL, completionHandler: (Handler)) {
  // implementation
}

getUser(from: someUrl) { response in
    switch response {
    case .success(let result):
        print(result)
    case .error(let error):
        print(error.localizedDescription)
    }
}
```

We've created a sum type called `Result` and we're using it to distinguish
between two possibilities. Our use case matches our implementation and all
is good.

### 2. Optional enum

You might not have known that Swift has an inbuilt sum type that we use
almost all of the time - Optional.

The optionals we know and love (or sometimes hate) are implemented inside
of the Swift language as an `enum`

```swift
enum Optional<T> {
  case some(T)
  case none
}
```

so a `let a: String? = "Hello"` wold just be shorthand syntax for
`let a = Optional.some("Hello")`.

Good thing is that swift has some neat syntax sugar to help us out on our
distinguishing of sum types - the `if let` and `guard let` constructs.

This

```swift
let a: String? = "Hello"

if let a = a {
    print(a)
} else {
    print("error")
}
```

is equivalent to this:

```swift
let a = Optional.some("Hello")

switch a {
case .some(let res):
  print(res)
case .none:
  print("Error")
}
```

### 3. Router and theme patterns

Some things in your apps have a finite number of possibilities and are
ultra easy to express as sum types. Like for example API endpoint router.

```swift
enum Router {
    case user(id: Int)
    case weather(day: Day)
}

extension Router {
    var url: String {
        switch self {
        case .user(let id):
            return "\(App.BaseUrl)/user/\(id)"
        case .weather(let day):
            return "\(App.BaseUrl)/weather/\(day.rawValue)"
        }
    }
}
```

Your router could use this pattern to expose everything from paramteres,
headers, request types etc...

And now a theme patten if you need themes:

```swift
struct AppThemeModel {
    let baseColor: UIColor
    let backgroundColor: UIColor
    let accentColor: UIColor
    let baseFont: UIFont
}

enum AppTheme {
    case dark
    case light

    var model: AppThemeModel {
      switch self {
      case .dark:
        return AppThemeModel(
          baseColor: .red
          backgroundColor: .darkRed
          accentColor: .yellow
          baseFont: .systemFontOfSize(12)
        )
      case .light:
        AppThemeModel(
          baseColor: .white
          backgroundColor: .gray
          accentColor: .blue
          baseFont: .systemFontOfSize(13)
        )
      }
    }
}


// During app init
var currentAppTheme = AppTheme.dark

```

### 3. Implementing data structures

Implementing trees and linked lists using sumt types in swift is ultra
easy

```swift
indirect enum Tree<T> {
    case node(T, l: Tree, r: Tree)
    case leaf(T)

    var l: Tree? {
      switch self {
      case .node(_, l: let l, _):
        return l
      case .leaf(_):
        return nil
      }
    }

    var r: // equivalent implementation to l

    var value: T {
      switch self {
      case .node(let val, _, _):
        return val
      case .leaf(let val):
        return val
      }
    }
}

let tree = Tree.node(12, l: Tree.leaf(11),
                         r: Tree.node(34, l: Tree.leaf(34),
                                          r: Tree.leaf(55)))
```
----

#### Enums syntax quick & dirty

Swift enums can be written like this:

```swift
enum Foo {
  case a
  case b
}

let sth = Foo.a
let oth = Foo.b
```

But can be also written like this:

```swift
enum Foo {
  case a(String)
  case b(isEnabled: Bool)
}

let sth = Foo.a("Hello")
let oth = Foo.b(isEnabled: false)
```
