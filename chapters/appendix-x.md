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

>💡 The above example explained the `receiver` argument in the context of a [[Get]] trap. However, it is also relevant for a [[Set]] trap. That too, can get passed in a  `receiver` argument, and its corresponding `Reflect.set` function also takes in a `receiver` argument.
>
