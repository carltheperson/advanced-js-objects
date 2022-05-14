# Section 6 - this

The `this` keyword can provide useful context for your code. 

```js
const obj = {
  canYouSeeMe: true,
  func: function () {
    console.log(this.canYouSeeMe) // true
  }
}
obj.func()
```

Above, we define an object with two properties. The first property is called `canYouSeeMe` and the second is a method called `func`. Interestingly, `func` is able to access `canYouSeeMe` using `this`.

`this` can be used anywhere in your code, and depending where it’s used, its value will vary quite a lot. This chapter will focus on the `this` inside functions in six different cases. A distinction will be made between *traditional functions* (`function () {}`) and *arrow functions* (`() => {}`). Below you will see the different cases that will be covered.

| How function is being called | Traditional function `this` |   Arrow function `this` |
| --- | --- | --- |
| On its own |                    ? |                      ? |
| On an object |                    ? |                      ? |
| Using `bind`, `call`, or `apply` |                    ? |                      ? |

>💡 Some of these cases have different outcomes depending if they’re in strict mode or not. Make sure you understand Strict-mode before you proceed. You can read about strict mode in [Appendix B](./appendix-b.md).
>

# globalThis

Before diving in, it’s important to understand the `globalThis` keyword. In certain cases, it will be used as a fallback for `this`. It, unlike `this`, has the same value no matter the context. Below you can see the value of `globalThis` in different contexts.

| Context | `globalThis` |
| --- | --- |
| NodeJS | `global` |
| Deno | `window` |
| All major browsers | `window` |

`globalThis` is not to be confused with `this` in the global scope (outside any function). In browsers they’re the same but not in NodeJS or Deno.

| Context | `this` in global scope |
| --- | --- |
| NodeJS | Empty object |
| Deno | `undefined` |
| All major browsers | `window` |

# Traditional function on its own

If you run a traditional function on its own, the value of its `this` will depend if the function is in strict mode or not.

If in strict mode, `this` will be `undefined`. If not in strict mode, `this` will be `globalThis`. 

```js
"use strict"

function func() {
  return this
}

console.log(func() === undefined) // true
```

```js
function func() {
  return this
}

console.log(func() === globalThis) // true
```

# Traditional function on an object

Calling a traditional function on an object will result in its `this` being the object its called on. 

```js
const obj = {
  func: function () {
    return this
  }
}

console.log(obj.func() === obj) // true
```

This provides a useful way to retain and share state between function calls.

```js
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

> ⚠️ `this` doesn’t follow a function if you remove it from its object, e.i. if you assign it to a variable and call it on its own.
>```js
>const obj = {
>  func: function () {
>    return this
>  }
>}
>
>const func = obj.func
>console.log(func()) // globalThis || undefined
>```
>

# Traditional function using bind, call, or apply

There are three methods that let you manipulate the `this` inside functions. They are called `bind`, `call`, and `apply`. They exist on `Function.prototype`, meaning they exist on all functions.

### bind

`bind` creates a new function with a `this` that you specify. The function it creates will have your `this` no matter the context. 

```js
function f1() {
  return this
}

const f2 = f1.bind({ iAmThis: true })
console.log(f2()) // { iAmThis: true }
```

### call

`call` is similar to `bind`, but it calls the function directly with the new `this` instead of creating a new function. The first argument is the `this`, and the rest are the arguments that should be passed to the function.

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

# Arrow function on its own or on an object

>💡 An arrow function called on its own or an on object will have the same `this`.
>

Arrow functions don’t have their own `this` binding. Instead, the `this` inside arrow functions is based on the scope they are defined within.

If an arrow function is defined inside a traditional function, it will have the same `this` as the traditional function. If it’s not defined inside a traditional function, its `this` will be the `this` of the global scope.

It’s important to remember that the `this` is based on where the arrow function is *defined* not where it’s called. This turns out to be a useful feature when it comes to callbacks. Callbacks are functions that you supply to other functions as arguments. Doing this with traditional functions means you often loose control of `this`. That’s because you usually don’t control *how* the callback is called. Luckily, arrow functions don’t care how they’re called, only where they’re defined.

An interesting application of arrow functions can be seen with `setTimeout`. `setTimeout` is a function that you call with two arguments. The first is a callback and the second is a number representing milliseconds. `setTimeout` will call your callback after the time has passed that you specified in milliseconds. 

Below we have two objects. Both of them have a method called `slowIncrement` which calls `setTimeout` with a callback to be called in 500 milliseconds. Both callbacks access `this` to increment a counter. The first callback is a traditional function and the second is an arrow function.

```js
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

>💡 You can, of course, always create a traditional function with a controllable `this` using `bind`.
>

# Arrow function using bind, call, or apply

Because arrow functions don’t have their own `this` binding, `bind`, `call`, or `apply` will have no effect on them.

```js
const func = () => this

const customThis = {}

console.log(func.bind(customThis)() === customThis) // false
console.log(func.call(customThis) === customThis) // false
console.log(func.apply(customThis) === customThis) // false
```
