# Symbol

Symbols are a **primitive** type introduced in ECMAScript 6, 
joining the existing primitive types: strings, numbers, booleans, null, and undefined. 
A symbol is a **unique** and **immutable** data type and may be used as an identifier or private members for object properties.

## Creating Symbols

You create symbols via the factory function Symbol() (which is loosely similar to String returning strings if called as a function):
```javascript
let s = Symbol();
```
Symbol() has an optional string-valued parameter that lets you give the newly created Symbol a description. 
That description itself cannot be used to access the property,it is used when the symbol is converted to a string (via toString() or String()):
```javascript
const s = Symbol('book');
s.toString(); //=> "Symbol(book)"
```
Because symbols are primitive values, calling new Symbol() throws an error when called.
```javascript
var sym = new Symbol(); // TypeError
```
Every symbol returned by Symbol() is unique, every symbol has its own identity:
```javascript
Symbol() == Symbol()  //=> false
Symbol() === Symbol() //=> false
```
You can see that symbols are primitive if you apply the typeof operator to one of them – it will return a new symbol-specific result:
```javascript
let s = Symbol();
typeof s  // =>"symbol"

```

One quick caveat about symbols: unlike almost anything else in the language, 
they can’t be automatically converted to strings or numbers. Trying to concatenate a symbol with strings will result in a TypeError.
```javascript
var sym = Symbol("<3");
const str1 = '' + sym; // TypeError: can't convert symbol to string
const str2 = `${sym}`; // TypeError: can't convert symbol to string
const num = +sym;      // TypeError: Cannot convert a Symbol value to a number
```
You can avoid this by explicitly converting the symbol to a string, writing `String(sym)` or `sym.toString()`.

## Using Symbols

* Symbols as non-public property keys
Symbols can be used as property keys,also as computed object literal property names as well as with Object.defineProperty() and Object.defineProperties() calls:

```javascript
let firstName = Symbol("first name");

// use a computed object literal property
let person = {
    [firstName]: "Nicholas"
};

// make the property read only
Object.defineProperty(person, firstName, { writable: false });

let lastName = Symbol("last name");

Object.defineProperties(person, {
    [lastName]: {
        value: "Zakas",
        writable: false
    }
});

console.log(person[firstName]);     // "Nicholas"
console.log(person[lastName]);      // "Zakas"
```
* Constants representing concepts
In ECMAScript 5, you may have used strings to represent concepts such as colors. 
In ES6, you can use symbols and be sure that they are always unique:

```javascript
const COLOR_RED    = Symbol();
const COLOR_GREEN  = Symbol();

function getComplement(color) {
  switch (color) {
    case COLOR_RED:
      return COLOR_GREEN;
    case COLOR_GREEN:
      return COLOR_RED;
    default:
      throw new Error('Undefined color');
    }
}
```

## Retrieving Symbol Properties
The following operations allow you to retrieve property symbols from an object.

* `Object.getOwnPropertySymbols()`
the Object.getOwnPropertySymbols() method was added in ECMAScript 6
* `Reflect.ownKeys()`
* Property access via []
* `Object.assign()`

Except above operations,other operations ignore symbols as property keys.such as `for...in`、`for...of` 、`Object.keys()`、 `Object.getOwnPropertyNames()`.

```javascript
var obj = {};
var a = Symbol('a');
var b = Symbol('b');
obj[a] = 'Hello';
obj[b] = 'World';
Object.getOwnPropertySymbols(obj);// =>[Symbol(a), Symbol(b)]
Reflect.ownKeys(obj); //=> [Symbol(a), Symbol(b)]
Object.keys(obj) // =>[] return null array
```

## Sharing Symbols

You may find that you want different parts of your code to use the same symbols. For example, 
suppose you have two different object types in your application that should use the same symbol property to represent a unique identifier. 
Keeping track of symbols across files or large codebases can be difficult and error-prone. 
That’s why ECMAScript 6 provides a global symbol registry that you can access at any point in time.

