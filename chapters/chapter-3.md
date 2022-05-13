# Chapter 3 - Property descriptors and object restrictions

This chapter will cover ways that you can restrict or alter the functionality of objects. This can be done in two ways. The first is using *descriptors* which effect individual properties. The second is using one of the three methods that restrict entire objects. Both of these ways will be covered below.

# Property descriptors

So far, properties have been described as being quite simple. A property is just a key and a value. There is one more thing that determine the functionality of properties, their descriptor. The descriptor for a property is a set of *attributes* that define how a property may be used. Think of it as the configuration for the property.

You can retrieve the descriptor for a property with `Object.getOwnPropertyDescriptor`. The first argument is the object and the second is the property key. Here is what the descriptor for `JSON.stringify` looks like: 

```js
console.log(Object.getOwnPropertyDescriptor(JSON, "stringify"))
/* {
    value: [Function: stringify],
    writable: true,
    enumerable: false,
    configurable: true
  } */
```

As you can see, the descriptor is presented as an object with a few properties. Each of these properties represent some functionality associated with `JSON.stringify`. An interesting one is `value`. It literally represents the value of `JSON.stringify`. As we can see, the value of `JSON.stringify` is a function. All the different descriptor attributes will be covered in a bit.

You can define a property with a custom descriptor using `Object.defineProperty`. It takes in three arguments. The first argument is the object, the second is the property key, and the third is the actual descriptor.

```js
const obj = {}

Object.defineProperty(obj, "myKey", {
  value: "Hello!",
  writable: true,
  enumerable: true,
  configurable: true
})

console.log(obj) // { myKey: "Hello!" }
```

> 💡 Descriptors are managed by the internals of JavaScript. They are not actually real objects, but are instead kept in a more primitive internal structure[descriptor-type^]. However, when we interact with descriptors they are converted to objects. This gives them a familiar form. For sake of simplicity, assume that descriptors are objects. 
>
> This also explains why descriptor members are called *attributes* and not properties. They are represented to us as object properties but aren’t actually kept as such.
> 

## Descriptor attributes

Here are the six different descriptor attributes with their default value[^descriptor-table]:

| Attribute name | Default value ^
| --- | --- |
| `value` | `undefined` |
| `writable` | `false` |
| `enumerable` | `false` |
| `configurable` | `false` |
| `get` | `undefined` |
| `set` | `undefined` |

### value

The `value` attribute, if defined, represents the actual value of the property.

```js
const obj = { myKey: 10 }
const desc = Object.getOwnPropertyDescriptor(obj, "myKey")
console.log(desc.value) // 10
```

> 💡 The `value` attribute won't represent the value of your property if it's an *accessor property*. Accessor properties will instead use the `get` attribute. This will be covered later.
> 


### writable

`writable` determines if the property should be able to be re-assigned. Setting it to `false` effectively freezes the initial value and makes the property read-only.

An attempt to re-assign the read-only property can lead to different outcomes. One possibility is that nothing happens and the property remains unchanged. The re-assignment just silently fails. This will happen if your code is not running in strict mode. 

> 💡 Strict mode is something that you can enable which changes the execution of your JavaScript code. You can read more about it in [Appendix B](./appendix-b.md).
> 

Below is a non-strict example:

```js
const obj = {}

Object.defineProperty(obj, "prop", {
  value: "Old value",
  writable: false
})

obj.prop = "New value?"

console.log(obj.prop) // Old value
```

If you attempt to re-assign a read-only property in strict mode an error will be thrown. Here is the same re-assignment as above but in strict mode:

```js
"use strict"

// ...

obj.prop = "New value?" // TypeError: Cannot assign to read only property
```

> ⚠️ While it is not possible to re-assign a read-only property with the conventional assignment operator (`x = val`), it might still be possible to change the property’s entire descriptor if the `configurable` attribute is set to `true`. This could result in a change of value since a different descriptor could contain a different `value` attribute.
> 

### enumerable

Setting the `enumerable` attribute to `false` can “hide” your properties in certain contexts. 

Throughout the following examples we will be using the same object called `obj`. It has three properties, `a`, `b`, and `c`.

`a` and `c` are *enumerable*, that is, their `enumerable` attribute is set to `true`.

`b` is not enumerable. 

