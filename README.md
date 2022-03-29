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