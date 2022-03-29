# Chapter 2 - Exploring objects further

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