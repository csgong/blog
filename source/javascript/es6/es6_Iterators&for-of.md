# Iterators and the for-of loop

## for-of
How do you loop over the elements of an array? When JavaScript was introduced, twenty years ago, you would do it like this:
```javascript
for (var index = 0; index < myArray.length; index++) {
  console.log(myArray[index]);
}
```
Since ES5, you can use the built-in `forEach` method:
```javascript
myArray.forEach(function (value) {
  console.log(value);
});
```
This is a little shorter, but there is one minor drawback: you can’t break out of this loop using a `break` statement or return from the enclosing function using a `return` statement.

It sure would be nice if there were just a for-loop syntax that looped over array elements.
How about a for–in loop?
```javascript
for (var index in myArray) {    // don't actually do this
  console.log(myArray[index]);
}
```
This is a bad idea for several reasons:

* The values assigned to index in this code are the strings "0", "1", "2" and so on, not actual numbers. Since you probably don’t want string arithmetic ("2" + 1 == "21"), this is inconvenient at best.
* The loop body will execute not only for array elements, but also for any other expando properties someone may have added. For example, if your array has an enumerable property myArray.name, then this loop will execute one extra time, with index == "name". Even properties on the array’s prototype chain can be visited.
* Most astonishing of all, in some circumstances, this code can loop over the array elements in an arbitrary order.

In short, for–in was designed to work on plain old Objects with string keys. For Arrays, it’s not so great.

The new `for-of` loop added in es6
```javascript
for (var value of myArray) {
  console.log(value);
}
```

`for-of` is the most concise, direct syntax yet for looping through array,it avoids all the pitfalls of for–in
and unlike forEach(), it works with break, continue, and return.

`for–of` is not just for arrays. It also works on iterable objects. like string、map、set.

## What Iterators
ES6 introduces a new mechanism for traversing data: iteration. Two concepts are central to iteration:

* An iterable is a data structure that wants to make its elements accessible to the public. 
It does so by implementing a method whose key is Symbol.iterator. That method is a factory for iterators.
* An iterator is a pointer for traversing the elements of a data structure (think cursors in databases).


A `for–of` loop starts by calling the `[Symbol.iterator]()` method on the collection. 
This returns a new iterator object. An iterator object can be any object with a `next()` method; 
the `for–of` loop will call this method repeatedly, once each time through the loop. For example, here’s the simplest iterator object I can think of:
```javascript
var zeroesForeverIterator = {
  [Symbol.iterator]: function () {
    return this;
  },
  next: function () {
    return {done: false, value: 0};
  }
};
```
Every time `next()` returns wrapped in an object, as the value of the property value. 
The boolean property `done` indicates when the end of the sequence of items has been reached.

## Implementing iterables
An object becomes iterable (“implements” the interface Iterable) if it has a method (own or inherited) whose key is `Symbol.iterator`. 
That method must return an iterator, an object that iterates over the items “inside” the iterable via its method `next()`.


In TypeScript notation, the interfaces for iterables and iterators look as follows:
```javascript
interface Iterable {
    [Symbol.iterator]() : Iterator;
}
interface Iterator {
    next() : IteratorResult;
    return?(value? : any) : IteratorResult;
}
interface IteratorResult {
    value: any;
    done: boolean;
}
```
Let’s first implement a dummy iterable to get a feeling for how iteration works.
```javascript
const iterable = {
    [Symbol.iterator]() {
        let step = 0;
        const iterator = {
            next() {
                if (step <= 2) {
                    step++;
                }
                switch (step) {
                    case 1:
                        return { value: 'hello', done: false };
                    case 2:
                        return { value: 'world', done: false };
                    default:
                        return { value: undefined, done: true };
                }
            }
        };
        return iterator;
    }
};
```
The function can be simplified if the iterable and the iterator are the same object:
```javascript
function iterateOver(...args) {
    let index = 0;
    const iterable = {
        [Symbol.iterator]() {
            return this;
        },
        next() {
            if (index < args.length) {
                return { value: args[index++] };
            } else {
                return { done: true };
            }
        },
    };
    return iterable;
}
```
Even if the original iterable and the iterator are not the same object, it is still occasionally useful if an iterator has the following method (which also makes it an iterable):
```javascript
[Symbol.iterator]() {
    return this;
}
```
All built-in `ES6` iterators follow this pattern.

## Using iterable objects 
The concept of iterable objects is used throughout the language, not only in `for–of` but in the `Map` and `Set` `constructors`, `destructuring` `assignment`, and the new `spread operator`.

* Destructuring via Array patterns works for any iterable
```javascript
const set = new Set().add('a').add('b').add('c');
const [x,y] = set;// => x='a'; y='b'
const [first, ...rest] = set;  // => first='a'; rest=['b','c'];
```
* The for-of loop
```javascript
for (const x of iterable) {
    ···
}
```
Note that the iterability of iterable is required, otherwise for-of can’t loop over a value. That means that non-iterable values must be converted to something iterable. For example, via `Array.from()`.

* Array.from()

