# Advanced JS Objects

Objects are the lifeblood of JavaScript

This book does not focus too much on the syntax of objects. More on functionality

- Ideas:
    - [ ]  Add a recap for every section
    - [ ]  Map out when you don’t know something
    - [ ]  Wait with graphs
    - [ ]  Should I describe something as String or a string
    - [ ]  Make it sound fun?
    - [ ]  Include code snippets with strange results
    - [ ]  On the website, include a video where you are standing with the book in your hand and explaining what it is.
    - [ ]  You don’t know TS
        - [ ]  Better social media presence
    - [ ]  When this is starting to be something. Put it in a GitHub repo
    - [ ]  Emojis?
    - [ ]  Yt videos

# Section 1 - Getting our data structures straight

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

> Note: In JavaScript, the type is assigned automatically from the value.
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

# Section 2 - Exploring objects further

An object is a collection of properties. A property is the mapping between a key and a value.

```jsx
const obj = {
	"abc": 100
}
```

In the above example we have an object called `obj`. It has a property with the key `“abc”` and the value `100`. The specific syntax we used to create the above object makes it an *object literal.* Object literals are defined using curly braces (`{}`) that enclose  properties. The properties are defined using the colon (`:`) which separate the key and value. Object literals are not the only way to create objects. More on that later.

### Property key types

A property key can only be a String or a Symbol. It is possible to define a property key with something that is not a String or Symbol, but the value will be automatically converted to a String. Below, we create an object containing a Number key with the value `100`. When we retrieve the keys for the object, the type has changed to String. Giving us `“100”`.

```jsx
const obj = { 100: true }
console.log(Object.keys(obj)) // [ "100" ]
```

This functionality becomes very clear with arrays. Arrays are just objects  

Our terminology for arrays change a bit. The property keys are referred to as indexes and the property values are referred to as array elements.

Below, we create an array with three elements. We then retrieve all the indexes/keys from the array/object. As we can see, the indexes that we might expect to be Numbers are actually Strings.

```jsx
const arr = ["a", "b", "c"]
console.log(Object.keys(arr)) // [ "0", "1", "2" ]
```

This might be a bit confusing, after all, you don’t normally access array elements with String indexes. Usually, array elements are accessed with Number indexes. Both ways work. The same possible conversion that happens when you define a property, can also happen when you retrieve a property value. That is, the conversion that converts a key to a String if it is not already a String/Symbol.

```jsx
const arr = ["a", "b", "c"]
console.log(arr["1"]) // b
console.log(arr[1]) // b
```

In the above example, we access the second element of the array in two different ways. The first way uses an key which is already a String, and the second way uses a Number, which gets converted to a String before the value is retrieved. The result is the same.

### Symbol property keys

As mentioned before, Symbols are also valid Object keys. This also means they don’t get converted to Strings when used as property keys. 

```jsx
const key1 = "Key 1";
const key2 = Symbol("Key 2");

const obj = {
 [key1]: "Value 1",
 [key2]: "Value 2"
}

console.log(obj[key1]) // Value 1
console.log(obj[key2]) // Value 2
```

One advantage of using Symbols as keys is that they are guaranteed to be unique. This is useful if you want to prevent properties from being overwritten by code consuming your Objects. Also, Symbols aren’t returned by the conventional methods used to retrieve property keys like `Object.keys` and `Object.getOwnPropertyNames`.

```jsx
const obj = { [Symbol("Key 1")]: true, "Key 2": true }

console.log(Object.keys(obj)) // [ "Key 2" ]
console.log(Object.getOwnPropertyNames(obj)) // [ "Key 2" ]
```

If you really want to retrieve Symbol property keys, you can use `Object.getOwnPropertySymbols`.

```jsx
const obj = { [Symbol("Key 1")]: true, "Key 2": true }

console.log(Object.getOwnPropertySymbols(obj)) // [ Symbol(Key 1) ]
```

### Well-known Symbols

Symbols can even be used to expose the internal logic of JavaScript. There is a set of predefined Symbols called *well-known Symbols.* These predefined Symbols live as properties on Objects, and represent functionality that was previously only accessible by internal algorithms in JavaScript. These well-known Symbols can be found as properties on the `Symbol` constructor. Here they are listed:

- `Symbol.asyncIterator`
- `Symbol.hasInstance`
- `Symbol.isConcatSpreadable`
- `Symbol.iterator`
- `Symbol.match`
- `Symbol.matchAll`
- `Symbol.replace`
- `Symbol.search`
- `Symbol.species`
- `Symbol.split`
- `Symbol.toPrimitive`
- `Symbol.toStringTag`
- `Symbol.unscopables`

An interesting well-known Symbol is `Symbol.toStringTag`. It is used to give a default description of an object. It is used by `Object.prototype.toString` which is the method called when you execute `.toString()` on your object literal.
By setting a custom value on the `Symbol.toStringTag` property, we can trick `Object.prototype.toString` into labeling our object what we want.

```jsx
const obj = {}

console.log(obj.toString()) // [object Object]

obj[Symbol.toStringTag] = "Hi Mom!"

console.log(obj.toString()) // [object Hi Mom!]
```

### Property values types

If property keys are restricted to be only Strings and Symbols, how are property values restricted? The answer is that they aren’t. Values of properties can be of any type, even other Objects.

```jsx
const obj = {
  key1: "Value 1",
  key2: 200,
  key3: 300n,
  key4: true,
  key5: null,
  key6: undefined,
  key7: Symbol(),
  key8: { isObject: true }
}
```

### Primitives vs Objects variables

Primitives and Objects behave differently when assigned to variables or passed to functions. When you assign a primitive to a variable, the value is stored directly in the memory address associated with that variable. When you assign an Object to a variable, a *reference* to the Object is stored instead.

```jsx
const value1 = 10
const value2 = { isObj: true }
// Have a graph after this code. Display the memory. 3 memory slots
```

When copying primitive values into other variables, a simple copy is made of the value. When copying Objects, you instead copy the reference. This means multiple variables can reference the same Object value. 

```jsx
let obj1 = { changed: false }
let obj2 = obj1

obj2.changed = true

console.log(obj1) // { changed: true }
console.log(obj2) // { changed: true }
```

Above, we create an Object with a reference assigned to the variable `obj1`. We then copy the reference into the variable `obj2`. When then change a property on the object that `obj2` references. This is of course the same Object that `obj1` references. They effectively share an Object.

Note, there is a difference between changing an Object and assigning a brand new Object to a variable. Below, we re-assign the variable `obj2` with a new object literal. The result is that the variables reference two separate Objects.

```jsx
let obj1 = { changed: false }
let obj2 = obj1

obj2 = { changed: true } // <-- New Object!

console.log(obj1) // { changed: false }
console.log(obj2) // { changed: true }
```

**Function parameters** 

An assignment similar to that of variables happens to function parameters. Consider this example:

```jsx
function func(param) { /* ... */ }

const myValue = 10

func(myValue)
```

How many assignments are made here? There are actually two. The first one is `myValue = 10` and the second one is `param = myValue`. The value of `myValue` is not passed in directly. Instead, a copy is made which is assigned to `param`.
This means we can safely assume that functions won’t modify our primitive variables.

```jsx
function iCantChangeYourPrimitive(val) {
	val = 200
}

const myVal = 100
iCantChangeYourPrimitive(myVal)
console.log(myVal) // 100
```

The same safety cannot be assumed for Objects passed to functions. We have to remember that we are assigning a **reference** to the parameter. This means that the function doesn’t get a copy of the Object, it gets a copy of the reference.

```jsx
function iCanChangeYourObject(obj) {
  obj.changed = true
}

const myObj = {}
iCanChangeYourObject(myObj)
console.log(myObj) // { changed: true }
```

