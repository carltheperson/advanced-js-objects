# Chapter 4 - Internal Object behavior


Peeking into the internals of Objects can be a good way to understand how they work. Specifically, every JavaScript Object has *internal methods.* These methods get called when you do pretty much anything with Objects.
The names of internal methods are labelled by enclosing brackets ([ ]), e.g. [[HasProperty]]. This is to distinguish them from regular Object methods.
You can’t actually call an internal method in your JavaScript program. This is because they exist outside the execution context of your code. They are called by your JavaScript *engine*, not your code. Still, it’s helpful to understand *when* they are called by the engine.

(Picture of an iceberg? Your code vs internals)

- [ ]  Terminology. Internal / external?

## Essential internal methods

Some internal methods exist on every single Object. These are called *essential internal methods*. 

Below is a table with all essential internal methods. The arguments and return type are written with a notation that resembles an arrow function. An arrow (`=>`) separates the arguments from the return type. The pipe symbol (`|`) is used when a value can be multiple possible types. Square brackets (`[]`) after a type means an Array of that type.

| Internal Method | Arguments- and return type | Description |
| --- | --- | --- |
| [[GetPrototypeOf]] | () => Object | Null | Get the Prototype of the Object. |
| [[SetPrototypeOf]] | (Object | Null) => Boolean | Set a Prototype on the Object. |
| [[IsExtensible]] | () => Boolean | Check if the Object is extensible. |
| [[PreventExtensions]] | () => Boolean | Make the Object non-extensible. |
| [[GetOwnProperty]] | (Key) => Descriptor | Undefined | Get the descriptor of a property. |
| [[DefineOwnProperty]] | (Key, Descriptor) => Boolean | Define a property with a descriptor. |
| [[HasProperty]] | (Key) => Boolean | Check if a property exists. |
| [[Get]] | (Key, Receiver) => Any | Get the value of a property. |
| [[Set]] | (Key, Any, Receiver) => Boolean | Change the value of a property. |
| [[Delete]] | (Key) => Boolean | Delete/remove a property. |
| [[OwnPropertyKeys]] | () => Key[] | Get all property keys. |

As you can see, some types are different than the ones we defined in chapter one. This is because they are internal and separate from the language types you use in your code. All of the internal types can be converted into language types and vise versa. The new internal types introduced above are:

- `Key`. A property key. Is converted to/from a String or Symbol.
- `Any`. Can be any external type. All instances of this type above represent property values.
- `Descriptor`. The descriptor of a property. Is converted to/from an Object.
- `Receiver`. This argument is always optional. If specified, it will become the `this` of the function. If not, `this` will be the Object the method is called on. It will be covered more later.

## Internal methods of functions

As you know already, functions are Objects. But functions do also have extra internal functionality. Specifically, they have an extra internal method called [[Call]].
The [[Call]] method is executed internally when the function is called in your code.

| Internal method | Arguments- and return type |
| --- | --- |
| [[Call]] | (Any, Any[]) => Any |
- The first argument will be the `this` of the function.
- The second argument is a list of values which are passed to the function during the function call. Meaning, the second internal argument contains the arguments to the function as we know it.
- The return value is the return value of the function.

(Make this more clear)

## Internal methods of constructors

| Internal method | Arguments- and return type |
| --- | --- |
| [[Construct]] | (Any[], Object) => Object |

## Reflect: The internal method wrapper

As mentioned before, you can’t call internal methods because, well, they’re internal. But you can sort of call them with the help of `Reflect`. `Reflect` provides minimal wrappers over the internal methods. Meaning, we can call the internal methods *through* `Reflect`. This effectively bridges the gab between the internal methods and our code.

You will notice that `Reflect` has a lot of similar functions as `Object`. `Object` is more utility focused and doesn’t cover all the internal methods. It also, unlike `Reflect`, has functions than don’t correspond directly with internal methods.

Below, you will see a table with all the internal methods and their corresponding `Reflect` function. The `Object` function is also listed if it exists.

