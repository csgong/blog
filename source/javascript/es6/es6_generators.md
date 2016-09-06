# Generators

Generators, a new feature of ES6, are functions that can be paused and resumed. That helps with many applications. Two important ones are:

* Implementing iterables
* Blocking on asynchronous function calls

## What are generators?

Generators are functions that can be paused and resumed.

As a first example, consider the following generator function whose name is `quips`:
```javascript
function* quips(name) {
  yield "hello " + name + "!";
  yield "i hope you are enjoying the blog posts";
  if (name.startsWith("X")) {
    yield "it's cool how your name starts with X, " + name;
  }
  yield "see you later!";
}
```
Generators has a lot in common with functions, but you can see two differences right away:

* Regular functions start with function. Generator-functions start with function*.

* Inside a generator-function, yield is a keyword, with syntax rather like return. 
The difference is that while a function (even a generator-function) can only return once, a generator-function can yield any number of times. 
The yield expression suspends execution of the generator so it can be resumed again later.


What happens when you call the quips() generator-function?
```javascript
var iter = quips("jorendorff");  //=> [object Generator]
  
iter.next(); //=>{ value: "hello jorendorff!", done: false }
iter.next(); //=> { value: "i hope you are enjoying the blog posts", done: false }
iter.next(); //=>{ value: "see you later!", done: false }
iter.next(); //=>{ value: undefined, done: true }
```
You’re probably very used to ordinary functions and how they behave. 
When you call them, they start running right away, and they run until they either return or throw. 
All this is second nature to any JS programmer.

Calling a generator looks just the same: `quips("jorendorff")`. 
But when you call a generator, it doesn’t start running yet. Instead, 
it returns a paused Generator object (called iter in the example above). 
You can think of this Generator object as a function call, frozen in time. Specifically, it’s frozen right at the top of the generator-function, just before running its first line of code.

Each time you call the Generator object’s `next()` method, 
the function call thaws itself out and runs until it reaches the next `yield` expression.

That’s why each time we called `iter.next()` above, we got a different string value. 
Those are the values produced by the yield expressions in the body of `quips()`.

On the last `iter.next()` call, we finally reached the end of the generator-function, 
so the `.done` field of the result is true. Reaching the end of a function is just like returning undefined, 
and that’s why the `.value` field of the result is undefined.

## Kinds of generators
There are four kinds of generators:

1. Generator function declarations:
```javascript
function* genFunc() { ··· }
const genObj = genFunc();
```
2. Generator function expressions:
```javascript
const genFunc = function* () { ··· };
const genObj = genFunc();
```
3. Generator method definitions in object literals:
```javascript
const obj = {
     *generatorMethod() {
         ···
     }
 };
const genObj = obj.generatorMethod();
```
4. Generator method definitions in class definitions (class declarations or class expressions)
```javascript
class MyClass {
     *generatorMethod() {
         ···
     }
 }
const myInst = new MyClass();
const genObj = myInst.generatorMethod();
```


## Generators as iterators

Generator objects implement both the interfaces Iterable and Iterator (shown below). 
That means that the result of a generator function is both an iterable and an iterator. 
ES6 language constructs that support iterables can be applied to generator objects.

```javascript
function* genNum(){
     yield 1;
     yield 2;
     yield 3;
 }
 
//for of
for (const x of genNum()) {
    console.log(x); //=>1,2,3
}

//spread operator
const arr = [...genNum()]; //=> [1, 2, 3]

//destructuring
const [x, y,z] = genNum();
console.log(x,y,z); //=> 1 2 3
```

The previous generator function did not contain an explicit `return`. An implicit `return` is equivalent to returning undefined. Let’s examine a generator with an explicit `return`:

```javascript
function* genFuncWithReturn() {
    yield 'a';
    yield 'b';
    return 'result';
}
```
The returned value shows up in the last object returned by `next()`, whose property `done` is true:

```javascript
const genObjWithReturn = genFuncWithReturn();
genObjWithReturn.next(); //=>{ value: 'a', done: false }
genObjWithReturn.next(); //=>{ value: 'b', done: false }
genObjWithReturn.next(); //=>{ value: 'result', done: true }
```
However, most constructs that work with iterables ignore the value inside the done object:

```javascript
for (const x of genFuncWithReturn()) {
    console.log(x); //=> a,b
}
const arr = [...genFuncWithReturn()]; //=>  ['a', 'b']
```
Let’s look at an example that demonstrates how convenient and astonshingly generators are for implementing iterables.

```javascript
//iterate over the properties of an object
function* objectEntries(obj) {
    // In ES6, you can use strings or symbols as property keys,
    // Reflect.ownKeys() retrieves both
    const propKeys = Reflect.ownKeys(obj);

    for (const propKey of propKeys) {
        yield [propKey, obj[propKey]];
    }
}

const jane = { first: 'Jane', last: 'Doe' };
for (const [key,value] of objectEntries(jane)) {
    console.log(`${key}: ${value}`); //=>first: Jane,  last: Doe
}
```
For comparison – an implementation of `objectEntries()` that doesn’t use generators is much more complicated:

```javascript
function objectEntries(obj) {
    let index = 0;
    let propKeys = Reflect.ownKeys(obj);

    return {
        [Symbol.iterator]() {
            return this;
        },
        next() {
            if (index < propKeys.length) {
                let key = propKeys[index];
                index++;
                return { value: [key, obj[key]] };
            } else {
                return { done: true };
            }
        }
    };
}
```
```javascript
```
# Additional resources

>[MDN iterable](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols#iterable)

>[Es6.ruanyifeng#iterator](http://es6.ruanyifeng.com/#docs/iterator)

>[Exploring ES6#iteration](http://exploringjs.com/es6/ch_iteration.html)

>[Es6-in-depth-iterators-and-the-for-of](https://hacks.mozilla.org/2015/04/es6-in-depth-iterators-and-the-for-of-loop/)