**Object properties**

What happens to objects when you assign them to other Objects as properties? The answer is that the Object **reference** becomes the property value. This is the same functionality we have with variable assignment.

```jsx
const obj1 = { changeMe: "" }
const obj2 = { obj1Here: obj1 }

obj1.changeMe = "I changed!"

console.log(obj2.obj1Here) // { changeMe: "I changed!" }
```

This can, in some rare cases, lead to a circular Object structure where two Objects both reference *each* *other*. This becomes a problem if you want to convert the Objects to JSON which doesn’t support references.

```jsx
const obj1 = {}
const obj2 = {}

obj1.otherObj = obj2
obj2.otherObj = obj1

// TypeError: Converting circular structure to JSON
console.log(JSON.stringify(obj1))
```

# Section 3 - Property descriptors and Object restrictions

## **Property descriptors**

So far, properties have been described as being quite simple. A property is just a key and a value. There is one more thing that determine the functionality of properties, their *descriptor*. The descriptor for a property is a set of *attributes* that define how a property may be used. Think of it as the configuration for the property.

You can retrieve the descriptor for a property with `Object.getOwnPropertyDescriptor`. The first argument is the Object and the second argument is the property key. Here is what the descriptor for `JSON.stringify` looks like: 

```jsx
console.log(Object.getOwnPropertyDescriptor(JSON, "stringify"))
/* {
    value: [Function: stringify],
    writable: true,
    enumerable: false,
    configurable: true
  } */
```

As you can see, the descriptor is presented as an Object with a few properties. Each of these properties represent some functionality associated with `JSON.stringify`. An interesting one is `value`. It literally represents the value of `JSON.stringify`. As we can see, the value of `JSON.stringify` is a function. All the different descriptor attributes will be covered in a bit.

You can define a property with a custom descriptor using `Object.defineProperty`. It takes in three arguments. The first argument is the Object, the second is the property key, and the third is the actual descriptor.

```jsx
const obj = {}

Object.defineProperty(obj, "myKey", {
  value: "Hello!",
  writable: true,
  enumerable: true
})

console.log(obj) // { myKey: "Hello!" }
```

> Descriptors are managed by the internals of JavaScript. They are not actually real Objects, but are instead kept in a more primitive internal structure. However, when we interact with descriptors they are converted to Objects. This gives them a familiar form. For sake of simplicity, assume that descriptors are Objects. 
This also explains why descriptor members are called *attributes* and not properties. They are represented to us as Object properties but aren’t actually kept as such.
> 

### Descriptor attributes

Here are the six different descriptor attributes with their default value:

| Attribute name | Default value |
| --- | --- |
| value | undefined |
| writable | false |
| enumerable | false |
| configurable | false |
| get | undefined |
| set | undefined |

**value**
This is the value of the property. 

**writable**
`writable` determines if the property should be able to be re-assigned. Setting it to `false` effectively freezes the initial value and makes the property read-only.

An attempt to re-assign the read-only property can lead to different outcomes. One possibility is that nothing happens and the property remains unchanged. The re-assignment just silently fails. This will happen if your code is not running in Strict mode. 

> Strict mode is ... You can read more about it in appendix B
> 

Below is a non-strict example:

```jsx
const obj = {}

Object.defineProperty(obj, "prop", {
  value: "Old value",
  writable: false
})

obj.prop = "New value?"

console.log(obj.prop) // Old value
```

If you attempt to re-assign a read-only property in Strict mode an error will be thrown. Here is the same re-assignment as above but in Strict mode:

```jsx
"use strict"

obj.prop = "New value?" // TypeError: Cannot assign to read only property
```

> Warning:  While it is not possible to re-assign a read-only property with the conventional assignment operator (`x = val`), it might still be possible to change the property’s entire descriptor if the `configurable` attribute is set to `true`. This could result in a change of value, since a different descriptor could contain a different `value` attribute.
> 

**enumerable**
Setting the `enumerable` attribute to `false` can “hide” your properties in certain contexts. Throughout the following examples we will be using the same Object called `obj`. It has three properties, `a`, `b`, and `c`.
`a` and `c` are *enumerable*, that is, their `enumerable` attribute is set to `true`. `c` is not enumerable. 

```jsx
const obj = {}

Object.defineProperty(obj, "a", { value: "A", enumerable: true })
Object.defineProperty(obj, "b", { value: "B", enumerable: false })
Object.defineProperty(obj, "c", { value: "C", enumerable: true })
```

Spread operator
The Spread operator is a way to combine Objects by *spreading* an Object into another. However, the Spread operator will only copy over properties that are enumerable*.*

```jsx
const newObj = { ...obj }
console.log(Object.getOwnPropertyNames(newObj)) // [ "a", "c" ]
```

Retrieving an array of property key
Normally, when you want to get an array of property keys for an Object you use `Object.keys`. This, like the spread operator, will only give you enumerable property keys. If you want to include non-enumerable property keys you can use `Object.getOwnPropertyNames`.

```jsx
console.log(Object.keys(obj)) // [ "a", "c" ]
console.log(Object.getOwnPropertyNames(obj)) // [ "a", "b", "c"" ]
```

> Warning: None of these methods return properties with Symbol keys. If you want **all** property keys for an object you can use `Reflect.ownKeys`
> 

console.log
In NodeJS, logging an Object with `console.log` with only show enumerable properties.

```jsx
console.log(obj) // { a: "A", c: "C" }
```

for...in loop
The for...in loop gives you a convenient way to iterate over property values. It ignores non-enumerable and Symbol properties.

```jsx
for (const value in obj) {
  console.log(value, "is enumerable!")
}
// A is enumerable!
// C is enumerable!
```

> Warning: The for...in loop will also include enumerable properties from the *prototype chain* of the Object. The prototypes chain will be covered in a later section, but it’s basically a way to set up inheritance between Objects. These inherited properties will be included in the for...in loop if they are enumerable.
> 

Checking if a property is enumerable
If you want a simple way to check if a property is enumerable you can use `propertyIsEnumerable`. You call this method on the Object itself.

```jsx
console.log(obj.propertyIsEnumerable("a")) // true
console.log(obj.propertyIsEnumerable("b")) // false
```

**configurable**
Setting the `configurable` attribute to `false` prevents your property from being deleted or having its descriptor changed.

delete operator
The delete operator will is used to remove properties from Objects. You can only use it to remove configurable properties. If you’re in Strict mode and attempt to delete a non-configurable property, an error will be thrown. If you’re not in Strict mode the deletion just silently fails. 

```jsx

const obj = {}

Object.defineProperty(obj, "a", {
  value: "A",
  enumerable: true,
  configurable: false
})

delete obj.a

console.log(obj) // { a: "A" }

"use strict"

delete obj.a // TypeError: Cannot delete property "a"
```

Changing a properties descriptor
`Object.defineProperty` can be used to define a new property but also change an existing property’s descriptor, if that property is configurable.

```jsx
const obj = {}

Object.defineProperty(obj, "a", {
  value: "A",
  enumerable: false,
  configurable: true,
  writable: false
})

Object.defineProperty(obj, "a", {
  value: "B",
  enumerable: true,
  writable: true
})

console.log(obj) // { a: "B" }
```

**get**
The `get` attribute can be an alternative way to provide values for properties. Instead of supplying a static value, you supply a function which will be called to retrieve the value. 

```jsx
const obj = {}

Object.defineProperty(obj, "time", {
  get: () => new Date().getTime()
})

console.log(obj.time) // 1645720383336
console.log(obj.time) // 1645720383341
```

Note: The function will always be called with empty arguments

**set**
The `set` attribute allows you to have a function be called every time an assignment is attempted for a property. The function will be called with the proposed value as an argument. The function is not required to set anything.