| Internal Method | Reflect function | Object function |
| --- | --- | --- |
| [[GetPrototypeOf]] | Reflect.getPrototypeOf | Object.getPrototypeOf |
| [[SetPrototypeOf]] | Reflect.setPrototypeOf | Object.setPrototypeOf |
| [[IsExtensible]] | Reflect.isExtensible | Object.isExtensible |
| [[PreventExtensions]] | Reflect.preventExtensions | Object.preventExtensions |
| [[GetOwnProperty]] | Reflect.getOwnPropertyDescriptor | Object.getOwnPropertyDescriptor |
| [[DefineOwnProperty]] | Reflect.defineProperty | Object.defineProperty |
| [[HasProperty]] | Reflect.has |  |
| [[Get]] | Reflect.get |  |
| [[Set]] | Reflect.set |  |
| [[Delete]] | Reflect.deleteProperty |  |
| [[OwnPropertyKeys]] | Reflect.ownKeys |  |
| [[Call]] | Reflect.apply |  |
| [[Construct]] | Reflect.construct |  |

All of the `Reflect` functions takes in an Object as their first argument. This is the Object that the internal method should be called on. The other arguments will match the ones the internal method has.
An example is the internal method [[HasProperty]] which takes in a single `Key` argument. The corresponding `Reflect.has` function takes in two arguments. The first one being the Object that the [[HasProperty]] should be called on*.* The second argument being the actual argument to [[HasProperty]].

```jsx
const obj = { a: "A" }
console.log(Reflect.has(obj, "a")) // true
```

## Ordinary objects vs exotic objects

In JavaScript, not all Objects are created equal. Most Objects are what’s called *Ordinary Objects*. These include the ones you create yourself. Other Objects are what’s called *Exotic Objects*. Exotic Objects have different internal behavior than Ordinary Objects. Specifically, they have a different implementation of some of their internal methods. This allows them to have special internal functionality that isn’t available to Ordinary Objects.
Here are all the Exotic Objects:

- Bound Function Objects
- Array Objects
- String (primitive wrapper) Objects
- Argument Objects
- Integer-Indexed Objects
- Module Namespace Objects
- Immutable Prototype Objects
- Proxy Objects

Two of these will be explained further below (Array- and Proxy Objects).

### Arrays (exotic Objects)

Arrays are exotic Objects that have a different [[DefineOwnProperty]] method than ordinary Objects. The [[DefineOwnProperty]] method is used internally to define properties. For ordinary Objects this method is quite straight forward; a property is defined with a key, value, and descriptor. The [[DefineOwnProperty]] of Arrays works the same but has special behavior for properties in two special cases:

- Case 1: The key is called `“length”`
- Case 2: The key is a valid Array index. To be this, the key needs to fulfill two criteria:
    - It should be a *numeric* String*.*  Meaning, a String that is a valid Number e.g. `“5”`
    - The numeric value is a positive Number e.g. `“10”` not `“-10”`.

>💡 The property values of an Array Object are also refereed to as Array *elements.*
>

>💡 It might sound strange to learn that valid Array indexes has to be a Strings. This is because we normally use Numbers to access/define Array elements. It might be helpful to think of two different kinds of Array indexes:
> - Internal Array indexes which has to be Strings.
> - External Array indexes which can be both Strings and Numbers.
> The reason external Array indexes can be both Strings and Numbers, is that Numbers are converted to Strings if used as property keys.
> 

#### Special case 1: The key is called “length”

It is possible to redefine the `length` property on your Arrays. In this case, the internal method will “cut off” extra elements that would make the Array exceed the new length.

```jsx
const arr = ["a", "b", "c"]
arr.length = 2

console.log(arr) // ["a", "b"]
```

In the above example, the last element `“c”` was removed from the Array. 
If you set `length` to be greater than the current length then no elements will be removed. This does create a bit of a strange case, however. In that case, the number of array elements won’t match the length of the array. Turns out this is perfectly valid.

