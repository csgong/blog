# Iterators and the for-of loop

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


# Additional resources

>[MDN Set](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set)

>[MDN Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)

>[Es6.ruanyifeng#set-map](http://es6.ruanyifeng.com/#docs/set-map)

>[Exploring ES6#maps-sets](http://exploringjs.com/es6/ch_maps-sets.html#_when-should-i-use-a-map-when-an-object)

>[Es6-in-depth-collections](https://hacks.mozilla.org/2015/06/es6-in-depth-collections/)