```jsx
const obj = {}

Object.defineProperty(obj, "setMe", {
  set: (value) => console.log("You want to set", value, "!")
})

obj.setMe = 100 // You want to set 100!
```

## Data properties vs accessor properties

There are two ways to categorize properties. Properties are either *data properties* or *accessor properties*. You categorize these different types of properties by their descriptor. Certain descriptor attributes are reserved for data properties and some are reserved for accessor properties. Additionally, some attributes can exist in both data and accessor properties. This table shows the 

| Attribute name | Which type of property does it represent |
| --- | --- |
| value | Data property  |
| writable | Data property  |
| get | Accessor property |
| set | Accessor property |
| enumerable | Data property or accessor property |
| configurable | Data property or accessor property |

One implication of this, is that you can’t define a descriptor with e.g. a `value` and `get` attribute. It’s helpful to keep this in mind when defining descriptors. An error will be thrown if you try to mix attributes for data properties with those for accessor properties.

```jsx
const obj = {}
Object.defineProperty(obj, "a", {
  value: "A",
  get: function() {}
}) // TypeError: Invalid property descriptor
```

**Getters and setters**

Getters and setters give a concise syntax for creating accessor properties. You create them by prefixing a method with `get` or `set` when creating Object literals. This will create a property with a descriptor containing your methods on the `get` and `set` attributes.

```jsx
const obj = {
  get x() {
    console.log("Getting x!")
  },
  set x(val) {
    console.log("Setting x!")
  }
}

// Same as

const obj = {}
Object.defineProperty(obj, "x", {
  enumerable: true,
  configurable: true,
  get: function () {
    console.log("Getting x!")
  },
  set: function () {
    console.log("Setting x!")
  }
})
```

## **Object restrictions**

Just like you can restrict the functionality of properties it is also possible to restrict entire Objects. There are three methods that can apply a varying amount of restriction to your Objects. They are:

- `Object.preventExtensions` - Makes your Objects non-*extensible*
- `Object.seal` - Makes your Objects *sealed*
- `Object.freeze` - Makes your Objects *frozen*

> The following code examples will be in non-Strict mode but will contain code that would throw errors in Strict mode. A line ending with a `// Failure` comment indicates that an error would be thrown in Strict mode.
> 

**Object.preventExtensions**

By default, Objects are extensible. This means it is possible to add new properties to them.  `Object.preventExtensions` prevents this by making your Objects non-extensible*.* This will only prevent *adding* new properties, and not deleting/changing existing properties.

```jsx
const obj = { a: "A", b: "B" }
Object.preventExtensions(obj)

obj.c = "C" // Failure

console.log(obj) // { a: "A", b: "B" }
```

<aside>
💡 You can check if an Object is extensible with `Object.isExtensible`

</aside>

**Object.seal**

Sealing your Object will make it non-extensible and make every property non-configurable. The result of this is that you can’t create or delete any properties. It is still possible to change the value of properties if they are not read-only.

```jsx
const obj = { a: "A", b: "B" }
Object.seal(obj)

delete obj.a // Failure
obj.c = "C" // Failure

console.log(obj) // { a: "A", b: "B" }
```

<aside>
💡 You can check if an Object is sealed with `Object.isSealed`

</aside>

**Object.freeze**

Freezing an Object makes it non-extensible and makes every property non-configurable *and* read-only. This effectively prevents the Object from being changed in any way.

```jsx
const obj = { a: "A", b: "B" }
Object.freeze(obj)

delete obj.a // Failure
obj.b = "B2" // Failure
obj.c = "C" // Failure

console.log(obj) // { a: "A", b: "B" }
```

> 💡 You can check if an Object is frozen with `Object.isFrozen`
>

> Warning: It’s not possible to undo any of the above restrictions. Once applied, your Object will remain restricted for its entire lifetime. It is, however, still possible to copy its properties into a new non-restricted Object.
> 

**Table of Object restrictions**

Below you will find a table with the three methods covered above. It allows you to reference which actions can be performed on properties belonging to restricted Objects. 

|  | Create  | Delete | Change | Read |
| --- | --- | --- | --- | --- |
| Object.preventExtensions |      ❌  |      ✓ |      ✓ |      ✓ |
| Object.seal |      ❌  |      ❌  |      ✓ |      ✓ |
| Object.freeze |      ❌ |      ❌  |      ❌  |      ✓ |

As you can see, the three methods form a sort of hierarchy with `Object.preventExtensions` being the least restrictive and `Object.freeze` being the most restrictive.

# Section 4 - Internal Object behavior

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

<aside>
💡 The property values of an Array Object are also refereed to as Array *elements.*

</aside>

<aside>
💡 It might sound strange to learn that valid Array indexes has to be a Strings. This is because we normally use Numbers to access/define Array elements. It might be helpful to think of two different kinds of Array indexes:
 - Internal Array indexes which has to be Strings.
 - External Array indexes which can be both Strings and Numbers.
The reason external Array indexes can be both Strings and Numbers, is that Numbers are converted to Strings if used as property keys.

</aside>

**Special case 1: The key is called “length”**

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

**Special case 2: The key is a valid Array index**

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

**Normal property assignment**

If a property is assigned to the Array Object that doesn’t fit the two special cases, it will be assigned normally. This won’t affect the length of the Array.

```jsx
const arr = ["a", "b", "c"]
arr["Hello"] = "World"

console.log(arr) // [ "a", "b", "c", Hello: "World" ]
console.log(arr.length) // 3
```

### Proxies (exotic Objects)

Proxies are the only exotic Objects that have a different implementation of every single essential internal method. When one of its internal methods are called, the Proxy can call a function of *your* choosing. You can essentially set up listeners that run custom code when internal methods are called. This is really powerful and allows you to create truly flexible Objects.

**Proxy handlers**

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

Trap name
The name of the trap is the same as the internal method’s corresponding `Reflect` function. An example could be the internal method [[HasProperty]], which has a corresponding `Reflect` function called `has`. This means that the trap for [[HasProperty]] is also called `has`.

Trap arguments
The arguments passed to a trap will also match the arguments passed to the corresponding `Reflect` function. The `Reflect.has` function takes in two arguments, `target` and `key`. This means that the trap called `has` will also receive the arguments `target` and `key`.

Trap return type
The expected return type of a trap will also match that of the corresponding `Reflect` function. The `Reflect.has` function returns a Boolean, which means the trap called `has` should also return a Boolean.

**Proxy targets**

Initializing a Proxy also requires another Object called the *target*. This will serve as a fallback. If an internal method isn’t implemented in your Proxy handler, it will call the internal method on the target instead. The target is also passed to every trap as the first argument.

![Untitled](Advanced%20J%207b5fc/Untitled.png)

In the above diagram a trap is defined for ... but not for ...

**Creating Proxies**

You create a new Proxy Object with the globally available `Proxy` constructor. The first argument is the target and the second is the Proxy handler.

```jsx
const proxyObj = new Proxy(target, proxyHandler)
```

The result is an exotic Proxy Object which will call your traps when its own internal methods are called. A few useful internal methods to trap are covered below.

Trapping [[Get]]
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

Trapping [[Set]]
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

Trapping [[OwnPropertyKeys]]
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

Trapping [[GetOwnPropertyDescriptor]]
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

> Warning: For the descriptor returned, it is required to have the `configurable` attribute set to `true` unless the target explicitly has `configurable` set to `false` for the same property.
> 

Trap ideas
The traps showed so far have been pretty simple. Here are some more interesting ideas for traps. There are code examples for these in appendix X.

- Validate data
- Restricting access to certain properties
- Limit property values to be a certain type
- Listen and change something like DOM
- Just logging. Using Reflect.

# Section 5 - Prototypes

