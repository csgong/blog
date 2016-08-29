# Collections

##  Why collections?
Anyone familiar with JS knows that there’s already something like a hash table built into the language: objects.
A plain Object, after all, is pretty much nothing but an open-ended collection of key-value pairs. You can get, set, and delete properties, iterate over them—all the things a hash table can do. 
So why add a new feature at all?
Well, many programs do use plain objects to store key-value pairs, and for programs where this works well, there is no particular reason to switch to Map or Set. 
Still, there are some well-known issues with using objects this way:

* Objects being used as lookup tables can’t also have methods, without some risk of collision.
Therefore programs must either use Object.create(null) (rather than plain {}) or exercise care to avoid misinterpreting builtin methods (like Object.prototype.toString) as data.

* Property keys are always strings (or, in ES6, symbols). Objects can’t be keys.

* There’s no efficient way to ask how many properties an object has.

The ES6 collections do not expose their data as properties. This means that expressions like obj.key or obj[key] cannot be used to access hash table data. 
You’ll have to write map.get(key).
Also, hash table entries, unlike properties, are not inherited via the prototype chain.
The upside is that, unlike plain Objects, Map and Set do have methods, and more methods can be added, either in the standard or in your own subclasses, without conflict.

## Set 

A Set is a collection of values. It’s mutable, so your program can add and remove values as it goes. So far, this is just like an array. 
But there are as many differences between sets and arrays as there are similarities.

* First, unlike an array, a set never contains the same value twice. If you try to add a value to a set that’s already in there, nothing happens.
The value equality will be checked and is not based on the same algorithm as the one used in the === operator. Specifically, `NaN` is considered the same as `NaN`.
 
```javascript
let desserts = new Set("abcd");
desserts.size //=>4 
```
* Second, a Set keeps its data organized to make one particular operation fast: membership testing.

```javascript
// Check whether "a" is exist.
let arr = ["b","a"];
let s = new Set("ba");
arr.indexOf("a") !== -1  // slow
s.has("a");              // fast
```

Here are all the operations on sets:

* `new Set`
creates a new, empty set.

* `new Set(iterable)`
makes a new set and fills it with data from any **`iterable`** value.

* `set.size`
gets the number of values in the set.

* `set.has(value)`
returns true if the set contains the given value.

* `set.add(value)`
adds a value to the set. If the value was already in the set, nothing happens.

* `set.delete(value)`
removes a value from the set. If the value wasn’t in the set, nothing happens. 
Both .add() and .delete() return the set object itself, so you can chain them.

* `set[Symbol.iterator]()`
returns a new iterator over the values in the set. You won’t normally call this directly, but this method is what makes sets iterable. 
It means you can write for (v of set) {...} and so on.

* `set.forEach(f)`
is easiest to explain with code. It’s like shorthand for:
```javascript
for (let value of set)
    f(value, value, set);
```
This method is analogous to the .forEach() method on arrays.

* `set.keys()`, `set.values()`, `set.entries()`
return various iterators. These are provided for compatibility with Map, so we’ll talk about them below.

Of all these features, the constructor new Set(iterable) stands out as a powerhouse, because it operates at the level of whole data structures. 
You can use it to convert an array to a set, eliminating duplicate values with a single line of code. 
Or, pass it a generator: it will run the generator to completion and collect the yielded values into a set. 
This constructor is also how you copy an existing Set.


As nice as Set is, there are some missing methods that would make nice additions to a future standard:

* Functional helpers that are already present on arrays, like .map(), .filter(), .some(), and .every().
* Non-mutating set1.union(set2) and set1.intersection(set2).
* Methods that can operate on many values at once: set.addAll(iterable), set.removeAll(iterable), and set.hasAll(iterable).

The good news is that all of these can be implemented efficiently using the methods provided by ES6.

# Additional resources

>[MDN Symbol](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol)

>[Understandinges6#Symbols and Symbol Properties](https://leanpub.com/understandinges6/read#leanpub-auto-symbols-and-symbol-properties)

>[Es6.ruanyifeng#Symbol](http://es6.ruanyifeng.com/#docs/symbol)

>[Exploring ES6#Symbols](http://exploringjs.com/es6/ch_symbols.html)

>[MDN #es6-in-depth-symbols](https://hacks.mozilla.org/2015/06/es6-in-depth-symbols/)