When you want to create a symbol to be shared, use the Symbol.for() method instead of calling the Symbol() method. 
The Symbol.for() method accepts a single parameter, which is a string identifier for the symbol you want to create. 
That parameter is also used as the symbol’s description. For example:

```javascript
let uid = Symbol.for("uid");
let object = {};

object[uid] = "12345";

console.log(object[uid]);       // "12345"
console.log(uid);               // "Symbol(uid)"
```

The Symbol.for() method first searches the global symbol registry to see if a symbol with the key "uid" exists. 
If so, the method returns the existing symbol. If no such symbol exists, then a new symbol is created and registered to the global symbol registry using the specified key. 
The new symbol is then returned. That means subsequent calls to Symbol.for() using the same key will return the same symbol, as follows:

```javascript
let uid = Symbol.for("uid");
let object = {
    [uid]: "12345"
};

console.log(object[uid]);       // "12345"
console.log(uid);               // "Symbol(uid)"

let uid2 = Symbol.for("uid");

console.log(uid === uid2);      // true
console.log(object[uid2]);      // "12345"
console.log(uid2);              // "Symbol(uid)"
```

Another unique aspect of shared symbols is that you can retrieve the key associated with a symbol in the global symbol registry by calling the Symbol.keyFor() method
If sym isn’t in the registry, this method returns undefined

```javascript
let uid = Symbol.for("uid");
console.log(Symbol.keyFor(uid));    // "uid"

let uid2 = Symbol.for("uid");
console.log(Symbol.keyFor(uid2));   // "uid"

let uid3 = Symbol("uid");
console.log(Symbol.keyFor(uid3));   // undefined
```

## Exposing Internal Operations with Well-Known Symbols

ECMAScript 6 has predefined symbols called well-known symbols that represent common behaviors in JavaScript that were previously considered internal-only operations. 
Each well-known symbol is represented by a property on the Symbol object, such as Symbol.create.
The well-known symbols are:

* Symbol.hasInstance
Lets an object C customize the behavior of x instanceof C.

```javascript
class MyClass {
  [Symbol.hasInstance](foo) {
    return foo instanceof Array;
  }
}

[1, 2, 3] instanceof new MyClass() // true
```
* Symbol.isConcatSpreadable
A Boolean value indicating that whether Array.prototype.concat() adds the indexed elements of an array to its result (“spreading”) or the array as a single element.

```javascript
let arr1 = ['c', 'd'];
['a', 'b'].concat(arr1, 'e') // ['a', 'b', 'c', 'd', 'e']
arr1[Symbol.isConcatSpreadable] // undefined

let arr2 = ['c', 'd'];
arr2[Symbol.isConcatSpreadable] = false;
['a', 'b'].concat(arr2, 'e') // ['a', 'b', ['c','d'], 'e']
```
* Symbol.iterator 
A method with this key makes an object iterable (its elements can be iterated over by language constructs such as the for-of loop and the spread operator (...)); it returns an iterator.
* Symbol.match 
A method used by String.prototype.match() to compare strings.
* Symbol.replace 
A method used by String.prototype.replace() to replace substrings.
* Symbol.search 
A method used by String.prototype.search() to locate substrings.
* Symbol.species 
The constructor for making derived objects. (Derived objects are covered in Chapter 8.)
* Symbol.split 
A method used by String.prototype.split() to split up strings.
* Symbol.toPrimitive 
A method that returns a primitive value representation of an object.
* Symbol.toStringTag 
A string used by Object.prototype.toString() to create an object description.
* Symbol.unscopables 
An object whose properties are the names of object properties that should not be included in a with statement.

# Additional resources

>[MDN Symbol](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol)

>[Understandinges6#Symbols and Symbol Properties](https://leanpub.com/understandinges6/read#leanpub-auto-symbols-and-symbol-properties)

>[Es6.ruanyifeng#Symbol](http://es6.ruanyifeng.com/#docs/symbol)

>[Exploring ES6#Symbols](http://exploringjs.com/es6/ch_symbols.html)

>[MDN #es6-in-depth-symbols](https://hacks.mozilla.org/2015/06/es6-in-depth-symbols/)