Have you ever thought about how you are able to call `toString` on seemingly every Object? 

```jsx
const obj = {}
console.log(Reflect.ownKeys(obj)) // []

console.log(obj.toString()) // [object Object]
```

How is this possible if the Object doesn’t have any properties? The answer is *prototypes.* Prototypes is how you implement inheritance in JavaScript. They allow you have an Object inherit properties from another Object. Our empty Object inherited the `toString` method from a standard Object called `Object.prototype`.

```jsx
const obj = {}
console.log(obj.toString === Object.prototype.toString) // true
```

## The Prototype chain

All Objects, expect for a few exotic ones, have an internal property called [[Prototype]]. The value of this property is either `null` or a reference to another Object. The value of this property is refereed to as the *prototype* of the Object. You can’t directly access it but you can retrieve it using `Object.getPrototypeOf` or `Reflect.getPrototypeOf`.

```jsx
const obj = {}
const objProto = Reflect.getPrototypeOf(obj)
console.log(typeof objProto) // object
```

In the above example we define an empty Object literal. We then use `Reflect.getPrototypeOf` which gives us the prototype of that Object. As we can see, the prototype of our Object is also an Object. That might seem a bit strange; we never gave it a prototype. Object literals have a default prototype. More on that later. 

We now know that Objects can have an internal reference to another Object known as their prototype. This becomes more interesting when you realize that since prototypes are also Objects, prototypes can have their *own* prototype. This leads to something called the *prototype chain.* It’s the linking between potentially multiple Objects through prototypes.

![Untitled](Advanced%20J%207b5fc/Untitled%201.png)

Above, we can see a prototype chain starting with Object A. Object A has the prototype Object B. Object B has the prototype Object C. A prototype chain ends if the prototype of an Object is `null`. This is the case for Object C.

<aside>
💡 It’s important to take note of the direction of the arrows. The prototype of Object A is Object B but not vice versa. Object B knows nothing about Object A. Object A is not part of the prototype chain of Object B.

</aside>

### Inheritance

The prototype chain isn’t just used to link Objects together, it’s used for inheritance. Specifically, if an Object lacks a property, it can inherit it from its prototype chain. To understand how this works, it’s important to understand what happens when you try to access a property. It goes as follows:

1. If the property exists on the Object, **return** it.
2. Else, retrieve the Object’s prototype. If that’s `null` **return** `undefined`.
3. Else, go back to step 1. This time let the “Object” be the prototype.

This means your JavaScript engine will “clime up” the prototype chain in search of the property. If it doesn’t exist on the Object it will check the prototype, then that prototype’s prototype etc. until it finds it or reaches `null`. 

Consider these three Objects. They each have a unique property. How many of these properties are accessible to Object A?

![Untitled](Advanced%20J%207b5fc/Untitled%202.png)

The answer is all of them. Property X exists directly on Object A and Property Y and Z exist in its prototype chain.

What if the same property key exists multiple places in a prototype chain? Which property value will be returned when Property X is accessed on Object A below?

![Untitled](Advanced%20J%207b5fc/Untitled%203.png)

The answer is Property X of Object B. It comes first in the prototype chain. When the JavaScript engine goes through the prototype chain to find a property, it stops when it finds the first occurrence.

<aside>
💡 Properties that exist directly on an Object are refereed to as its *own* properties. This term is used in functions like `Reflect.getOwnPropertyDescriptor` and `Reflect.ownKeys`. These functions won’t clime up the prototype chain.

</aside>

## Standard prototypes

Some Objects are used as the default prototype for the Objects you create. It’s useful to understand these so you know which properties your Objects inherit. Two important standard prototypes will be covered below.

**Object.prototype**
If you create an Object literal, the prototype will be an Object called `Object.prototype`. It isn’t just specific to Object literals however. It is no doubt the most important Object in the context of prototypes. It is present in the prototype chain of practically every Object in JavaScript. It’s found at the end of the chain right before `null`.

For Object literals, it’s the first and only prototype.

```jsx
const obj = {}
const proto = Reflect.getPrototypeOf(obj)
console.log(proto === Object.prototype) // true
console.log(Reflect.ownKeys(proto))
/* [
  'constructor',
  '__defineGetter__',
  '__defineSetter__',
  'hasOwnProperty',
  '__lookupGetter__',
  '__lookupSetter__',
  'isPrototypeOf',
  'propertyIsEnumerable',
  'toString',
  'valueOf',
  '__proto__',
  'toLocaleString'
] */
```

**Function.prototype**
The prototype of the functions you create will be `Function.prototype`. It contains useful methods like `apply`, `bind`, and `call` which will be covered later.

```jsx
function func() {}
const proto = Reflect.getPrototypeOf(func)
console.log(proto === Function.prototype) // true
console.log(Reflect.ownKeys(proto))
/* [
  'length',
  'name',
  'arguments',
  'caller',
  'constructor',
  'apply',
  'bind',
  'call',
  'toString',
  Symbol(Symbol.hasInstance)
] */
```

The prototype of `Function.prototype` is `Object.prototype`. Notice how both Objects contain a method `.toString`. This means that functions don’t access the `.toString` from `Object.prototype` but instead access a more function specific `.toString` method from `Function.prototype`.

![Untitled](Advanced%20J%207b5fc/Untitled%204.png)

## The **__proto__ property**

If you open your browser console and access a property called `__proto__` on an Object, you will receive its prototype. 

```jsx
const obj = {}
const objProto = Reflect.getPrototypeOf(obj)
console.log(objProto === obj.__proto__) // true
```

Because of this, many people assume that the prototype of an Object is stored *on* the `__proto__` property. That’s wrong. The prototype of an Object is stored internally and `__proto__` is just an accessor property that exposes it.

It is not recommended that you use `__proto__` to access or set prototypes. It’s only guaranteed to be supported in browsers, and even then is only kept for compatibility purposes. Deno is an example of a modern JavaScript runtime that doesn’t support `__proto__`.

## Setting prototypes on Objects

There are two ways to set a prototype on an Object. The first is with `Object.create`. This will create a new empty Object with a prototype you supply as an argument.

```jsx
const proto = { canYouAccessMe: true }
const obj = Object.create(proto)
console.log(obj.canYouAccessMe) // true
```

The second way is with `Object.setPrototypeOf` or `Reflect.setPrototypeOf`. These functions replace the prototype on an already created Object.

```jsx
const proto = { canYouAccessMe: true }
const obj = {}
Reflect.setPrototypeOf(obj, proto)
console.log(obj.canYouAccessMe) // true
```

<aside>
⚠️ Changing the prototype of an exisitng Object can be a performance issue ...

</aside>

## super

# Section 6 - this

The `this` keyword can provide useful context for your code. 

```jsx
const obj = {
  canYouSeeMe: true,
  func: function () {
    console.log(this.canYouSeeMe) // true
  }
}
obj.func()
```

Above, we define an Object with two properties. The first property is called `canYouSeeMe` and  the second is a method called `func`. Interestingly, `func` is able to access `canYouSeeMe` using `this`.

`this` can be used anywhere in your code and depending where it’s used, its value will vary quite a lot. This chapter will focus on the `this` inside functions in six different cases. A distinction will be made between *traditional functions* (`function () {}`) and *arrow functions* (`() => {}`). Below you will see the different cases that will be covered.

| How function is being called | Traditional function this |   Arrow function this |
| --- | --- | --- |
| On its own |                    ? |                      ? |
| On an Object |                    ? |                      ? |
| Using bind, call, or apply |                    ? |                      ? |

<aside>
💡 Some of these cases have different outcomes depending if they’re in Strict-mode or not. Make sure you understand Strict-mode before you proceed.

</aside>

# globalThis

