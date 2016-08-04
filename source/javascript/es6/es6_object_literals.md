# Object literals
ECMAScript 6 makes declaring object literals even more succinct by providing shorthand syntax for initializing properties from variables and defining function methods.
It also enables the ability to have computed property keys in an object literal definition.

## Property value shorthand
The new property value shorthand lets us abbreviate the initialization of a property within an object literal,
provided that the property key matches an existing variable name.

```javascript
var foo = 'bar';
var baz = {foo};//=> {foo: "bar"}

// The ES3/ES5 equivalent
var baz = {foo: foo};

function f(x, y) {
  return {x, y};
}

// The ES3/ES5 equivalent
function f(x, y) {
  return {x: x, y: y};
}
f(1, 2); // Object {x: 1, y: 2}
```
## Computed property keys
ES6 now allows property keys of object literals to be use expressions, making them computed property keys.
```javascript
let propKey = 'foo';
let obj = {
  [propKey]: true,
  ['a' + 'bc']: 123
};
obj //=>{abc: 123, foo: true}

// The ES3/ES5 equivalent
var obj = {};
var propKey = 'foo';
obj['a' + 'bc'] = 123;
obj[propKey] = true;
obj //=>{abc: 123, foo: true}
```
Unfortunately, however, the property value shorthand cannot be combined with the computed property key syntax.
If you tried to do the following, you would get a SyntaxError:
```javascript
let key = 'value';
let value = 'Ben';

// might expect `obj` to have a property
// key named 'value', with the value of 'Ben'
// but this is a SyntaxError.
// You would need to do `{ [key] : value}`
let obj = { [key] };
```
## Method definition shorthand
ES6 now allows method defined using shorthand syntax omitting `function` keyword & colon
```javascript
var o = {
  method() {
    return "Hello!";
  }
};

// The ES3/ES5 equivalent
var o = {
  method:function() {
    return "Hello!";
  }
};
```
It’s also worth noting that computed property keys do work with method definition shorthand and property accessors:
```javascript
let pr = "print"
let obj = {
  ['h'+'ello']() {
    return 'hi';
  },
  [pr]() {
    console.log("hi");
  }
};
obj //=>Object {hello: function(),print: function()}
```
# Additional resources

>[learning-es6#enhanced-object-literals](http://www.benmvp.com/learning-es6-enhanced-object-literals/)--Mainly referenced

>[es6katas#Object literal](http://es6katas.org/)

>[es6.ruanyifeng#属性的简洁表示法](http://es6.ruanyifeng.com/#docs/object#属性的简洁表示法)

>[es6features#enhanced-object-literals](https://github.com/lukehoban/es6features#enhanced-object-literals)