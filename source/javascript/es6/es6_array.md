# Array的扩展

## Array.from(arrayLike[, mapFn[, thisArg]])
`Array.from` method creates a new Array instance from an array-like or iterable object.
array-like is an object with a length property and indexed elements,such as arguments and 
the result of `document.querySelectorAll()` etc 
```javascript
//create an array-like object
let al = {
    '0': 'a',
    '1': 'b',
    '2': 'c',
    length: 3
};
Array.from(al)    // => ['a', 'b', 'c']
//等价于
Array.prototype.slice.call(al) // => ['a', 'b', 'c']  
//等价于
Array.prototype.map.call(al,(v) => v);
```
第二个可选参数作用类似于数组的map方法，用来对每个元素进行处理，将处理后的值放入返回的数组
```javascript
//create an array-like object
let al = {
    '0': 'a',
    '1': 'b',
    '2': 'c',
    length: 3
};
Array.from(al, (x) => x + x); //=>["aa", "bb", "cc"]
Array.from(al, (x) => this + x,new Date()); //=>["aa", "bb", "cc"]
```
第三个可选参数用来指定处理函数的上下文（this）
```javascript
//create an array-like object
let al = {
    '0': 'a',
    '1': 'b',
    '2': 'c',
    length: 3
};
Array.from(al,function(x){
  return x + this.name;
},{name:1});
```
`Array.from`也可以将字符串转为数组
```javascript
Array.from("aaa");  //=>["a", "a", "a"]
```

## Array.of(element0[, element1[, ...[, elementN]]])

`Array.of()`方法用于将一组值，转换为数组。
```javascript
Array.of();         //=>[]
Array.of(undefined);//=>[undefined]
Array.of("a");      //=>["a"]
Array.of("a","b",1);//=>["a", "b", 1]
```
这个方法的主要目的，是弥补数组构造函数Array()的不足。因为参数个数的不同，会导致Array()的行为有差异。
当参数个数为1时，相当于创建了指定数组长度的空数组，只有参数大于2个时，才会返回有参数组成的数组。
```javascript
Array() // []
Array(3) // [, , ,]
Array(1, 2, 3) // [1, 2, 3]
```

## copyWithin(target[, start[, end]])
将指定位置的成员复制到数组的其他位置并返回。方法改变了原数组但是不会修改数组的长度。

* target:替换的起始位置，数值类型，默认为0。
* start:从该位置开始读取数据，默认为0。如果为负值，表示倒数。
* end:到该位置前停止读取数据，默认等于数组长度。如果为负值，表示倒数。

```javascript
[1, 2, 3, 4, 5].copyWithin();        //=>[1, 2, 3, 4, 5]
[1, 2, 3, 4, 5].copyWithin(0,1,2);   //=>[2, 2, 3, 4, 5]
[1, 2, 3, 4, 5].copyWithin(-1,1,2);  //=>[1, 2, 3, 4, 2]
[1, 2, 3, 4, 5].copyWithin(-1,-2,-1);//=>[1, 2, 3, 4, 4]
```
如果参数target是不为数字的值，默认为从数组的起始位置开始替换。
```javascript
[1, 2, 3, 4, 5].copyWithin(undefined,-2,-1);//=>[4, 2, 3, 4, 5]
[1, 2, 3, 4, 5].copyWithin("sfsdfd",-2,-1); //=>[4, 2, 3, 4, 5]
```
如果参数start 大于end值，或者start 和end 是不能转为数字类型的值，那么方法将返回原数组。
```javascript
[1, 2, 3, 4, 5].copyWithin(0,2,1);     //=>[1, 2, 3, 4, 5]
[1, 2, 3, 4, 5].copyWithin(0,"2",1);   //=>[1, 2, 3, 4, 5]
[1, 2, 3, 4, 5].copyWithin(0,"str",-1);//=>[1, 2, 3, 4, 5]
```
## find(callback[, thisArg])
方法用于找出第一个使callback方法返回true的成员，如果没有找到，则返回undefined。

