# Chapter 1 - Getting our data structures straight

To understand objects it’s essential to understand where objects fit into the JavaScript data structure scene. This chapter firsts cover the different JavaScript types then cover which types contain so called *primitives*. I will also attempt to clear some confusion regarding the `typeof` operator.

## What is a type?

Every value you define will have a type. Here are all JavaScript types[^spec-types]:

- String
- Number
- BigInt
- Boolean
- Symbol
- Undefined
- Null
- Object

Usually, you don't have to think too much about types in JavaScript. That's because types are inferred automatically from values. But being able to categorize your values into types is still quite useful. As you'll see, different types have very different capabilities.

The main thing to know about types is that they group values. Some types contain more values than others, and some contain only one value. But it's important to always think of them as sets of values. All the types listed above will be further explained below.

> 💡 This book will describe the name of a types using capitalization e.g. "Object" or "Number" [^capitalization]. However, when the type isn't explicitly addressed, groups of values will be described in lower-case e.g. "object(s)" or "number(s)".
>

### String

Text values are of the String type.

```js
const hello = "Hello world!" // Type is String
```

In the above example the value is “Hello world!” and the type of the value is String.

You can imagine that the range of possible values for the String type is quite large, which is true. The number of different String combinations is practically infinite.

### Number

There exists two types for numeric values in JavaScript. The most common is Number. It consists of 18,437,736,874,454,810,624 numeric values, half of which are positive, and the other half are negative[^number-amount].

```js
// Type is Number
const num1 = -250
const num2 = 0
const num3 = 524.75
```

The Number type also includes three special values. They are Not-a-Number, positive Infinity, and negative Infinity. They are represented as `NaN`, `+Infinity`, and `-Infinity`.

```js
// Type is Number
const num1 = NaN
const num2 = +Infinity
const num3 = -Infinity
```

### BigInt

BigInt is an alternative type for a numeric value. It differs from Number by allowing numeric values of any size. This becomes useful if you’re performing mathematical operations on very large numbers.

You define a value to be of type BigInt by appending an `n` to your numbers.

```js
const num = 100n
```

You can’t represent decimals with BigInts.

```js
const num = 100.5n // SyntaxError: unexpected token
```

You also can’t mix BigInt values with values of other types.

```js
const num = 100n + 100 // TypeError: Cannot mix BigInt and other types
```

### Boolean

The Boolean type contains only the values `true` and `false`.

```js
// Type is Boolean
const bool1 = true
const bool2 = false
```

### Symbol

Symbols allow you to create your own custom unique values. You create a new symbol with the built-in `Symbol` function[^symbol-function].
^
```js
const mySymbol = Symbol() // Type is Symbol
```

You can optionally give your symbols a description. This is mostly to act as documentation.

```js
const mySymbol = Symbol("This is my Symbol")
```

Interestingly, symbols can be used as keys on object properties. More on that later.

### Undefined and Null

The Undefined and Null types are a bit special. They each contain just one value. Undefined contains only the value `undefined`, and Null contains only the value `null`.

```js
const a = undefined // Type is Undefined
const b = null // Type is Null
```

### Object

Objects are collections of properties. These properties map keys to values. 

```js
const obj = {
	key: "Value"
}
```

## What is a primitive?

Certain types contain values which are *primitive*. Primitive values belong to the following types:

- String
- Number
- BigInt
- Boolean
- Symbol
- Undefined
- Null

The only type that doesn’t contain primitive values is Object. This means that all objects are non-primitive values.

Primitives are represented directly at the lowest level of the language implementation. All primitives are *immutable*, meaning they can’t be changed after creation. Have you ever noticed how it’s impossible to change a string after creating it?

```js
const myString = "Hello World"
myString.repeat(5)
console.log(myString) // Hello World
```

The value of `myString` isn’t repeated 5 times like you might expect. The `repeat` method actually returns a new string because it can’t modify the primitive `“Hello World”` value.

Another quirk of primitives is that they can’t have properties/methods. This might be surprising to learn, after all, the above example uses a *method* called `repeat`. It’s true that `repeat` is a method, but it doesn’t exist on the primitive itself. The method exists on a *wrapper object*[^wrapping].
All primitives except Undefined and Null have wrapper objects. When you attempt to access a property on a primitive, a wrapper object is instantiated which you access instead. The wrapper object for String types is called `String`. `String` is a globally available constructor, which means we can easily simulate the work JavaScript does under the hood. 

```js
const a1 = "a".repeat(5)
const a2 = new String("a").repeat(5)
console.log(a1) // aaaaa
console.log(a2) // aaaaa
```

In the above example, we let JavaScript create the wrapper object for `a1` but we do it ourselves for `a2`. The result is the same.

Interestingly, the specific instance of the wrapper object only exists when you try to access a property and is discarded right after. That’s why this code doesn’t work:

```js
const myString = "Hello World"
myString.someField = "Can you see me?"
console.log(myString.someField) // undefined
```

We set `someField` on a `String` wrapper object that then is immediately discarded meaning we receive another instance of `String` on the last line.

## typeof

I feel it’s necessary to explain some confusing parts of the `typeof` operator.

Using the operator on a value produces a string that gives an indication of the type. Here are the possible values it can return:

- `"string"`
- `"number"`
- `"bigint"`
- `"boolean"`
- `"symbol"`
- `"undefined"`
- `"object"`
- `"function"`

First, notice how all the types are represented in lower-case. Second, notice the lack of the Null type. This is what happens if you use the `typeof` operator on `null`:

```js
console.log(typeof null) // object
```

The type of `null` is wrongly computed to be Object. This is a bug that was introduced in the first implementation of JavaScript[^typeof]. It has never been possible to correct the bug since a lot of programs depend on the false result. If you need a more accurate way to check for objects you have to do the following.

```js
if (typeof value === "object" && value !== null) {
	console.log("Is object")
}
```

The last notable thing about the `typeof` operator is how it differentiates between objects and functions. Functions in JavaScript don’t have their own type and instead belong to the Object type. This is because functions are just callable Objects. So it’s reasonable to expect the `typeof` operator to return `“object”` for a function but it instead returns `“function”`.

```js
const func = () => {}
console.log(typeof func) // function
```


[^capitalization]: This is the same capitalization that the specification uses https://tc39.es/ecma262/#sec-ecmascript-language-types.
[^spec-types]: Taken, in same order, from here https://tc39.es/ecma262/#sec-ecmascript-language-types.
[^number-amount]: https://tc39.es/ecma262/#sec-ecmascript-language-types-number-type
[^symbol-function]: Some people refer to this as a constructor which is not accurate. It's a constructor in a conceptual sense because it lets you create new symbols, but it doesn't have an internal [[Construct]] method meaning it will throw an error when used with `new`.
[^wrapping]: This is how it happens in the specification.
	- A property access https://tc39.es/ecma262/#sec-property-accessors-runtime-semantics-evaluation
	- Calls GetValue https://tc39.es/ecma262/#sec-getvalue
	- Which calls ToObject https://tc39.es/ecma262/#sec-toobject (Creates the wrapper object)
	I recommend reading this great post https://2ality.com/2022/03/properties-of-primitives.html
[^typeof]: A good read on this https://2ality.com/2013/10/typeof-null.html