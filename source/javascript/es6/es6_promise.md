# Promises

## What are Promises
Promises, have been around quite a while and are defined by a spec called Promise/A+. 
ES6 has adopted this spec for its Promise implementation.

Promises give us a way to handle asynchronous processing in a more synchronous fashion. 
They represent a value that we can handle at some point in the future. And, better than callbacks here, 
Promises give us guarantees about that future value, specifically:

* No other registered handlers of that value can change it (the Promise is immutable)
* We are guaranteed to receive the value, regardless of when we register a handler for it, 
even if it's already resolved (in contrast to events, which can incur race conditions).

## Problems of callbacks
Callback is the pattern for dealing with asynchronous functions in general,but they're not perfect. 
Two bigger problems are:

* Inversion of control
* Complicated error handling

## Creating Promises
The standard way to create a Promise is by using the `new Promise` constructor which accepts a handler that is given two functions as parameters. 
The first handler (typically named `resolve`) is a function to call with the future value when it's ready; 
and the second handler (typically named `reject`) is a function to call to reject the Promise if it can't resolve the future value.

```javascript
var p = new Promise(function(resolve, reject) {  
   if (/* condition */) {
      resolve(/* value */);  // fulfilled successfully
   }
   else {
      reject(/* reason */);  // error, rejected
   }
});
```
In this way, a Promise itself has one of the following three states:

* `Pending` - until a Promise is fulfilled it is in pending state
* `Fulfilled` - when the first handler is called the Promise is considered fulfilled with the value passed to that handler.
* `Rejected` - if the second handler is called, the Promise is considered rejected with the value passed to that handler.

A Promise can only be "settled" (meaning it has been fulfilled or rejected) once. 
Other consumers, as we stated previously, can not change the settled value.


## Consuming Promises
Every Promise has a method, called `then()`, which accepts two functions as arguments: resolve, and reject, in that order. 
Calling then on a Promise and passing it these functions allows the function you passed to the constructor to access them.

A Promise's `then()` method actually takes two possible parameters. 
The first is the function to be called when the Promise is fulfilled and the second is a function to be called if the Promise is rejected.

```javascript
var promise = new Promise(function(resolve, reject) {
  // ... some code

  if (/* 异步操作成功 */){
    resolve(value);
  } else {
    reject(error);
  }
}).then(function(value) {
    // success
}, function(error) {
    // failure
});
```

hat makes Promises so useful for asynchronous functions is that once a Promise is settled, 
it doesn’t change anymore. Furthermore, there are never any race conditions, 
because it doesn’t matter whether you invoke `then()` or `catch()` before or after a Promise is settled:

* Reactions that are registered with a Promise before it is settled, are notified of the settlement once it happens.
* Reactions that are registered with a Promise after it is settled, receive the cached settled value “immediately” (their invocations are queued as tasks).

## Chaining Promises

`then()` method always returns a new Promise object. That means you can chain several then calls to create complex and synchronous-looking control flows over asynchronous operations. 
Each `then()` receives the result of the previous then's return value.

```javascript
new Promise(function(resolve, reject) { 
	// A mock async action using setTimeout
	resolve(10);
}).
then(function(num) { console.log('first then: ' + num); return num * 2; }).                        //=> "first then: 10"
then(function(num) { console.log('second then: ' + num); throw new Error(" thrown in second"); }). //=> "second then: 20"
then(null,function(e) { console.log(e.message);});                                                 //=> "thrown in second"
```

## Syntactical Sugar for Catching Errors
We passed `then()` two functions: resolve, which we call in the event of success; and `reject()`, which we call in the event of error.
Promises also expose a function similar to `then()`, called `catch()`. It accepts a reject handler as its single argument.

```javascript
new Promise(function(resolve, reject) {
	// A mock async action using setTimeout
	setTimeout(function() { reject('Done!'); }, 30);
})
.then(function(e) { console.log('done', e); })
.catch(function(e) { console.log('catch: ', e); }); //=> 'catch: Done!'
```

## Chaining and errors
There can be one or more `then()` method calls that don’t have error handlers. 
Then the error is passed on until there is an error handler.

