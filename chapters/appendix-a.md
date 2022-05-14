# Appendix A - Well-known Symbols

Symbols can even be used to expose the internal logic of JavaScript. There is a set of predefined symbols called *well-known Symbols.* These predefined symbols live as properties on objects and represent functionality that was previously only accessible by internal algorithms in JavaScript. These well-known Symbols can be found as properties on the `Symbol` object. Here they are listed:

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

### Well-known symbol example

An interesting well-known Symbol is `Symbol.toStringTag`. It is used to give a default description of an object. It is used by `Object.prototype.toString`, which is the method called when you execute `.toString()` on your object literal.
By setting a custom value on the `Symbol.toStringTag` property, we can trick `Object.prototype.toString` into labeling our object whatever we want.

```js
const obj = {}

console.log(obj.toString()) // [object Object]

obj[Symbol.toStringTag] = "Hi Mom!"

console.log(obj.toString()) // [object Hi Mom!]
```
 