Before diving in, it’s important to understand the `globalThis` keyword. In certain cases it will be used as a fallback for `this`. It, unlike `this`, has the same value no matter the context. Below you can see the value of `globalThis` in different runtimes.

| Context | globalThis |
| --- | --- |
| NodeJS | global |
| Deno | window |
| All major browsers | window |

`globalThis` is not to be confused with `this` in the global scope (outside any function). In browsers they’re the same but not in NodeJS or Deno.

| Context | this in global scope |
| --- | --- |
| NodeJS | Empty Object |
| Deno | undefined |
| All major browsers | window |

# Traditional function on its own

If you run a traditional function on its own, the value of its `this` will depend if the function is in Strict-mode or not. If in Strict-mode, `this` will be `undefined`. If not in Strict-mode, `this` will be `globalThis`. 

```jsx
"use strict"
function func() {
  return this
}
console.log(func() === undefined) // true
```

```jsx
function func() {
  return this
}
console.log(func() === globalThis) // true
```

# Traditional function on an Object

Calling a traditional function on an Object will result in its `this` being the Object its called on. 

```jsx
const obj = {
  func: function () {
    return this
  }
}

console.log(obj.func() === obj) // true
```

This provides a useful way to retain and share state between function calls.

```jsx
const obj = {
  counter: 0,
  increment: function () {
    this.counter += 1
  }
}

console.log(obj.counter) // 0
obj.increment()
obj.increment()
console.log(obj.counter) // 2
```

Note, `this` doesn’t follow a function if you assign it to a variable and call it on its own.

```jsx
const obj = {
  func: function () {
    return this
  }
}

const func = obj.func
console.log(func()) // globalThis || undefined
```

# Traditional function using bind, call, or apply

There are three methods that let you manipulate the `this` inside functions. They are called `bind`, `call`, and `apply`. They exist on `Function.prototype`, meaning they exist on all functions.

### bind

`bind` creates a new function with a `this` that you specify.

```jsx
function f1() {
  return this
}

const f2 = f1.bind({ iAmThis: true })
console.log(f2()) // { iAmThis: true }
```

### call

`call` is similar to bind, but it calls the function directly with the new `this` instead of creating a new function.  The first argument is the `this`, and the rest are the arguments that should be passed to the function.

```jsx
function increaseAge(amount) {
  this.age += amount
}

const person = { age: 30 }

increaseAge.call(person, 5)

console.log(person) // { age: 35 }
```

### apply

`apply` is the same as `call` except you pass arguments as an array. 

```jsx
function setCoords(x, y) {
  this.x = x
  this.y = y
}

const person = {}

setCoords.apply(person, [5, 10])

console.log(person) // { x: 5, y: 10 }
```

# Arrow function on its own or on an Object

<aside>
💡 An arrow function called on its own or an on Object will have the same `this`.

</aside>

Arrow functions don’t have their own `this` binding. Instead, the `this` inside arrow functions is based on the scope they are defined within. If the arrow function is defined inside a traditional function, it will have the same `this` as the traditional function. If it’s not defined inside a traditional function, its `this` will be the `this` of the global scope.

It’s important to remember that the `this` is based on where the arrow function is *defined* not where it’s called. This turns out to be a useful feature when it comes to callbacks. Callbacks are functions that you supply to other functions as arguments. Doing this with traditional functions means you often loose control of `this`. That’s because you usually don’t control *how* the callback is called. Luckily, arrow functions don’t care how they’re called, only where they’re defined.

An interesting application of arrow functions can be seen with `setTimeout`. `setTimeout` is a function that you call with two arguments. The first argument is a callback and the second is a number representing milliseconds. `setTimeout` will call your callback after the time has passed that you specified in milliseconds. 

Below we have two Objects. Both of them have a method called `slowIncrement` which provides `setTimeout` with a callback to be called in 500 milliseconds. Both callbacks access `this` to increment a counter. The first callback is a traditional function and the second is an arrow function.

```jsx
const obj1 = {
  count: 0,
  slowIncrement: function () {
    setTimeout(function () {
      this.count += 1
      console.log(this.count)
    }, 500)
  }
}
obj1.slowIncrement() // NaN, this.count is undefined

const obj2 = {
  count: 0,
  slowIncrement: function () {
    setTimeout(() => {
      this.count += 1
      console.log(this.count)
    }, 500)
  }
}

obj2.slowIncrement() // 1
```

As we can see, the traditional function callback isn’t able to access the `this` we want. It instead accesses a `this` we don’t control from inside the `setTimeout` function. The arrow function callback is able to access the same `this` as `slowIncrement` which is `obj2`. Meaning, the arrow function was able to “take on” the `this` of the traditional function it was *defined* within.

<aside>
💡 You can, of course, always create a traditional function with a controllable `this` by  using `bind`.

</aside>

# Arrow function using bind, call, or apply

Because arrow functions don’t have their own `this` binding, `bind`, `call`, or `apply` will have no effect on them.

```jsx
const func = () => this

const customThis = {}

console.log(func.bind(customThis)() === customThis) // false
console.log(func.call(customThis) === customThis) // false
console.log(func.apply([customThis]) === customThis) // false
```

# Section 7 - Constructors

Constructors provide a convenient way to create instances of Objects. Below we use the `Date` constructor to create a new date Object.

```jsx
const obj = new Date()
console.log(typeof obj) // object
```

# What is a constructor?

All functions in JavaScript have an internal method called [[Call]]. This method is used to execute the code inside the function. Some JavaScript functions have another internal method called [[Construct]]. It too, executes the code in the function, although slightly differently. Functions that have a [[Construct]] method are called constructors.

### new

But how do you call a function using [[Construct]] instead of [[Call]]? You use the `new` operator. We can see this with the `Date` constructor. Below, we call it in two ways. The first is with the `new` operator and the second is without. Both ways work.

```jsx
const obj1 = new Date() // Internally calling [[Construct]]
const obj2 = Date() // Internally calling [[Call]]
```

### Reflect.construct

Another way to call a constructor using [[Construct]] is with `Reflect.construct`. The first argument is the constructor and the second is an array of arguments.

```jsx
const obj = Reflect.construct(Date, [])
console.log(typeof obj) // object
```

<aside>
💡 There is a third optional argument for `Reflect.construct` called `newTarget`. It is only relevant for classes and will be covered later.

</aside>

# Which functions have a [[Construct]] method?

