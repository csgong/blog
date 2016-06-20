# Object的扩展

## Array.from(arrayLike[, mapFn[, thisArg]])
`Array.from` method creates a new Array instance from an array-like or iterable object.
array-like is an object with a length property and indexed elements,such as arguments and 
the result of `document.querySelectorAll()` etc 

```javascript
[1, 2, 3].includes(2);     //=> true
[1, 2, 3].includes("2");   //=> false
[1, 2, 3].includes(1,2);   //=> false
[1, 2, 3].includes(2,-2);  //=> true
[1, 2, 3].includes(2,-20);  //=> true  如果搜索起始为负值，并且大于数组长度，则相当于0
[1, 2, NaN].includes(NaN); //=> true  可以检测NaN
```