```javaascript
new Promise(function(resolve, reject) { 
	throw new Error("something wrong!");
}).
then().then().catch(function (e) {
    console.log(e.message);  //=> "something wrong!"
});

```
## Other ways of creating Promises
There are two more ways of creating Promises.
### 1. Promise.resolve()
`Promise.resolve(x)` works as follows:

* For most values x, it returns a Promise that is fulfilled with x:
```javascript
Promise.resolve('abc').then(x => console.log(x)); //=> abc
```
    
* If x is a Promise whose constructor is the receiver (Promise if you call `Promise.resolve()`) then x is returned unchanged:
```javascript
const p = new Promise(() => null);
onsole.log(Promise.resolve(p) === p); //=> true
```
    
* If x is a thenable, it is converted to a Promise: the settlement of the thenable will also become the settlement of the Promise. 
```javascript
const fulfilledThenable = {
      then(reaction) {
          reaction('hello');
      }
  };
  const promise = Promise.resolve(fulfilledThenable);
  console.log(promise instanceof Promise); //=> true
  promise.then(x => console.log(x)); //=> hello
```
### 2. Promise.reject()
`Promise.reject(err)` returns a Promise that is rejected with err:
```javascript
const myError = new Error('Problem!');
Promise.reject(myError).catch(err => console.log(err === myError)); //=> true
```

## Promises are always asynchronous

The Promises/A+ specification demands that the latter mode of execution be always used. It states so via the following requirement (2.2.4) for the `then()` method:
> onFulfilled or onRejected must not be called until the execution context stack contains only platform code.

```javascript
Promise.resolve('abc').then(x => console.log(x));
console.log("start");
//output order:
//start
//abc
```

## Examples

### 1. Example using XMLHttpRequest
The following is a Promise-based function that performs an HTTP GET via the event-based XMLHttpRequest API:

```javascript
function httpGet(url) {
    return new Promise(
        function (resolve, reject) {
            const request = new XMLHttpRequest();
            request.onload = function () {
                if (this.status === 200) {
                    // Success
                    resolve(this.response);
                } else {
                    // Something went wrong (404 etc.)
                    reject(new Error(this.statusText));
                }
            };
            request.onerror = function () {
                reject(new Error(
                    'XMLHttpRequest Error: '+this.statusText));
            };
            request.open('GET', url);
            request.send();
        });
}
```
This is how you use httpGet():
```javascript
httpGet('http://example.com/file.txt')
.then(
    function (value) {
        console.log('Contents: ' + value);
    },
    function (reason) {
        console.error('Something went wrong', reason);
    });
```

### 2. Example loading an image with XHR
Another simple example using Promise and XMLHttpRequest to load an image 
```javascript
function loadImageAsync(url) {
  return new Promise(function(resolve, reject) {
    var image = new Image();

    image.onload = function() {
      resolve(image);
    };

    image.onerror = function() {
      reject(new Error('Could not load image at ' + url));
    };

    image.src = url;
  });
}
```
### 3. Example delaying an activity
Let’s implement `setTimeout()` as the Promise-based function `delay()`.

```javascript
function delay(ms) {
    return new Promise(function (resolve, reject) {
        setTimeout(resolve, ms); // (A)
    });
}

// Using delay():
delay(5000).then(function () { // (B)
    console.log('5 seconds have passed!')
});
```

### 4. Example timing out a Promise
Let’s implement `setTimeout()` as the Promise-based function `delay()`.

```javascript
function timeout(ms, promise) {
    return new Promise(function (resolve, reject) {
        promise.then(resolve);
        setTimeout(function () {
            reject(new Error('Timeout after '+ms+' ms')); // (A)
        }, ms);
    });
}

// Using timeout() looks like this:
timeout(5000, httpGet('http://example.com/file.txt'))
.then(function (value) {
    console.log('Contents: ' + value);
})
.catch(function (reason) {
    console.error('Error or timeout', reason);
});
```
Note that the rejection after the timeout  does not cancel the request,but it does prevent the Promise being fulfilled with its result.

## Common Promise chaining mistakes