```jsx
const arr = ["a", "b", "c"]
arr.length = 5

console.log(arr) // [ "a", "b", "c", <2 empty items> ]
```

Your browser might explain the difference between the number of elements and the length with `<x empty items>`. Don’t be fooled by that. These “empty items” don’t actually exist.

#### Special case 2: The key is a valid Array index

If you define a property with a valid Array index key, JavaScript will convert the key to a Number and compare it to the value of its `length` property.
If the numeric value of the key is greater then or equal to (`>=`) the value of `length`, it will redefine `length`. The value of `length` will be set to the numeric value of the key + 1.

```jsx
const arr = ["a", "b"]
console.log(arr.length) // 2

arr["2"] = "c"
console.log(arr.length) // 3
```

In the above example, we start be defining an Array with a length of 2. We then define a property using the key `“2”`. The length changes to the numeric value of `“2”` plus one (2 + 1 = 3).

The length will also be redefined if the numeric value is greater than the length. We can see this below where we define a property using the key `“5”` on an array with a length of 2. The length changes to 5 + 1 = 6.

```jsx
const arr = ["a", "b"]
console.log(arr.length) // 2

arr["5"] = "c"
console.log(arr.length) // 6
```

The length is now 6 but the amount of elements is still 3. Because of this, your browser will likely show some “empty items” inside the Array. Remember, these aren’t real elements.

```jsx
console.log(arr) // [ "a", "b", <3 empty items>, "c" ]
```

#### Normal property assignment

If a property is assigned to the Array Object that doesn’t fit the two special cases, it will be assigned normally. This won’t affect the length of the Array.

```jsx
const arr = ["a", "b", "c"]
arr["Hello"] = "World"

console.log(arr) // [ "a", "b", "c", Hello: "World" ]
console.log(arr.length) // 3
```

### Proxies (exotic Objects)

Proxies are the only exotic Objects that have a different implementation of every single essential internal method. When one of its internal methods are called, the Proxy can call a function of *your* choosing. You can essentially set up listeners that run custom code when internal methods are called. This is really powerful and allows you to create truly flexible Objects.

#### Proxy handlers

When you initialize a Proxy you give it a *Proxy handler,* which is an Object. You attach methods to this Object which will be called by the Proxy. A method on a Proxy handler is called a *trap*. A trap is specific to a certain internal method.

Below you will see a Proxy handler that implements every possible trap. This is not required. You only need to define the traps you need. The traps have a comment indicating which type should be returned from them.

```jsx
const proxyHandler = {
  getPrototypeOf: (target) => {}, // Object | Null
  setPrototypeOf: (target, proto) => {}, // Boolean
  isExtensible: (target) => {}, // Boolean
  preventExtensions: (target) => {}, // Boolean
  getOwnPropertyDescriptor: (target, key) => {}, // Object
  defineProperty: (target, key, desc) => {}, // Boolean
  has: (target, key) => {}, // Boolean
  get: (target, key, receiver) => {}, // Any
  set: (target, key, value, receiver) => {}, // Boolean
  deleteProperty: (target, key) => {}, // Boolean
  ownKeys: (target) => {}, // (String | Symbol)[]
  apply: (target, thisArg, args) => {}, // Any
  construct: (target, args, newTarget) => {} // Object
}
```

#### Trap name
The name of the trap is the same as the internal method’s corresponding `Reflect` function. An example could be the internal method [[HasProperty]], which has a corresponding `Reflect` function called `has`. This means that the trap for [[HasProperty]] is also called `has`.

#### Trap arguments
The arguments passed to a trap will also match the arguments passed to the corresponding `Reflect` function. The `Reflect.has` function takes in two arguments, `target` and `key`. This means that the trap called `has` will also receive the arguments `target` and `key`.

#### Trap return type
The expected return type of a trap will also match that of the corresponding `Reflect` function. The `Reflect.has` function returns a Boolean, which means the trap called `has` should also return a Boolean.

#### Proxy targets