Some functions have an internal [[Construct] method and some don’t. You will now see some examples of constructors and non-constructors.

<aside>
⚠️ If you attempt to call [[Construct]] on a non-constructor function it will throw a `TypeError`.

</aside>

### Classes

Classes are a common way to create instances of Objects. They are constructors. Much more on them later.

```jsx
class Func {}
new Func() // OK
```

### Traditional functions

Generally speaking, traditional functions are valid constructors. This might be surprising if you’re mainly using classes.

```jsx
function Func() {}
new Func() // OK
```

An exception to this are async functions. They are not constructors.

```jsx
async function Func() {}
new Func() // TypeError: Not a constructor
```

### Arrow functions

Arrow functions are not constructors.

```jsx
const Func = () => {}
new Func() // TypeError: Not a constructor
```

# [[Construct]]

What exactly does [[Construct]] do when called on a function? Here is a brief overview:

1. Create an empty Object
2. Set its prototype to the value of the constructor’s `prototype` property
3. Execute the code with `this` being a reference to the Object
4. Return the Object

Step 2 and 3 will be covered further below.

### The prototype property

All constructors will have a property called `prototype`. Below, we define a constructor called `Car` and verify that it has a `prototype` property. As we can see, the type is an Object.

```jsx
function Car() {
console.log(typeof Car.prototype) // object
```

Given the name of the property, it’s easy to think that it references the prototype of the *constructor*. That’s wrong.

```jsx
function Car() {}
const CarProto = Reflect.getPrototypeOf(Car)
console.log(CarProto === Car.prototype) // false
```

The `prototype` property actually holds the prototype that the constructor wishes to give *instances* created with it.

```jsx
function Car() {}
const instance = new Car()
const instanceProto = Reflect.getPrototypeOf(instance)
console.log(instanceProto === Car.prototype) // true
```

### The this inside a constructor

Consider this function:

```jsx
function Car(speed) {
  this.speed = speed
}
```

It assigns a property to its `this` and then ends. Calling the function using [[Construct]] will execute the code inside and return its `this` to us. 

```jsx
function Car(speed) {
  this.speed = speed
}

console.log(new Car(5)) // { speed: 5 }
```

As we can see, the return value we get from invoking [[Construct]] (using `new`) is an Object with a `speed` property. Meaning, the “instance” we receive from a constructor is actually an instance of its `this`.

### Putting this and prototype together (creating methods)

Turns out, the `prototype` property of a constructor is the perfect place to define methods for its instances. Consider the `Car` constructor from before. Let’s give it a method called `drive`.

```jsx
function Car(speed) {
  this.speed = speed
}

Car.prototype = {
  drive() {
    console.log(`Wr${"o".repeat(this.speed)}m!`)
  }
}

const car1 = new Car(2)
car1.drive() // Wroom!
const car2 = new Car(5)
car2.drive() // Wrooooom!
```

Above, two instances being created, `car1` and `car2`. As we can see, they are both able to access the `drive` method defined on `Car.prototype`.

![Untitled](Advanced%20J%207b5fc/Untitled%205.png)

This is a common pattern when giving methods to JavaScript instances. The two instances don’t have a `drive` method each. Instead, they *share* the `drive` method by both having `Car.prototype` as their prototype.

<aside>
💡 You might be wondering why the result of calling `drive` on the instances is different. After all, they access the exact same method. The difference comes from the `this` inside the drive method.
`drive` is a traditional function so its `this` is the Object its called *on*. That explains the difference between calling `car1.drive()` and `car2.drive()`.

</aside>

# instanceof

The `instanceof` operator tells you if an Object contains a constructor’s `prototype` property somewhere in its prototype chain.

To illustrate this, let’s create a date and visualize its prototype chain.

```jsx
const date = new Date()
```

![Untitled](Advanced%20J%207b5fc/Untitled%206.png)

As we can see, both `Date.prototype` and `Object.prototype` exist in its prototype chain. This means that `instanceof` will return `true` for both `Date` and `Object`.

```jsx
const date = new Date()

console.log(date instanceof Date) // true
console.log(date instanceof Object) // true
```

<aside>
💡 In reality, `instanceof` will first check for a method on the constructor using the well-known Symbol key `Symbol.hasInstance`. If such a method exists, `instanceof` will call it and return its result. If it doesn’t exist, `instanceof` will work as described above.
This essentially means that the result of `instanceof` can be customized for individual constructors. However, constructors rarely take advantage of this.

</aside>

# Section 8 - Classes

Classes are ...

Here is an example of a class ...

- [x]  Re-read the ES6 chapter
- [ ]  Re-read the deep js chapter
- [ ]  Re-read the overflow thread
- [x]  Change the order of constructor logic. Maybe recreate it with JS function

# Classes are constructors

The first thing you need to know about classes is that they are constructors. To clarify, the class *itself* is a constructor. Meaning a class is a *function* with both a [[Call]] and [[Construct]] internal method. However, if you call a class using [[Call]] it will throw a `TypeError`. This means there is practically only one way to call classes, with [[Construct]].

```jsx
new MyClass() // Internally calling [[Construct]], OK
MyClass() // Internally calling [[Call]], throws TypeError
```

# The special constructor method

A class method called `constructor` will be considered special to classes. To understand it, it helps to remember what a class is. It’s a function. A function, of course, has code inside it. Turns out, the code inside a class **comes from its `constructor` method. It sounds strange but bear with me.
Let’s create a class and a traditional function that do the same when called with `new`.

```jsx
class MyClass {
  constructor() {
    console.log("Hello!")
  }
}

function MyFunc() {
  console.log("Hello!")
}

new MyClass() // Hello!
new MyFunc() // Hello!
```

For the class, isn’t it a bit strange that we executed the `console.log` code by calling `new MyClass()` and didn’t have to call `new MyClass.constructor()`? After all, the `console.log` code exists inside a method inside the class. But this works because the code of the `constructor` method becomes the code of the class function itself.

![Untitled](Advanced%20J%207b5fc/Untitled%207.png)

<aside>
⚠️ A class definition doesn’t actually produce a traditional function definition like shown above. Both definitions create their own internal function Object. However, it’s safe to assume, for now, that they produce an identical function Object. Meaning, you can use the above figure as a helpful mental model.

</aside>

<aside>
💡 It’s not required to define a `constructor` method. If you don’t, it’s the equivalent of defining an empty one (`constructor(){}`). This doesn’t apply to *derived* *classes* which will be covered later.

</aside>

# Own properties

Certain properties will be own properties of Objects created with classes. Meaning, the properties will exist *on* the instance. Not in its prototype chain or anywhere else.

Classes have two ways to give their instances own properties:

- Instance properties
- Field declarations

### Instance properties

Instance properties are defined inside the special `constructor` class method we covered above. You define them by assigning properties to `this`.  

```jsx
class Person {
  constructor(name) {
    this.name = name
  }
}

const paul = new Person("Paul")
console.log(paul) // { name: "Paul" }
```

Above, we have a class called `Person` that has a `constructor` method which assigns a value to `this.name`.̣ When we invoke [[Construct]] on the class (using `new`) we get an Object with our instance property, `name`.
We can mimic this with a traditional function:

```jsx
function Person(name) {
  this.name = name
}

const paul = new Person("Paul")
console.log(paul) // { name: "Paul" }
```

### Field declarations

Field declarations are another way to define own properties on class instances. They are defined in the body of a class (outside any method).

```jsx
class Cat {
  livesLeft = 9
}

const garfield = new Cat()
console.log(garfield) // { livesLeft: 9 }
```

# Methods

Defining a method on a class will actually define it on the classes `prototype` property. Remember, a class is a constructor, and the `prototype` property is special to constructors. The prototype of instances created with a constructor, will come from the *constructors* `prototype` property. To illustrate this, let’s create two instances from a class with a `sayName` method. 

```jsx
class Person {
  constructor(name) {
    this.name = name
  }
  sayName() {
    console.log("My name is", this.name)
  }
}

const paul = new Person("Paul")
const owen = new Person("Owen")

paul.sayName() // My name is Paul
owen.sayName() // My name is Owen
```

At first glance, it seems like `paul` and `owen` each have their own `sayName` method. But in reality, they both share the exact same method.

```jsx
console.log(paul.sayName === owen.sayName) // true
```

This is because they both inherited the method from their prototype. Since `paul` and `owen` where created as instances of a constructor, their prototype comes from the constructor’s `prototype` property. In this case the constructor is `Person`.

```jsx
const paulProto = Reflect.getPrototypeOf(paul)
const owenProto = Reflect.getPrototypeOf(owen)
console.log(paulProto === Person.prototype) // true
console.log(owenProto === Person.prototype) // true
```

Below is an illustration of how `paul` and `owen` access the `sayName` method.

![Untitled](Advanced%20J%207b5fc/Untitled%208.png)

# Static members

Static members, that is, static field declarations and methods, exists as own properties of the class *itself*. They do not appear in the prototype chain of instances, so they can only be accessed directly on the class.

```jsx
class Person {
  static totalPeople = 0
  static incrementPeople() {
    Person.totalPeople += 1
  }
}

Person.incrementPeople()
console.log(Person.totalPeople) // 1
```

Above we have a class with two static members, `totalPeople` and `incrementPeople`. We access these members directly on `Person`.

# Derived classes

Classes can inherit functionality from other classes using the `extends` keyword. A class that “extends” another class is called a *derived class*. Setting up this inheritance will add another link in the prototype chain of class instances. Specifically, the Object stored on the `prototype` property of a derived class will have a prototype pointing to the `prototype` property of the parent class.

To explore this, let’s create a derived class called `Cat` that derives from `Animal`. We can then create an instance called `cat` and examine its prototype chain.

```jsx
class Animal {}
class Cat extends Animal {}

const cat = new Cat()
```

![Untitled](Advanced%20J%207b5fc/Untitled%209.png)

As we can see, the prototype of `Cat.prototype` is `Animal.prototype`. This means that properties defined on `Animal.prototype` are available to our instance `cat` through prototype chain inheritance. We can see this by defining a `sayHi` method on `Animal`.

```jsx
class Animal {
  sayHi() {
    console.log("Hi! I'm an animal")
  }
}

class Cat extends Animal {}

const cat = new Cat()

cat.sayHi() // Hi! I'm an animal
```

Above, we can see that `sayHi` is available to our `cat` instance. It’s inherited from `Animal.prototype`.

```jsx
console.log(cat.sayHi === Animal.prototype.sayHi) // true
```

### Super keyword

The `super` keyword allows a derived class to interact with its parent.

**this of a super call**

Calling `super` inside the `constructor` method of a derived class calls [[Construct]] on the parent class. When this happens, the `this` inside both functions is the same Object. This can be seen in the below example.

```jsx
let this1
let this2

class Parent {
  constructor() {
    this1 = this
  }
}

class Derived extends Parent {
  constructor() {
    super()
    this2 = this
  }
}

new Derived()
console.log(this1 === this2) // true
```

This means that any assignment to `this` inside the parent class `constructor` method also happens to `this` inside the derived class’ `constructor` method. We can see this below where we define a message on `this.hello` inside the parent class `Parent`. The message is then available on the `this` inside `Derived` after we call `super`.

```jsx
class Parent {
  constructor() {
    this.hello = "Hello from Parent"
  }
}

class Derived extends Parent {
  constructor() {
    super()
    console.log(this.hello) // Hello from Parent
  }
}

new Derived()
```

<aside>
⚠️ Any use of `this` before calling `super` inside a derived class will result in a `ReferenceError`.

</aside>

**Arguments of a super call**

The arguments to a `super` call becomes the arguments used when [[Construct]] is called on the parent class.

```jsx
class Calculator {
  constructor(num1, num2) {
    this.result = num1 + num2
  }
}

class Derived extends Calculator {
  constructor() {
    super(2, 6)
    console.log(this.result) // 8
  }
}

new Derived()
```

<aside>
💡 If you don’t define a `constructor` method inside a derived class it’s the equivalent of defining one that calls `super` with all its own arguments (`constructor(...args) { super(...args) }`)

</aside>

**Accessing a property on super**

Accessing a property on `super` will go up the prototype chain starting with the classes *parent*. This makes it useful if a method with the same name is defined in both a derived- and parent class. We can see this below where we access `method` on both `this` and `super` with different results.

```jsx
class Parent {
  method() {
    console.log("You accessed Parent")
  }
}

class Derived extends Parent {
  constructor() {
    super()
    this.method()  // You accessed Derived
    super.method() // You accessed Parent
  }
  method() {
    console.log("You accessed Derived")
  }
}

new Derived()
```

# Private members

Private members are exclusive to classes. They allow you to define properties and methods which are inaccessible outside their class. A private class member is prefixed with a hashtag (`#`).

<aside>
💡 Private members won’t become part of the classes prototype chain inheritance. Meaning, they can’t be inherited by derived classes.

</aside>

### Private field

```jsx
class Person {
  #age = 0
  constructor(age) {
    this.#age = age
  }
}

const person = new Person(30);
person.#age // syntaxError
```

As we can see, `#age` is not accessible outside the class.

<aside>
💡 Private properties have to be declared as fields. Meaning, they have to be declared in the body of the class (outside any method). This is unlike public properties which can be declared directly on `this` without first being declared as a field.

</aside>

### Private method

Like private fields, it’s also possible to define private methods.

```jsx
class Person {
  #age = 0
  constructor(age) {
    this.#age = age
  }
  #canVote() {
    return this.#age >= 18
  }
}

const person = new Person(30)
person.#canVote() // syntaxError
```

### Private static members

It’s also possible to define private static members. These fields and methods, while static, are only accessible inside the scope of the class they are defined within.

```jsx
class Person {
  constructor() {
    Person.#incrementPeople()
  }
  static #totalPeople = 0
  static #incrementPeople() {
    Person.#totalPeople += 1
  }
}

Person.#incrementPeople() // SyntaxError
Person.#totalPeople // SyntaxError
```

# Appendix

### receiver argument for traps

(some more context here?)

The `receiver` argument becomes important if you want a trap that returns the same value it would without the trap. E.g a [[Get]] trap only used for logging purposes. You would expect to be able to just return `target[key]` but that doesn’t always work. It might not work if your Proxy becomes part of a prototype chain. 

Consider the following example. Here we have a person Object that combines `this.name` with `this.lastName` in the getter method `fullName`. The Object `paul` inherits this method. This works like a charm.

```jsx
const person = {
  get fullName() {
    return `${this.name} ${this.lastName}`
  }
}

const paul = { name: "Paul", lastName: "Bertsen" }
Reflect.setPrototypeOf(paul, proxyPerson)

console.log(paul.fullName) // Paul Bertsen
```

But what if we want to log something every time a property is read on `person`? We could wrap it in a Proxy, do our logging in the [[Get]] trap, and return `target[key]`, right? No, this wouldn’t work. 

```jsx
const person = {
  get fullName() {
    return `${this.name} ${this.lastName}`
  }
}

const proxyPerson = new Proxy(person, {
  get: (target, key, receiver) => {
		// Logging here ...
    return target[key]
  }
})

const paul = { name: "Paul", lastName: "Bertsen" }
Reflect.setPrototypeOf(paul, proxyPerson)

console.log(paul.fullName) // undefined undefined
```

Here, it’s important to understand the difference between `target` and `receiver`.

- `receiver` in this case is `paul` because we initially read `fullName` on `paul`.
- `target` in this case is `person` because we specified it as the target when we created the `proxyPerson` Proxy.

Getting `target[key]` becomes the same as getting `person[key]`. We are missing `paul`. Maybe we should get `receiver[key]` instead since `receiver` is `paul`? No, this would create an infinite loop because the prototype of `paul` is our Proxy. It would look something like this:

- Get `paul.fullName`. It doesn’t exist.
- The prototype of `paul` is `proxyPerson`. Try getting `proxyPerson.fullName` instead.
- The [[Get]] trap on `proxyPerson` wants to get `paul.fullName`.
- Get `paul.fullName`. It doesn’t exist.
- ...

What we need, is a way to get `target[key]` but supply `receiver` as the custom `this` for any possible getter function. Meaning, we call the getter for `person.fullName` with `this` being `paul`. That would work. Perhaps we could extract the property’s getter function from its descriptor and call it using `bind`, `call`, or `apply`. There is an easier way. We can use `Reflect.get`. Its last argument (conveniently also named `receiver`) will become `this` in any getter/setter function. It therefor achieves exactly what we need. 

```jsx
const person = {
  get fullName() {
    return `${this.name} ${this.lastName}`
  }
}

const proxyPerson = new Proxy(person, {
  get: (target, key, receiver) => {
    // Logging here ...
    return Reflect.get(target, key, receiver)
  }
})

const paul = { name: "Paul", lastName: "Bertsen" }
Reflect.setPrototypeOf(paul, proxyPerson)

console.log(paul.fullName) // Paul Bertsen
```

<aside>
💡 The above example explained the `receiver` argument in the context of a [[Get]] trap. However, it is also relevant for a [[Set]] trap. That too, can get passed in a  `receiver` argument, and its corresponding `Reflect.set` function also takes in a `receiver` argument.

</aside>

---

---

---

## Links

**[ValidateAndApplyPropertyDescriptor](https://tc39.es/ecma262/#sec-validateandapplypropertydescriptor) ( O, P, extensible, Desc, current )**

**[EnumerateObjectProperties](https://tc39.es/ecma262/#sec-enumerate-object-properties) ( O )**

[https://github.com/tc39/proposal-object-rest-spread](https://github.com/tc39/proposal-object-rest-spread)

[http://perfectionkills.com/understanding-delete/](http://perfectionkills.com/understanding-delete/)

[https://tc39.es/ecma262/#sec-property-descriptor-specification-type](https://tc39.es/ecma262/#sec-property-descriptor-specification-type)

Get and set [https://tc39.es/ecma262/multipage/ecmascript-language-functions-and-classes.html#sec-runtime-semantics-methoddefinitionevaluation](https://tc39.es/ecma262/multipage/ecmascript-language-functions-and-classes.html#sec-runtime-semantics-methoddefinitionevaluation)

ES5 book [https://leanpub.com/understandinges6/read#leanpub-auto-the-road-to-ecmascript-6](https://leanpub.com/understandinges6/read#leanpub-auto-the-road-to-ecmascript-6)

Are arrays numerically indexed? [https://stackoverflow.com/a/453773](https://stackoverflow.com/a/453773) 

Getting a property [https://tc39.es/ecma262/#sec-property-accessors-runtime-semantics-evaluation](https://tc39.es/ecma262/#sec-property-accessors-runtime-semantics-evaluation) Follow to find [https://tc39.es/ecma262/#sec-topropertykey](https://tc39.es/ecma262/#sec-topropertykey)

Primitive wrapping [https://2ality.com/2022/03/properties-of-primitives.html](https://2ality.com/2022/03/properties-of-primitives.html)

[https://javascript.info/proxy#reflect](https://javascript.info/proxy#reflect)

Globally available variables. Where are they? [https://tc39.es/ecma262/#sec-value-properties-of-the-global-object](https://tc39.es/ecma262/#sec-value-properties-of-the-global-object)

Prototype algorithm [https://tc39.es/ecma262/#sec-ordinaryget](https://tc39.es/ecma262/#sec-ordinaryget)

ES6 book [https://leanpub.com/understandinges6/read#leanpub-auto-the-road-to-ecmascript-6](https://leanpub.com/understandinges6/read#leanpub-auto-the-road-to-ecmascript-6)

Function create from source text [https://tc39.es/ecma262/#sec-ordinaryfunctioncreate](https://tc39.es/ecma262/#sec-ordinaryfunctioncreate)

this value passed to function [https://tc39.es/ecma262/#sec-evaluatecall](https://tc39.es/ecma262/#sec-evaluatecall)

new [https://tc39.es/ecma262/#sec-evaluatenew](https://tc39.es/ecma262/#sec-evaluatenew)

class calling Construct with a newTarget [https://tc39.es/ecma262/#_ref_6983](https://tc39.es/ecma262/#_ref_6983)

class stuff [https://tc39.es/ecma262/#sec-class-definitions](https://tc39.es/ecma262/#sec-class-definitions)

Making a function a constructor [https://tc39.es/ecma262/#sec-makeconstructor](https://tc39.es/ecma262/#sec-makeconstructor)

Enviroment records [https://tc39.es/ecma262/#sec-environment-records](https://tc39.es/ecma262/#sec-environment-records)

Deep js book with class stuff [https://exploringjs.com/impatient-js/toc.html](https://exploringjs.com/impatient-js/toc.html)

Referencing super [https://tc39.es/ecma262/multipage/ecmascript-language-expressions.html#sec-makesuperpropertyreference](https://tc39.es/ecma262/multipage/ecmascript-language-expressions.html#sec-makesuperpropertyreference)

- New/constructor examples
    
    Remember instanceof
    
    ```jsx
    const Func1 = function () {}
    
    const f = new Func1()
    const fProto = Object.getPrototypeOf(f)
    
    console.log(fProto == Func1.prototype) // true
    ```
    
    ```jsx
    const Func1 = function () {}
    const Func2 = () => {}
    
    new Func1() // OK
    new Func2() // TypeError: Not a constructor
    ```
    
- Proxy examples
    
    ```jsx
    const arr = [1, 2]
    const numberOnlyArr = new Proxy(arr, {
      set: (arr, key, value) => {
        return typeof value == "number"
      }
    })
    
    numberOnlyArr.push(3) // OK
    
    // TypeError: 'set' on proxy:
    // trap returned falsish for property '2'
    numberOnlyArr.push("a")
    ```
    
    ```jsx
    const values = { value1: 100, value2: 200 }
    
    const obj = new Proxy(values, {
      get: (target, key) => {
        return { value: target[key], time: new Date().getTime() }
      }
    })
    
    console.log(obj.value1) // { value: 100, time: 1646495363701 }
    console.log(obj.value2) // { value: 200, time: 1646495363708 }
    ```
    
    ```jsx
    const add = new Proxy({}, {
        get: (target, key) => {
          const [num1, num2] = key.split("+");
          return parseInt(num1) + parseInt(num2)
        }
      }
    )
    
    console.log(add["2+2"]) // 4
    console.log(add["4+2"]) // 6
    console.log(add["10+10"]) // 20
    ```
    

## Prototype

## Function

Function object

![Untitled](Advanced%20J%207b5fc/Untitled%2010.png)

[https://tc39.es/ecma262/#table-additional-essential-internal-methods-of-function-objects](https://tc39.es/ecma262/#table-additional-essential-internal-methods-of-function-objects)

Private fields

![Untitled](Advanced%20J%207b5fc/Untitled%2011.png)

[https://tc39.es/ecma262/#table-internal-slots-of-ecmascript-function-objects](https://tc39.es/ecma262/#table-internal-slots-of-ecmascript-function-objects)

![Untitled](Advanced%20J%207b5fc/Untitled%2012.png)

## Misc

Things:

- Generators/iterators?
- for..in loop ****for prototypes?
- Constructors
- How something becomes global
- Classes
- Why is it called Object.prototype

# Appendix ideas

Proxy Object that logs every internal thing. Used to explore internal behavior. JSON.stringify could be explored.

Re-create [[Construct]]

```jsx
function logPrototypeChain(obj) {
  const proto = Reflect.getPrototypeOf(obj)
  if (proto === null) return
  console.log(Reflect.ownKeys(proto))
  logPrototypeChain(proto)
}
```

[https://tc39.es/ecma262/#sec-ordinaryfunctioncreate](https://tc39.es/ecma262/#sec-ordinaryfunctioncreate)

```jsx
// Run in global scope
if (this === undefined) {
  console.log("This is running in Deno")
} else if (Reflect.ownKeys(this).length === 0) {
  console.log("This is running in NodeJS")
} else if (this === window) {
  console.log("This is running in a browser")
}
```

```jsx
const jane = new Person('Jane');

const cheeta = new jane.constructor('Cheeta');
```

**Strict mode**

```jsx
calculator.fivePlusSix
delete creates a property
set updates DOM
remove elements in array by setting length
russian rulete function. Every time you call it there is a chance that it will delete the DB. Ruletify()
Did you know you can mix classes and functions (extends)
Class computed names
```