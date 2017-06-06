---
layout: post
title: 6 Reasons Why F# Might Be The Coolest Language You've Never Heard Of
---

<img src="http://fsharp.org/img/logo/fsharp256.png"/>

# Introduction

Ever heard of C#? Of course you have. 

The Microsoft-made Java/C based language
is everywhere. It's used in everything from 

- Web Pages (ASP.NET, Nancy,
... )
- Desktop apps (WinForms, WPF, ...)
- Databases
- Mobile apps (Xamarin).

But, hidden in it's shadow, there is another member of the
'sharp' family - F#. A functional-first, C# compatible, .NET language.
And a great one at that. 

Developed by Microsoft researcher Don Syme and released in it's 1.x
iteration in May 2005, F# failed to gain mainstream traction. The main
reason was the dominance of curly brace syntax, object oriented languages
(C++, Java, C#) through the major part of the 00's. A contributing factor
was also a general disinterest of the programming public in functional
languages. Often described as purely academic in nature, functional languages
are still struggling to gain traction in the software mainstream although
progress is being made - mostly by adding functional features to imperative languages (notable examples being Java 8 lambdas, Rust,
Swift etc...). 

However, F# couldn't be further from a purely academic language like
Haskell. It's a very concise, pragmatic language - taking the best 
features from object oriented, functional and imperative programming 
languages. Add to that, a fantastic type inference system and extremely
refined type system and - to me at least - we've got ourselves a winner on our hands.

# Disclaimer

I'm not a seasoned F# developer - I've discovered it a while ago while
playing around with ASP.Net for macOS and found it fascinating. That
being said - I took the effort of validating the correctness of all the
claims in this post

# 1. F# is beautifully simple and pragmatic

As we'll see - F# is not only safe and expressive. It's also endlessly
pragmatic.

For example - you declare a variable in F# by using the `let` keyword

```ocaml
let greeting = "Hello world"
```

Functions, being true first-class citizens are declared in a very
similar way

```ocaml
let createHelloWorld () =
    let hello = "hello"
    let world = "world"
    hello + " " + world

createHelloWorld ()
```



In this example the `let` keyword creates a function which takes
zero arguments and returns a `string`. It returns `"Hello world"` every
time it's invoked. 

Note that F# uses indentation instead of brackets to scope code. Also
every statement returns a value and the last statement in the function
is the return value of the function - so there is no need for a `return`
keyword.

If we wanted to declare a function which takes some parameters - we'd
do it like this:

```ocaml
let multiplyTwoNumbers x y = x * y
```

This creates a function called `multiplyTwoNumbers` which takes
two parameters and returns their multiple.

You call the function by simply doing:

```ocaml
multiplyTwoNumbers 12 10 // returns 120
```

This uniform way of defining functions and variables and looking
at them as being essentially the same thing has far reaching implications
in how we program our code. It allows us to look at 

```ocaml
let greeting = "Hello world"
```

as simply a function which takes no arguments and always evaluates to
`"Hello world"`. And that makes our code _simple_ . Now as Clojures 
Rich Hickey concluded - simple doesn't mean easy. 

It might just be
that it's easier to understand functions when looking at them as being
C style functions. But having functions and variables be different 
concepts, with differing semantics - means our underlying system is
complex. It also means that any attempts to do things which naturally
arise from the simple language will be very hard to do in an easy one.

# 2. Type inference in F# is out of this world

Dynamic languages are a breeze to code in, but are harder to maintain
and often cause headaches during runtime. 

Static languages enable us to inspect the correctness of our code during
compile time, but they tend to be overly verbose and force developers to
make all sorts of hacks to achieve desired effects. 

The position of F# is, as always, the best of both worlds. Type safety
without verbosity.

Let's look at an example of a simple function

```ocaml
let adder x = x + 12
```

Now as you can see - we wrote no type annotations anywhere. But the 
compiler inferred that the function signature is `x: Int -> Int`. It 
inferred the return type _and_ the parameter type just from the fact that
we provided it with the number `12` which is an `int` literal.

We can take this even further. F# can express data holders with _record_
types. Let's say we define a type called `Address`. The syntax is pretty
self-explanatory:

```ocaml
type Address = {
    country: string;
    city: string;
    street: string;
}
```

Now you'd imagine that we would have to explicitly instantiate the 
`Address` type in order for the compiler to know what it needs to put
in a variable? Well, guess again. 

```ocaml
let mainFlat = {
    country = "Croatia";
    city = "Zagreb";
    street = "Nikola Tesla Street";
}
```

This is enough to have the `mainFlat` variable evaluate to an `Address`
type. The compiler is smart enough to _infer_ unique constraints that
this puts on the type and see that only the `Address` type satisfies 
those constraints. 


# 3. F# functions can be partially applied out-of-the-box

Currying is great. If you disagree - here is a Koan for you 

>A student came to Jacques Garrigue and said, "I do not understand what currying is good for." Jacques replied, "Tell me your favorite meal and your favorite dessert". The puzzled student replied that he liked okonomiyaki and kanten, but while his favorite restaurant served great okonomiyaki, their kanten always gave him a stomach ache the following morning. So Jacques took the student to eat at a restaurant that served okonomiyaki every bit as good as the student's favorite, then took him across town to a shop that made excellent kanten where the student happily applied the remainder of his appetite. The student was sated, but he was not enlightened ... until the next morning when he woke up and his stomach felt fine.
>
>  - [Functional programming Koans in OCaml](http://web.archive.org/web/20041012103936/http%3A//www.bagley.org/~doug/ocaml/Notes/okoans.shtml)

Currying allows you to partially apply functions - creating new functions. Once you
accept currying and start using it - you will have much less repetition in your code
and stay comfortably [DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)

So, how does it look?

```ocaml
(* Create a function which takes
   two parameters *)
let append (suffix: string) (input: string) =
    input + suffix

(* Call that same function with only one parameter
// When we do that - the `colonSuffix` function is now
// a function which takes one argument. It has been
// 'partially applied' *)
let colonSuffix = append ":"


let inputFields = ["name";"age";"address"]

let formattedInputFields = 
    (* We pass the colonSuffix function to List.map
    // which expects a function for transforming a string
    // into a string (since inputFields is an array of strings) *)
    inputFields |> List.map colonSuffix

(* formattedInputFields is now ["name:";"age:";,"address:"] *)
```

This was a very simple example (not at all a useful one), but it serves the
purpose of demonstrating the concept quite nicely. 

# 4. F# is immutable by default and copying couldn't be easier

In this day and age of multi-core processors and distributed data - programming
with the expectation of concurrency is a must. Unfortunately most of our tools aren't really meant for the job. 

The best example for this are imperative object oriented languages. The issues
that arise when working in a parallel environment are mostly about handing _state_. </br>
There have been many solutions for handling asynchronous states in imperative languages. Most notably, [locks](https://docs.oracle.com/javase/7/docs/api/java/util/concurrent/locks/Lock.html)

Properly written functional code essentially doesn't have that issue (it can have
many other issues though - that's why F# being pragmatic as it is - actually
allows us to declare mutable variables)

Here are some examples:

```ocaml
type Person = {
    name: string;
    age: int;
}

(* `let` keyword automatically creates
// an immutable value in the `mike` identifier *)
let mike = {
    name = "Mike";
    age = 32
}

(* trying to change the value
// causes a compiler error `this value
// is not mutable` *)
mike <- {
    name = "John";
    age = 23
}

(* it's also impossible to change 
// underlying properties *)
mike.age <- 23

(* if we want to get some new values
// we must copy the contents of `mike`
// into `john`. Fortunately it's
// comparatively easy to do this in F# *)
let john = { mike with name = "John" }
```


# 5. F# has access to the entire .NET library

Alongside JDK, .NET is one of the most comprehensive development libraries
in the world. This is actually one of the key points differentiating F# from
other functional languages. 

Not only does this accelerate the development process - it also makes it 
easier for existing .NET developers to transition to F#

# 6. F# is a cross-platform, open source, tested and mature platform - ready for prime time on desktop, mobile and web

As I've mentioned in the intro paragraph - F# has been with us for over a decade.
In that time it's been battle-tested, run on numerous production platforms. 

In all of those cases it proved itself to be a indispensable tool, enabling 
developers to write expressive, more secure code. 

Currently you can develop using F# on all major platforms (I run it on macOS using
Visual Studio Code and Ionide plugin). And you can also develop _for_ all major 
platforms. Here are some examples:

- On desktop - all .NET windows platforms are available

- On mobile - you can use Xamarin

- On web - you can use ASP.NET


Thanks to Reddit user /u/FinnNk for pointing out some errors