Array.from() converts **iterable** and **Array-like** values to Arrays. It is also available for **typed Arrays**.
```javascript
Array.from(new Map().set(false, 'no').set(true, 'yes')); //=> [[false,'no'], [true,'yes']]
Array.from({ length: 2, 0: 'hello', 1: 'world' });  //=> ['hello', 'world']
```
* The spread operator (...)

The spread operator inserts the values of an iterable into an Array:
```javascript
const arr = ['b', 'c'];
['a', ...arr, 'd']; //=> ['a', 'b', 'c', 'd']
```
That means that it provides you with a compact way to convert **any iterable** to an Array:
```javascript
const arr = [...iterable];
```
The spread operator also turns an iterable into the arguments of a function, method or constructor call:
```javascript
 Math.max(...[-1, 8, 3]);  //=> 8
```
* Maps and Sets

The constructor of a Map turns an iterable over [key, value] pairs into a Map and the constructor of a Set turns an iterable over elements into a Set:
```javascript
//set constructor
const set = new Set(['red', 'green', 'blue']);
set.has('red'); //=>true

//map constructor
const map = new Map([['uno', 'one'], ['dos', 'two']]);
map.get('uno'); //=>'one'
```
* Promises

Promise.all() and Promise.race() accept iterables over Promises:
```javascript
Promise.all(iterableOverPromises).then(···);
Promise.race(iterableOverPromises).then(···);
```

* yield*

yield* is an operator that is only available inside generators. It yields all items iterated over by an iterable.
```javascript
function* yieldAllValuesOf(iterable) {
    yield* iterable;
}
```
The most important use case for yield* is to recursively call a generator (which produces something iterable).


## The optional iterator methods`return()`
The optional iterator methods`return()` gives an iterator the opportunity to clean up if an iteration ends prematurely.It closes an iterator. In `for-of` loops, premature (or abrupt, in spec language) termination can be caused by:

* break
* continue 
* throw
* return

In each of these cases, for-of lets the iterator know that the loop won’t finish. 
```javascript
function createIterable() {
    let done = false;
    const iterable = {
        [Symbol.iterator]() {
            return this;
        },
        next() {
            if (!done) {
                done = true;
                return { done: false, value: 'a' };
            } else {
                return { done: true, value: undefined };
            }
        },
        return() {
            console.log('return() was called!');
            return { done: true };
        },
    };
    return iterable;
}
for (const x of createIterable()) {
    console.log(x); //=> a
    // There is only one value in the iterable and
    // we abort the loop after receiving it
    break;
}
//`return()` method is called after `break`;
//=> return() was called!
```
The `return()` method must return an object. That is due to how generators handle the return statement

## Closing iterators
An  iterator is closable if it has a method `return()`. Not all iterators are closable. For example, Array iterators are not:
```javascript
let iterable = ['a', 'b', 'c'];
const iterator = iterable[Symbol.iterator]();
'return' in iterator; //=> false
```
Generator objects are closable by default. If you invoke `return()` on the result of a Generator object,iteration is finished:
```javascript
function* elements() {
    yield 'a';
    yield 'b';
    yield 'c';
}
const iterator = elements();
iterator.next();   //=> {value: 'a', done: false }
iterator.return(); //=> { value: undefined, done: true }
iterator.next()    //=> { value: undefined, done: true }

```
If an iterator is not closable, you can continue iterating over it after an abrupt exit (such as the one in line A) from a `for-of` loop:
```javascript
function twoLoops(iterator) {
    for (const x of iterator) {
        console.log(x);
        break; // (A)
    }
    for (const x of iterator) {
        console.log(x);
    }
}
function getIterator(iterable) {
    return iterable[Symbol.iterator]();
}

twoLoops(getIterator(['a', 'b', 'c']));
// Output:
// a
// b
// c
```
## Preventing iterators from being closed
The following class is a generic solution for preventing iterators from being closed. It does so by wrapping the iterator and forwarding all method calls except `return()`.
```javascript
class PreventReturn {
    constructor(iterator) {
        this.iterator = iterator;
    }
    /** Must also be iterable, so that for-of works */
    [Symbol.iterator]() {
        return this;
    }
    next() {
        return this.iterator.next();
    }
    return(value = undefined) {
        return { done: false, value };
    }
    // Not relevant for iterators: `throw()`
}
```
If we use PreventReturn, the result of the generator elements() won’t be closed after the abrupt exit in the first loop of twoLoops().
```javascript
function* elements() {
    yield 'a';
    yield 'b';
    yield 'c';
}
function twoLoops(iterator) {
    for (const x of iterator) {
        console.log(x);
        break; // abrupt exit
    }
    for (const x of iterator) {
        console.log(x);
    }
}
twoLoops(elements());
// Output:
// a

twoLoops(new PreventReturn(elements()));
// Output:
// a
// b
// c
```

# Additional resources

>[MDN iterable](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols#iterable)

>[Es6.ruanyifeng#iterator](http://es6.ruanyifeng.com/#docs/iterator)

>[Exploring ES6#iteration](http://exploringjs.com/es6/ch_iteration.html)

>[Es6-in-depth-iterators-and-the-for-of](https://hacks.mozilla.org/2015/04/es6-in-depth-iterators-and-the-for-of-loop/)