# Protocol oriented programming

Protocol-oriented programming is a new approach to developing applications in
Swift. It's not a new paradigm, nor was it introduced by Swift. It's simply
a new name for a cool concept which can be very useful in abstracting away
the complexites of our apps. It also allows us to avoid certain object-oriented
pitfalls such as the **fragile base class** issue and **god class** issue when
dealing with inheritance.

# Networking layer with protocol-oriented programming

## End result

The end result of this approach will be that we'll be able to call our service by
doing the following:

```swift
UserModel.fetch(Router.getUser(id: 1200), onSuccess { result in
    if case let .asSelf(model) = result {
        print(model.name)
    }
}, onError: { error in
    print(error)
})
```

## Setup

I found the paradigm to be really useful in creating an expressive networking
layer for my latest application

For this you'll need:

* Unbox for deserialization https://github.com/JohnSundell/Unbox
* Alamofire for accessing the network https://github.com/Alamofire/Alamofire

The steps are:

* Make a protocol called `Fetchable` which will define classes that can be
fetched from the server and parsed
* Make a `Router` enum which will conform to Alamofires `URLRequestConvertble`

Let's assume our endpoints are

* `http://www.example.com/api/users` for a list of users
* `http://www.example.com/api/user/<id>` for a specific user
* `http://www.example.com/api/products?limit=<limit>&offset=<offset>` for a list of products where `limit` is an `int` parameter for a number of elements and `offset` is an `int` parameter for the starting position of the element
* `http://www.example.com/api/product/<id>` for a single product

## `Fetchable` protocol

First we'll make a helper `enum` so we can support root arrays and dictionaries

```swift
enum MappingResult<T> {
    case asSelf(T)
    case asDictionary([String: T])
    case asArray([T])
    case raw(Data)
}
```
The unboxer will try to deserialize the result in the same order that it was defined in the `enum`

We'll define `Fetchable` as an empty protocol

```swift
protocol Fetchable {}
```

and implement the `fetch(...)` function in the protocol extension

In order to do that, we need to implement a protocol extension with a `Self` constraint. In this context - `Self` refers to a `struct`, `class` or `enum` implementing the `Fetchable` protocol.

We *constrain* `Self` to be `Unboxable` (`Unboxable` is a protocol in the Unbox library - you could've just as easily constrained `Self` to `Mappable` if you were using ObjectMapper or any other protocol).

So our protocol extension signature looks like this

```swift
extension Fetchable where Self: Unboxable { ... }
```

This means that when we use `Self` as a type in the extension - we can access
all the methods that are defined in the `Unboxable` protocol

So let's go and create the `fetch(...)` function.
First off - we'll define some helpful typealiases

```swift
typealias ErrorHandler = (Error) -> Void
typealias SuccessHandler<T> = (MappingResult<T>) -> Void where T: Unboxable
```

These are used just for convenience

The `fetch` function will look like this

```swift
static func fetch(with request: URLRequestConvertible, onSuccess: @escaping SuccessHandler<Self>, onError: @escaping ErrorHandler){

        Alamofire.request(request).responseJSON { response in
            if let errorData = response.result.error {
                onError(errorData)
                return
            }
            if let data = response.data {
                do {
                    let mapped: Self = try unbox(data: data)
                    onSuccess(.asSelf(mapped))
                } catch {
                    do {
                        let json = try JSONSerialization.jsonObject(with: data, options: []) as? [String: [String: Any]]
                        var mappedDictionary = [String: Self]()
                        try json?.forEach { key, value in
                            let data: Self = try unbox(dictionary: value)
                            mappedDictionary[key] = data
                        }
                        onSuccess(.asDictionary(mappedDictionary))
                    } catch {
                        do {
                            let mapped: [Self] = try unbox(data: data)
                            onSuccess(.asArray(mapped))
                        } catch {
                            do {
                                onSuccess(.raw(data))
                            } catch {
                                onError(error)
                            }
                        }
                    }
                }
            }
        }

    }
```

Now let's define our models:

```swift
struct ListItemModel : Fetchable, Unboxable {
    let id: Int
    let name: String

    init(unboxer: Unboxer) throws {
        id = try unboxer.unbox(key: "id")
        name = try unboxer.unbox(key: "name")
    }
}

struct UserModel: Fetchable, Unboxable {
    let fistName: String
    let lastName: String

    init(unboxer: Unboxer) throws {
        firstName = try unboxer.unbox(key: "first_name")
        lastName = try unboxer.unbox(key: "last_name")
    }
}

struct ProductModel: Fetchable, Unboxable {
    let sku: String
    let weight: Int

    init(unboxer: Unboxer) throws {
        sku = try unboxer.unbox(key: "sku")
        weight = try unboxer.unbox(key: "weight")
    }
}
```

The `Router` is defined as a classic swift `enum` router. Plenty of other tutorials
that cover that

And voilà you can now call the endpoints with the super-expressive

```swift
UserModel.fetch(Router.getUser(id: 150),
onSuccess: { result in

}, onError: { error in

})
```

And they are completely parsed and ready to use.