### 1. Mistake:losing the tail of a Promise chain
In the following code, a chain of two Promises is built, but only the first part of it is returned. 
As a consequence, the tail of the chain is lost.
```javascript
/ Don’t do this
function foo() {
    const promise = asyncFunc();
    promise.then(result => {
        ···
    });

    return promise;
}
```
This can be fixed by returning the tail of the chain:
```javascript
function foo() {
    const promise = asyncFunc();
    return promise.then(result => {
        ···
    });
}
```
If you don’t need the variable promise, you can simplify this code further:
```javascript
function foo() {
    return asyncFunc()
    .then(result => {
        ···
    });
}
```
### 2. Mistake:nesting Promises
In the following code, the invocation of `asyncFunc2()` is nested:
```javascript
/ Don’t do this
asyncFunc1()
.then(result1 => {
    asyncFunc2()
    .then(result2 => {
        ···
    });
});
```
The fix is to un-nest this code by returning the second Promise from the first then() and handling it via a second, chained, `then()`:
```javascript
asyncFunc1()
.then(result1 => {
    return asyncFunc2();
})
.then(result2 => {
    ···
});
```

### 3. Mistake:using then() for error handling
In principle, `catch(cb)` is an abbreviation for `then(null, cb)`. But using both parameters of `then()` at the same time can cause problems:
```javascript
/ Don’t do this
asyncFunc1()
.then(
    value => { // (A)
        doSomething(); // (B)
        return asyncFunc2(); // (C)
    },
    error => { // (D)
        ···
    });
```
The rejection callback (line D) receives all rejections of `asyncFunc1()`, 
but it does not receive rejections created by the fulfillment callback (line A). 
For example, the synchronous function call in line B may throw an exception or the asynchronous function call in line C may produce a rejection.

Therefore, it is better to move the rejection callback to a chained `catch()`:
```javascript
asyncFunc1()
.then(value => {
    doSomething();
    return asyncFunc2();
})
.catch(error => {
    ···
});
```


## Handling exceptions in Promise-based functions
If exceptions are thrown inside the callbacks of `then()` and `catch()` then that’s not a problem, 
because these two methods convert them to rejections.

However, things are different if you start your async function by doing something synchronous:
```javascript
function asyncFunc() {
    doSomethingSync(); // (A)
    return doSomethingAsync()
    .then(result => {
        ···
    });
}
```
If an exception is thrown in line A then the whole function throws an exception. There are two solutions to this problem.

* Solution 1: returning a rejected Promise

You can catch exceptions and return them as rejected Promises:
```javascript
function asyncFunc() {
    try {
        doSomethingSync();
        return doSomethingAsync()
        .then(result => {
            ···
        });
    } catch (err) {
        return Promise.reject(err);
    }
}
```
* Solution 2: executing the sync code inside a callback

You can also start a chain of `then()` method calls via `Promise.resolve()` and execute the synchronous code inside a callback:
```javascript
function asyncFunc() {
    return Promise.resolve()
    .then(() => {
        doSomethingSync();
        return doSomethingAsync();
    })
    .then(result => {
        ···
    });
}
```
An alternative is to start the Promise chain via the Promise constructor:
```javascript
function asyncFunc() {
    return new Promise((resolve, reject) => {
        doSomethingSync();
        resolve(doSomethingAsync());
    })
    .then(result => {
        ···
    });
}
```

## Composing Promises

### 1. Promise.all()
Sometimes we're working with multiple Promises and we need to be able to start our processing when all of them are fulfilled. 
This is where Promise.all() comes in. 

`Promise.all(iterable)` takes an iterable over Promises (thenables and other values are converted to Promises via Promise.resolve()). 
Once all of them are fulfilled, it fulfills with an Array of their values. 
If iterable is empty, the Promise returned by all() is fulfilled immediately.

```javascript
function *aa(){
  yield Promise.resolve(1);
  yield Promise.resolve(2);
}

Promise.all(aa())
.then(texts => {
    for (const text of texts) {
        console.log(text); //=> 1,2
    }
})
.catch(reason => {
    // Receives first rejection among the Promises
});
```
If any of those promises are rejected (or throw an exception) then the `Promise.all()` Promise will reject and the `catch()` below will be triggered.