```js
const obj = {}

Object.defineProperty(obj, "a", { value: "A", enumerable: true })
Object.defineProperty(obj, "b", { value: "B", enumerable: false })
Object.defineProperty(obj, "c", { value: "C", enumerable: true })
```

<ins>Spread operator</ins>

The spread operator is a way to combine objects by *spreading* an object into another. However, the spread operator will only copy properties that are enumerable.

```js
const newObj = { ...obj }
console.log(Object.getOwnPropertyNames(newObj)) // [ "a", "c" ]
```

<ins>Retrieving an array of property keys</ins>

Normally, when you want to get an array of property keys for an object you use `Object.keys`. This, like the spread operator, will only give you enumerable property keys. If you want to include non-enumerable property keys you can use `Object.getOwnPropertyNames`.

```js
console.log(Object.keys(obj)) // [ "a", "c" ]
console.log(Object.getOwnPropertyNames(obj)) // [ "a", "b", "c"" ]
```

> ⚠️ None of these methods return properties with symbol keys. If you want *all* property keys for an object you can use `Reflect.ownKeys`
> 

<ins>console.log</ins>

In NodeJS and Deno, logging an object with `console.log` will only show enumerable properties.

```js
console.log(obj) // { a: "A", c: "C" }
```

<ins>for...in loop</ins>

The for...in loop gives you a convenient way to iterate over property values. It ignores non-enumerable and symbol properties.

```js
for (const value in obj) {
  console.log(value, "is enumerable!")
}
// A is enumerable!
// C is enumerable!
```

> ⚠️ The for...in loop will also include enumerable properties from the *prototype chain* of the object. The prototypes chain will be covered in a [Chapter 5](./chapter-5.md), but it’s basically a way to set up inheritance between objects. These inherited properties will also be included in the for...in loop if they are enumerable.
> 

<ins>Checking if a property is enumerable</ins>

If you want a simple way to check if a property is enumerable you can use `propertyIsEnumerable`. You call this method on the object itself.

```js
console.log(obj.propertyIsEnumerable("a")) // true
console.log(obj.propertyIsEnumerable("b")) // false
```

### configurable

Setting the `configurable` attribute to `false` prevents your property from being deleted or having its descriptor changed.

<ins>delete operator</ins>

The delete operator will is used to remove properties from objects. You can only use it to remove configurable properties. If you’re in strict mode and attempt to delete a non-configurable property, an error will be thrown. If you’re not in strict mode the deletion just silently fails. 

```js
const obj = {}

Object.defineProperty(obj, "a", {
  value: "A",
  enumerable: true,
  configurable: false
})

delete obj.a

console.log(obj) // { a: "A" }
```

```js
"use strict"

// ...

delete obj.a // TypeError: Cannot delete property "a"
```

<ins>Changing a properties descriptor</ins>

`Object.defineProperty` can be used to define a new property but also change an existing property’s descriptor if that property is configurable.

```js
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

### get

The `get` attribute can be an alternative way to provide values for properties. Instead of supplying a static value you supply a function which will be called to retrieve the value. 

```js
const obj = {}

Object.defineProperty(obj, "time", {
  get: () => new Date().getTime()
})

console.log(obj.time) // 1645720383336
console.log(obj.time) // 1645720383341
```

Note: The function will always be called with empty arguments.

### set

The `set` attribute allows you to have a function be called every time an assignment is attempted for a property. The function will be called with the proposed value as an argument. The function is not required to set actually anything.

```js
const obj = {}

Object.defineProperty(obj, "setMe", {
  set: (value) => console.log("You want to set", value, "!")
})

obj.setMe = 100 // You want to set 100!
```

## Data properties VS accessor properties

There are two ways to categorize properties. Properties are either *data properties* or *accessor properties*. You categorize these different types of properties by their descriptor. Certain descriptor attributes are reserved for data properties and some are reserved for accessor properties. Additionally, some attributes can exist in both data and accessor properties. This table shows the different attributes and which type they represent[^descriptor-table].

| Attribute name | Type of property it represents |
| --- | --- |
| `value` | Data property  |
| `writable` | Data property  |
| `get` | Accessor property |
| `set` | Accessor property |
| `enumerable` | Data property or accessor property |
| `configurable` | Data property or accessor property |

One implication of this is that you can’t define a descriptor with e.g. both a `value` and `get` attribute. It’s helpful to keep this in mind when defining descriptors. An error will be thrown if you try to mix attributes for data properties with those for accessor properties.

```js
const obj = {}
Object.defineProperty(obj, "a", {
  value: "A",
  get: function() {}
}) // TypeError: Invalid property descriptor
```

### Data properties

Data properties are the most common type of properties. Their value can only change through re-assignment.

```js
const obj = {}