Initializing a Proxy also requires another Object called the *target*. This will serve as a fallback. If an internal method isn’t implemented in your Proxy handler, it will call the internal method on the target instead. The target is also passed to every trap as the first argument.

![Untitled](../images/Untitled.png)

In the above diagram a trap is defined for ... but not for ...

### Creating Proxies

You create a new Proxy Object with the globally available `Proxy` constructor. The first argument is the target and the second is the Proxy handler.

```jsx
const proxyObj = new Proxy(target, proxyHandler)
```

The result is an exotic Proxy Object which will call your traps when its own internal methods are called. A few useful internal methods to trap are covered below.

#### Trapping [[Get]]

This will call your trap for every attempt to access property values on your Object. The value that you return from your trap will act as the property value. The below Proxy Object will return a custom String for every property value requested.

```jsx
const proxyObj = new Proxy({}, {
    get: (target, key, receiver) => {
      return `Value for key ${key}`
    }
  }
)

console.log(proxyObj.key123) // Value for key key123
console.log(proxyObj.helloWorld) // Value for key helloWorld
```

Note, it still is possible to circumvent this trap by getting the whole descriptor of a property. The descriptor will contain the value or a getter function. Of course, you could trap [[GetOwnProperty]] to prevent this.

#### Trapping [[Set]]
This will call your trap every time an attempt is made to assign a value to a property.  

```jsx
const proxyObj = new Proxy({}, {
    set: (target, key, value, receiver) => {
      console.log("You want to set", value, "on", key)
      return true
    }
  }
)

proxyObj.abc = 1 // You want to set 1 on abc
proxyObj.dfg = 2 // You want to set 2 on dfg
```

It’s important to return `true` from this trap. Otherwise, the assignment will be considered failed and a `TypeError` will be thrown if in Strict-mode.
Note, your [[Set]] trap won’t trigger if a property is defined with a descriptor. Although, it is possible to trap [[DefineOwnProperty]] ****which will catch that.

`receiver` in [[Get]] and [[Set]] traps
You may have noticed how the [[Get]] and [[Set]] traps take in an argument called `receiver`. It could be important in certain cases. You can read about it in Appendix X. I recommend that you finish chapter x and chapter x first.

#### Trapping [[OwnPropertyKeys]]
This allows you to return a custom set of property keys for the Object.

```jsx
const proxyObj = new Proxy({}, {
    ownKeys: (target) => ["a", "b", "c"]
  }
)

console.log(Reflect.ownKeys(proxyObj)) // ["a", "b", "c"]
console.log(Object.keys(proxyObj)) // []
```

As you can see, `Reflect.ownKeys` returns the keys we return from our Proxy trap but `Object.keys` does not. The reason for this, is that `Object.keys` only returns keys of enumerable properties. The keys we return from our Proxy trap don’t actually exist, meaning they don’t have a descriptor with an `enumerable` attribute.

#### Trapping [[GetOwnPropertyDescriptor]]
This will allow you to return custom descriptors for any call to [[GetOwnPropertyDescriptor]]. Using this, we can fix the above example by always returning a descriptor specifying that the property is enumerable.

```jsx
const proxyObj = new Proxy({}, {
    ownKeys: (target) => ["a", "b", "c"],
    getOwnPropertyDescriptor: (target, key) => {
      return { enumerable: true, configurable: true }
    }
  }
)

console.log(Object.keys(proxyObj)) // ["a", "b", "c"]
```

This will also allow our “fake” property keys to be used with the Spread operator (`{...obj}`) and the for..in loop.

> ⚠️ For the descriptor returned, it is required to have the `configurable` attribute set to `true` unless the target explicitly has `configurable` set to `false` for the same property.
> 

Trap ideas
The traps showed so far have been pretty simple. Here are some more interesting ideas for traps. There are code examples for these in appendix X.

- Validate data
- Restricting access to certain properties
- Limit property values to be a certain type
- Listen and change something like DOM
- Just logging. Using Reflect.