```javascript
var fetchJSON = function(url) {  
  return new Promise((resolve, reject) => {
    $.getJSON(url)
      .done((json) => resolve(json))
      .fail((xhr, status, err) => reject(status + err.message));
  });
}

var itemUrls = {  
    'http://www.api.com/items/1234',
    'http://www.api.com/items/4567'
  },
  itemPromises = itemUrls.map(fetchJSON);

Promise.all(itemPromises)  
  .then(function(results) {
     // we only get here if ALL promises fulfill
     results.forEach(function(item) {
       // process item
     });
  })
  .catch(function(err) {
    // Will catch failure of first failed promise
    console.log("Failed:", err);
  });
```
Keep in mind, a failure (rejection or thrown exception) of any of the Promises in the array passed to Promise.all() will cause the Promise it returns to be rejected.

### 2. Promise.race()
Sometimes, we don't need to wait on all the Promises in our array; 
but we simply want to get the results of the first Promise in the array to fulfill. 
We can do this with `Promise.race()`, which, like `promise.all()`, 
takes an takes an iterable over Promises; but unlike `Promise.all()` will fulfill its returned Promise as soon as the first Promise in that array fulfills.

**If iterable is empty then the Promise returned by race() is never settled**.

```javascript
// A Promise that times out after ms milliseconds
function delay(ms) {  
  return new Promise((resolve, reject) => {
    setTimeout(resolve, ms);
  });
}

// Which ever Promise fulfills first is the result passed to our handler
Promise.race([  
  fetchJSON('http://www.api.com/profile/currentuser'),
  delay(5000).then(() => { user: 'guest' })
])
.then(function(json) {
   // this will be 'guest' if fetch takes longer than 5 sec.
   console.log("user:", json.user);  
})
.catch(function(err) {
  console.log("error:", err);
});
```

## Two useful additional Promise methods
This section describes two useful methods for Promises that many Promise libraries provide. 
They are only shown to further demonstrate Promises, 
you should not add them to Promise.prototype (this kind of patching should only be done by polyfills).
### 1. done()
When you chain several Promise method calls, you risk silently discarding errors. For example:

```javascript
function doSomething() {
    asyncFunc()
    .then(f1)
    .catch(r1)
    .then(f2); // (A)
}
```
If `then()` in line A produces a rejection, it will never be handled anywhere. 
The Promise library `Q` provides a method `done()`, to be used as the last element in a chain of method calls.

This is how you would implement `done()` in ECMAScript 6:
```javascript
Promise.prototype.done = function (onFulfilled, onRejected) {
    this.then(onFulfilled, onRejected)
    .catch(function (reason) {
        // Throw an exception globally
        setTimeout(() => { throw reason }, 0);
    });
};
```
While done’s functionality is clearly useful, it has not been added to ECMAScript 6.
### 2. finally()
Sometimes you want to perform an action independently of whether an error happened or not. 
For example, to clean up after you are done with a resource. That’s what the Promise method `finally()` is for, 
which works much like the finally clause in exception handling. 
Its callback receives no arguments, but is notified of either a resolution or a rejection.

```javascript
createResource(···)
.then(function (value1) {
    // Use resource
})
.then(function (value2) {
    // Use resource
})
.finally(function () {
    // Clean up
});
```
This is how you would implement `finally()` in ECMAScript 6:
```javascript
Promise.prototype.finally = function (callback) {
    const P = this.constructor;
    // We don’t invoke the callback in here,
    // because we want then() to handle its exceptions
    return this.then(
        // Callback fulfills => continue with receiver’s fulfillment or rejection
        // Callback rejects => pass on that rejection (then() has no 2nd parameter!)
        value  => P.resolve(callback()).then(() => value),
        reason => P.resolve(callback()).then(() => { throw reason })
    );
};
```
using `finally()` to tear down a test.
```javascript
const HTTP = require("q-io/http");
const server = HTTP.Server(app);
return server.listen(0)
.then(function () {
    // run test
})
.finally(server.stop);
```

# Additional resources

>[Es6.ruanyifeng#promise](http://es6.ruanyifeng.com/#docs/promise)

>[Datchley#es6-promises](http://www.datchley.name/es6-promises/)

>[Davidwalsh#promises](https://davidwalsh.name/promises)

>[Es6-in-depth-promises](http://exploringjs.com/es6/ch_promises.html)