Object.defineProperty(obj,"prop", {
	value: 10,
  writable: true
})

obj.prop // <-- Data property
```

#### Object literal data properties

Creating object literal properties using a colon (`:`), results in data properties.

```js
const obj = {
  prop: 10
}

obj.prop // <-- Data property
```

### Accessor properties

Accessor properties are more dynamic. Instead of using the `value` attribute, they use `get` and `set`. These attributes hold methods which are called when reading/assignment of the property is attempted.


```js
const obj = {}

Object.defineProperty(obj,"prop", {
	get: () => 10,
	set: (val) => {}
})

obj.prop // <-- Accessor property
```

#### Getters and setters in object literals

Getters and setters give a concise syntax for creating accessor properties. You create them by prefixing a method with `get` or `set` in object literals. This will create a property with a descriptor containing your methods on the `get` and `set` attributes. 

```js
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

# Object restrictions

Just like you can restrict the functionality of properties it is also possible to restrict entire objects. There are three methods that apply a varying amount of restriction to your objects. They are:

- `Object.preventExtensions` - Makes your objects non-*extensible*
- `Object.seal` - Makes your objects *sealed*
- `Object.freeze` - Makes your objects *frozen*

> ⚠️ It’s not possible to undo any of the these restrictions. Once applied, your object will remain restricted for its entire lifetime. It is, however, still possible to copy its properties into a new non-restricted object.
> 

> ⚠️ The following code examples will be in non-strict mode but will contain code that would throw errors in strict mode. A line ending with a `// Failure` comment indicates that an error would be thrown in strict mode.
> 

### Object.preventExtensions

By default, objects are extensible. This means it's possible to add new properties to them. `Object.preventExtensions` prevents this by making your objects non-extensible.

```js
const obj = { a: "A", b: "B" }
Object.preventExtensions(obj)

obj.c = "C" // Failure

console.log(obj) // { a: "A", b: "B" }
```

>⚠️ This will only prevent *adding* new properties, and not deleting/changing existing properties.
>

>💡 You can check if an object is extensible with `Object.isExtensible`
>

### Object.seal

Sealing your object will make it non-extensible and make every property non-configurable. The result of this is that you can’t create or delete any properties. It is still possible to change the value of properties if they are not read-only.

```js
const obj = { a: "A", b: "B" }
Object.seal(obj)

delete obj.a // Failure
obj.c = "C" // Failure

console.log(obj) // { a: "A", b: "B" }
```

>💡 You can check if an object is sealed with `Object.isSealed`
>

### Object.freeze

Freezing an object makes it non-extensible and makes every property non-configurable *and* read-only. This effectively prevents the object from being changed in any way.

```js
const obj = { a: "A", b: "B" }
Object.freeze(obj)

delete obj.a // Failure
obj.b = "B2" // Failure
obj.c = "C" // Failure

console.log(obj) // { a: "A", b: "B" }
```

> 💡 You can check if an object is frozen with `Object.isFrozen`
>


### Table of object restrictions

Below you will find a table with the three methods covered above. It allows you to reference which actions can be performed on properties belonging to restricted objects. 

|  | Create  | Delete | Change | Read |
| --- | --- | --- | --- | --- |
| `Object.preventExtensions` |      ❌  |      ✓ |      ✓ |      ✓ |
| `Object.seal` |      ❌  |      ❌  |      ✓ |      ✓ |
| `Object.freeze` |      ❌ |      ❌  |      ❌  |      ✓ |

As you can see, the three methods form a sort of hierarchy with `Object.preventExtensions` being the least restrictive and `Object.freeze` being the most restrictive.


[descriptor-type^]: It's a Record Specification Type https://tc39.es/ecma262/#sec-property-descriptor-specification-type
[^descriptor-table]: https://tc39.es/ecma262/#table-object-property-attributes