* callback:回调方法，每个数组成员都会调用一次该方法，方法有参数参数，分别为当前的值、当前的索引和原数组
* thisArg:指定调用回调方法的上下文。

```javascript
var context = {p:2};
[1, 2, 3, 4, 5].find(x => x > 3); //=>4  查找第一个大于3的元素
[1, 2, 3, 4, 5].find(x => x > 10);//=>undefined  查找第一个大于10的元素
[1, 2, 3, 4, 5].find(function(v){
    return this.p > v;
},context);                       //=> 1
```
## findIndex(callback[, thisArg])
方法用于找出第一个使callback方法返回true的成员的索引，如果没有找到，则返回-1。

* callback:回调方法，每个数组成员都会调用一次该方法，方法有参数参数，分别为当前的值、当前的索引和原数组
* thisArg:指定调用回调方法的上下文。

```javascript
var context = {p:2};
[1, 2, 3, 4, 5].findIndex(x => x > 3); //=>3  查找第一个大于3的元素索引
[1, 2, 3, 4, 5].findIndex(x => x > 10);//=>-1  查找第一个大于10的元素索引
[1, 2, 3, 4, 5].findIndex(function(v){
    return this.p > v;
},context);                            //=> 0
```

## fill(value[, start = 0[, end = this.length]])
方法可以将一个数组中指定区间的所有元素的值, 都替换成或者说填充成为某个固定的值。
方法返回被替换后的数组

* value:用来填充数组元素的值
* start:可选，开始索引
* end:  可选，结束索引

```javascript
[1,2,3,4,5,6,7,8,9].fill()       //=>[undefined, undefined, undefined, undefined, undefined, undefined, undefined, undefined, undefined]
[1,2,3,4,5,6,7,8,9].fill(7);     //=>[7, 7, 7, 7, 7, 7, 7, 7, 7]
[1,2,3,4,5,6,7,8,9].fill(7,1);   //=>[1, 7, 7, 7, 7, 7, 7, 7, 7]
[1,2,3,4,5,6,7,8,9].fill(7,1,2); //=>[1, 7, 3, 4, 5, 6, 7, 8, 9]
[1,2,3,4,5,6,7,8,9].fill(7,1,-1);//=>[1, 7, 7, 7, 7, 7, 7, 7, 9]      // 支持负数
[1,2,3,4,5,6,7,8,9].fill(7,"1","-1");//=>[1, 7, 7, 7, 7, 7, 7, 7, 9]  //非数值类型会转为数值类型
```

## 数组的entries()，keys()和values()
向java语言中map的迭代器（Iterator）一样，es6为数组提供了entries，keys,values方法，三个方法都返回一个迭代对象，
可以使用`for...of`进行遍历，也可以使用`next()`方法遍历。
entries()是键值对的遍历，keys()是数组索引的遍历，values()是值得遍历

```javascript
var arr = ["a","b","c","d"];
var entries = arr.entries();
var keys = arr.keys();
var values = arr.values();
for (let entry of entries) {
  console.log(entry);  //=>[0, "a"] [1, "b"] [2, "c"] [3, "d"]
}
for (let key of keys) {
  console.log(key);    //=>0 1 2 3
}
for (let value of values) {
  console.log(value); //=>a b c d
}
//使用next方法遍历
entries.next().value//=>[0, "a"]
values.next().value //=>a
values.next().value //=>b
values.next().value //=>c
```


## includes(searchElement[, fromIndex])
includes用于判断所给元素是否在数组中，返回true/false.

```javascript
[1, 2, 3].includes(2);     //=> true
[1, 2, 3].includes("2");   //=> false
[1, 2, 3].includes(1,2);   //=> false
[1, 2, 3].includes(2,-2);  //=> true
[1, 2, 3].includes(2,-20);  //=> true  如果搜索起始为负值，并且大于数组长度，则相当于0
[1, 2, NaN].includes(NaN); //=> true  可以检测NaN
```