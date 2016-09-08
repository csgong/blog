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

If an exception leaves the body of a generator then `next()` throws it:

```javascript
function* genFunc() {
    throw new Error('Problem!');
}
const genObj = genFunc();
genObj.next(); //=> Error: Problem!
```

## Warning about yield

* You can only `yield` in generators, that is, yielding in callbacks doesn’t work.

```javascript
function* genFunc() {
    ['a', 'b'].forEach(x => yield x); //=> SyntaxError
}
```

* You have to put yield in parentheses if you want to use it as an operand.
 
 For example, you get a SyntaxError if you make an unparenthesized yield an operand of plus:

```javascript
console.log('Hello' + yield); // SyntaxError
console.log('Hello' + yield 123); // SyntaxError

console.log('Hello' + (yield)); // OK
console.log('Hello' + (yield 123)); // OK
```

## Sending values via `next()`

When you execute a generator,you can send values to it via next() and it receives those values via yield:
```javascript
function* dataConsumer() {
    console.log('Started');
    let input = yield 1;
    console.log(`1.${input}`);
    let input2 = yield 2;
    console.log(`2.${input2}`);
    return 'result';
}
```
Let’s use this generator interactively. First, we create a generator object:
```javascript
> const genObj = dataConsumer();
```
We now call `genObj.next()`, which starts the generator. Execution continues until the first yield,The result of `next()` is the value yielded in line `let input = yield 1;`

```javascript
> genObj.next();
//Started
//=> { value: 1, done: false }
```
We call `next()` two more times, in order to send the value 'a' to the first yield and the value 'b' to the second yield:
```javascript
> genObj.next('a');
1.a
//=>{ value: 2, done: false }

> genObj.next('b');
2.b
//=>{ value: 'result', done: true }
```
The result of the last `next()` is the value returned from `dataConsumer().done` being true indicates that the generator is finished.

**In a word,`next()` always sends a value to the currently suspended yield, but returns the operand of the following yield**.

It is important to note that the only purpose of the first invocation of `next()` is to start the observer. 
It is only ready for input afterwards, because this first invocation advances execution to the first yield. 
Therefore, any input you send via the first `next()` is ignored:
The following utility function fixes this issue:
```javascript
function coroutine(generatorFunction) {
    return function (...args) {
        const generatorObject = generatorFunction(...args);
        generatorObject.next();
        return generatorObject;
    };
}
```

## return(),throw()
Generator objects have two additional methods, return() and throw(), that are similar to next().
```javascript
function* dataConsumer() {
    console.log('Started');
    let input = yield 1;
    console.log(`1. ${input}`);
    let input2 = yield 2;
    console.log(`1. ${input2}`);
    return 'result';
}
```
Let’s recap how `next(x)` works (after the first invocation):

* The generator is currently suspended at a yield operator.
* Send the value x to that yield, which means that it evaluates to x.
* Proceed to the next yield, return or throw:
    - `yield x` leads to `next()` returning with `{ value: x, done: false }`
    - `return x` leads to `next()` returning with `{ value: x, done: true }`
    - `throw err` (not caught inside the generator) leads to `next()` throwing err.

## Kinds of generators
There are four kinds of generators:

* Generator function declarations:
```javascript
function* genFunc() { ··· }
const genObj = genFunc();
```
*  Generator function expressions:
```javascript
const genFunc = function* () { ··· };
const genObj = genFunc();
```
*  Generator method definitions in object literals:
```javascript
const obj = {
     *generatorMethod() {
         ···
     }
 };
const genObj = obj.generatorMethod();
```
*  Generator method definitions in class definitions (class declarations or class expressions)
```javascript
class MyClass {
     *generatorMethod() {
         ···
     }
 }
const myInst = new MyClass();
const genObj = myInst.generatorMethod();
```

## return()




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
How else can we use the ability of generators to act as iterators?

* Making any object iterable.

Just write a generator-function that traverses this, yielding each value as it goes. 
Then install that generator-function as the [Symbol.iterator] method of the object.

* Simplifying array-building functions.

Suppose you have a function that returns an array of results each time it’s called, like this one:
```javascript
// Divide the one-dimensional array 'icons'
// into arrays of length 'rowLength'.
function splitIntoRows(icons, rowLength) {
  var rows = [];
  for (var i = 0; i < icons.length; i += rowLength) {
    rows.push(icons.slice(i, i + rowLength));
  }
  return rows;
}
```
Generators make this kind of code a bit shorter:
```javascript
function* splitIntoRows(icons, rowLength) {
  for (var i = 0; i < icons.length; i += rowLength) {
    yield icons.slice(i, i + rowLength);
  }
}
```
The only difference in behavior is that instead of computing all the results at once and returning an array of them, 
this returns an iterator, and the results are computed one by one, on demand.

* Results of unusual size.

You can’t build an infinite array. But you can return a generator that generates an endless sequence, 
and each caller can draw from it however many values they need.

* Refactoring complex loops.

Do you have a huge ugly function? Would you like to break it into two simpler parts? Generators are a new knife to add to your refactoring toolkit. 
When you’re facing a complicated loop, you can factor out the part of the code that produces data, turning it into a separate generator-function. 
Then change the loop to say for (var data of myNewGenerator(args)).

* Tools for working with iterables.

ES6 does not provide an extensive library for filtering, mapping, and generally hacking on arbitrary iterable data sets. 
But generators are great for building the tools you need with just a few lines of code.

For example, suppose you need an equivalent of Array.prototype.filter that works on DOM NodeLists, not just Arrays. Piece of cake:

```javascript
function* filter(test, iterable) {
  for (var item of iterable) {
    if (test(item))
      yield item;
  }
}
```







2. yield*
3. return()
4. throw()
5. finally  in generator  and return 
6. Eliminate first next() call
7. Simplifying asynchronous computations via generators
8. 
```javascript
```
# Additional resources

>[MDN iterable](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols#iterable)

>[Es6.ruanyifeng#iterator](http://es6.ruanyifeng.com/#docs/iterator)

>[Exploring ES6#iteration](http://exploringjs.com/es6/ch_iteration.html)

>[Es6-in-depth-iterators-and-the-for-of](https://hacks.mozilla.org/2015/04/es6-in-depth-iterators-and-the-for-of-loop/)