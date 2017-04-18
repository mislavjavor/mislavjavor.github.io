# Introduction

Without doubt, styling our code properly is not a task to be pushed aside.
Often times, we'll just type away - leaving the file when the code compiles and
does what we expect it to do.

But during all of this - we mustn't forget:
> Code is written once by one person, but read multiple times by multiple people

>-- Some smart guy

General code style guides and Swift code style guides are commonplace
and I don't mean to repeat them. For now I'll just demonstrate two
cool patterns that I wish I saw more often


# 1. "Nested" guard

The pattern looks like this:

Let's assume we're working with optional-heavy code. This could turn into
tower-of-doom `if let` chains. *Or* we could use guard and have a tidy
code structure where the code shape encapsulates functionality

```swift
func foo(optionalA: String?, optionalB: String?, model: Model?) throws {
    guard
      let firstName = optionalA,
      let address = model?.address,
      let color = model.color
    else {
      // Handle error
    }
}
```

# 2. "Static" structures

Sometimes we'll use our struct as a type of namespace or value holder type.
We don't want them to be initializable. One way to ensure this is by
creating a `private` constructor. We can put the constructor anywhere
in the code - but I think the best place to put it inline with the
declaration of the struct. That way everything it's crystal clear what
the semantic purpose of the struct is

```swift
struct ColorUtilities { private init(){}
    static func darkenColor(color: UIColor) {
        // implemntation
    }
}

struct Constants { private init(){}
    static let baseURL = "https://api.mysite.com/v1/"
}
```

# Conclusion

Writing readable code isn't hard and it will help you in the long run.
Always think how readable the code will be before writing it

Happy Coding 💻
