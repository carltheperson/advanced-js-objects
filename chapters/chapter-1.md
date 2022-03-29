# Chapter 1 - Getting our data structures straight

To understand objects it’s essential to understand where objects fit into the JavaScript data structure scene.

## What is a type?

Every value you define will have a type. Here they are listed:

- String
- Number
- BigInt
- Boolean
- Symbol
- Undefined
- Null
- Object

Think of types as holding a range of values.

> 💡 In JavaScript, the type is assigned automatically from the value.
> 

### String

Text values are of the String type. Here is an example:

```jsx
const hello = "Hello world!" // Type is String
```

In the above example the value is “Hello world!” and the type of the value is String.

You can imagine that the range of possible values for the String type is quite large, which is true. The number of different string combinations is practically infinite.

### Number

There exists two types for numeric values in JavaScript. One of them is Number. It consists of 18,437,736,874,454,810,624 numeric values, half of which are positive, and the other half are negative.

```jsx
// Type is Number
const num1 = -250
const num2 = 0
const num3 = 524.75
```

The Number type also includes three special values. They are Not-a-Number, positive Infinity, and negative Infinity. They are represented as `NaN`, `+Infinity`, and `-Infinity`.

```jsx
// Type is Number
const num1 = NaN
const num2 = +Infinity
const num3 = -Infinity
```

### BigInt

BigInt is an alternative type for a numeric value. It differs from Number by allowing numeric values of any size. This becomes useful if you’re performing mathematical operations on very large numbers.

You define a value to be of type BigInt by appending an `n` to your numbers.

```jsx
const num = 100n
```

You can’t represent decimals with BigInts.

```jsx
const num = 100.5n // SyntaxError: unexpected token
```

You also can’t mix BigInt values with values of other types.

```jsx
const num = 100n + 100 // TypeError: Cannot mix BigInt and other types
```

### Boolean

The Boolean type contains only the values `true` and `false`.

```jsx
// Type is Boolean
const bool1 = true
const bool2 = false
```

### Symbol

Symbols allow you to create your own custom unique values. You create a new Symbol value with the built-in `Symbol` constructor.

```jsx
const mySymbol = Symbol() // Type is Symbol
```

You can optionally give your Symbols a description. This is mostly to act as documentation.

```jsx
const mySymbol = Symbol("This is my Symbol")
```

Interestingly, symbols can be used as keys on objects. More on that later.

### Undefined and Null

The Undefined and Null types are a bit special. They each contain just one value. Undefined contains only the value `undefined`, and Null contains only the value `null`.

```jsx
const a = undefined // Type is Undefined
const b = null // Type is Null
```

### Object

## What is a primitive?

Primitive values belong to the following types:

- String
- Number
- BigInt
- Boolean
- Symbol
- Undefined
- Null

The only type that doesn’t contain primitive values is Object.

Primitives are represented directly at the lowest level of the language implementation.

All primitives are *immutable*, meaning they can’t be changed after creation. Have you ever noticed how it’s impossible to change a String after creating it?

```jsx
const myString = "Hello World"
myString.repeat(5)
console.log(myString) // Hello World
```

The value of `myString` isn’t repeated 5 times like you might expect. The `repeat` method actually returns a new string because it can’t modify the primitive “Hello World” value.

Another quirk of primitives is that they can’t have properties/methods. This might be surprising to learn, after all, the above example uses a *method* called `repeat`. It’s true that `repeat` is a method, but it doesn’t exist on the primitive itself. The method exists on a *wrapper object*.
All primitives except Undefined and Null have wrapper objects. When you attempt to access a property on a primitive, a wrapper object is instantiated which you access instead. The wrapper object for String types is called `String`. `String` is a globally available constructor, which means we can easily simulate the work JavaScript does under the hood. 

```jsx
const a1 = "a".repeat(5)
const a2 = new String("a").repeat(5)
console.log(a1) // aaaaa
console.log(a2) // aaaaa
```

In the above example, we let JavaScript create the wrapper object for `a1` but we do it ourselves for `a2`. The result is the same.

Interestingly, the specific instance of the wrapper object only exists when you try to access a property and is discarded right after. That’s why this code doesn’t work:

```jsx
const myString = "Hello World"
myString.someField = "Can you see me?"
console.log(myString.someField) // undefined
```

We set `someField` on a `String` wrapper object that then is immediately discarded meaning we receive another instance of `String` on the last line.

## typeof

I feel it’s necessary to explain some confusing parts of the `typeof` operator.

Using the operator on a value produces a String that gives an indication of the type. Here are the possible values it can return:

- `"string"`
- `"number"`
- `"bigint"`
- `"boolean"`
- `"symbol"`
- `"undefined"`
- `"object"`
- `"function"`

First, notice how the types are all lower case. Normally, you describe a JavaScript type in PascalCase, meaning all words start with an uppercase character e.g. BigInt.

Another thing to notice is the lack of the Null type. This is what happens if you use the `typeof` operator on `null`:

```jsx
console.log(typeof null) // object
```

The type of `null` is wrongly computed to be Object. This is a bug that was introduced in the  first implementation of JavaScript. It has never been possible to correct the bug since a lot of programs depend on the false result.

The last notable thing about the `typeof` operator is how it differentiates between objects and functions. Functions in JavaScript don’t have their own type and instead belong to the Object type. This is because functions are just callable Objects. So it’s reasonable to expect the `typeof` operator to return `“object”` for a function but instead it returns `“function”`.

```jsx
const func = () => {}
console.log(typeof func) // function
```


