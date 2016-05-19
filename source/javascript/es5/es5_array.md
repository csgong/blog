# 数组新特性

### Array.isArray()
`Array.isArray()`方法用来判断给定参数是否是数组，ES3中可以使用对象的类属性来判断。

```javascript
var arr = [4,5,6];
Array.isArray(arr); //true
//E3中的判断方法
typeof arr ==="object" &&
        Object.prototype.toString.call(arr)==="[object Array]"
```
### indexOf()和lastIndexOf()
indexOf和lastIndexOf搜索整个数组查找给定的元素是否在数组中，方法返回找到的第一个元素的索引值，没有找到、参数为空、不传参数和数组为空的情况下都返回-1。
方法第二个参数是可选的，它指定数组开始开始查找的索引。第二个参数也可以是负数，表示相对数组最后一个元素的偏移量。
与indexOf不同，lastIndexOf反向搜索。字符串也有相同方法，用法类似。

```javascript
var arr = [4,5,6];
arr.indexOf(6);    //=>2
arr.indexOf(100); //=>-1
arr.indexOf();    //=>-1
arr.indexOf(6,2); //=>2
arr.indexOf(6,-1); //=>2
```
### foreach()
foreach方法用于遍历数组，为每个元素调用指定的函数，这个函数就是第一个参数，foreach调用该函数时会传递三个参数，依次是数组元素、索引和数组本身。foreach无法使用return和break跳出循环，只能使用抛出异常这种方式结束循环。

```javascript
function iterateArr(){
    var arr = [4,5,6];
    try{
        arr.forEach(function(v,i,a){
            if(i===1) throw iterateArr.stopErr;
            console.log("数组"+a+"第"+i+"个元素的值为："+v);
        });
    }catch(e){
        if(e ===iterateArr.stopErr) return ;
        else throw e;
    }
}
iterateArr.stopErr = new Error("StopIteration");
iterateArr(); //=>数组4,5,6第0个元素的值为：4
```
### map()
map方法同foreach，区别是方法返回新的数组，新的数组是由参数函数返回值组成的，map方法不改变原数组。

```javascript
var arr = [4,5,6];
arr.map(function(v,i,a){
    return v+1;
}); //=>[5, 6, 7]  返回新的数组
arr//=>[4,5,6]  原数组不变
```
### filter()
filter方法调用方式和map方法一样，该方法用于返回原数组的子集，返回的子集数组是由参数函数的返回值决定的，如果函数返回可以转为true的值，那么当前元素将会被添加到返回数组中。**filter方法可以跳过稀疏数组缺失的元素，可以用来压缩数组**。

```javascript
var arr = [4,5,,6];
arr.filter(function(v,i,a){
    return v>5;  //返回大于5的数组元素集合
});  //=>[6]
//压缩稀疏数组
arr.filter(function(v,i,a){
    return true
});  //=>[4, 5, 6]
```
### every()和some()
every和some方法用于逻辑判定，他们都依据参数函数进行判定从而返回布尔值。every是当数组中所有元素调用参数函数都返回true时，它才返回true，而some是只要有一个元素调用判定函数返回true,它就返回true。**空数组调用every返回true，调用some则返回false**。
```javascript
var arr = [4,5,6];
arr.every(function(v,i,a){
    return v>5;
});=>false 4和5都不大于5
arr.some(function(v,i,a){
    return v>5;
});=>true  存在6大于5
```
### reduce()reduceRight()
reduce和reduceRight用指定函数将数组合并成一个值，他们需要两个参数，一个是执行合并的函数，另一个就是初始值。reduce和reduceRight同以上的函数调用不用，调用传递的函数的参数将变为到目前为止的合并结果、数组元素、数组索引和数组。reduceRight和reduce一样，区别就是reduceRight按照数组索引从高到底处理数组。

```javascript
var arr = [4,5];
arr.reduce(function(s,v,i,a){
    return s+v;
},100);  //=>109
arr.reduceRight(function(s,v,i,a){
    return s+v;
},100);  //=>109
```
使用reduce和reduceRight需要注意以下几点：

  1. 如果没有设置初始值，那么第一个参数就是数组的第一个元素，第二个参数就自然使用数组的第二个元素

```javascript
var arr = [4,5];
arr.reduce(function(s,v,i,a){
console.log("参数1是"+s+",参数2是"+v);
    return s+v;
});  //=>9 控制台打印 “参数1是4,参数2是5”
```
  2. 如果数组为空时，调用reduce和reduceRight将抛出TypeError

```javascript
var arr = [];
arr.reduce(function(s,v,i,a){
    return s;
});  //=> TypeError: Reduce of empty array with no initial value
```
  3. 如果数组只有一个元素，则直接返回这个元素，不会执行合并函数

```javascript
var arr = [1];
var excuteFlag = false;
arr.reduce(function(s,v,i,a){
    excuteFlag = true;
    return s;
});  //=>1
excuteFlag //=>false  处理函数没有执行
```