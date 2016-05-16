# Javascript权威指南读书笔记-数组
![知识点](http://7xrv9g.com1.z0.glb.clouddn.com/Array2.png)
## 创建数组

创建数组可以简单的使用数组直接量，如果省略直接量中的某个值，那么省略的元素将会赋予undefined值，和显示的给数组元素赋值undefined不同，省略的值是不存在的，使用`in`检测时返回false

```javascript
var arr = [9,8,,6];
0 in arr //=>true
2 in arr //=>false  元素不存在
arr[2] = undefined;
2 in arr //=>true  元素存在,但是值为undefined
```
<!-- more --> 
除了直接量，还可以调用构造函数Array()来创建数组，如果没有参数，则创建一个空数组，如果有数值类型的参数，则创建指定长度的数组，数组没有元素,虽然指定了长度，但是这个数组的长度还是可以改变的，并不是固定不变的。

```javascript
var arr = new Array();
var hasLenArr = new Array(10);
arr.length //=>0
hasLenArr.length //=>10
hasLenArr[10] = 11;
hasLenArr.length //=>11
```
## 数组的读和写

使用`[]`读写数组，如果方括号中的值不是非负整数，那么方括号就同对象属性操作一样，将会操作数组对象属性。
```javascript
var arr = [1,2];
arr["1"]  //=>2
arr[0]    //=>1
arr[2.1] = 3;  //将会创建一个名为2.1的属性
arr["atr"] = 4;  //将会创建一个名为atr的属性
arr[2.1]  //=>访问对象属性
arr["atr"]  //=>访问对象属性
```
## 稀疏数组

如果数组的索引是不连续的，或者数组的长度大于数组的元素个数，那么这个就是稀疏数组，然后就没有然后了，嘻嘻。。。
## 数组长度

数组对象都有length属性，length属性总是大于数组最大的索引值，改变length值，将会改变数组的长度，如果将length属性定义为只读，那么数组的长度固定的，不会更改，也就是不能添加新元素到数组中，这和将数组对象设置为不可扩展效果是一样的。

```javascript
var arr = [1,2,3,4,5];
arr.length = 4;
arr //=>[1, 2, 3, 4] 数组元素减少
Object.defineProperty(arr,"length",{writable:false});//锁定长度
arr[100] = 100;
arr  =>[1, 2, 3, 4] 数组长度不变，元素不变
```
## 数组方法

* join()
join可以将数组元素拼接到一起转成字符串返回，可以指定一个字符串分隔符，默认是“，”。

```javascript
var arr = [1,2,3,4,5];
arr.join("-");  //=>"1-2-3-4-5"
```
* reverse()
reverse方法返回逆序的数组，它不返回新数组，只是对原数组进行重新排序。

```javascript
var arr = [1,2,3,4,5];
arr.reverse();  //=>[5, 4, 3, 2, 1]
```
* sort()
说到排序，就必须要说sort方法了，该方法将原数组排序并返回，不带参数时，sort按字母表顺序排序，如果想按照其他方式排序，就需要给sort方法传递一个比较函数，该函数决定了两个元素的先后循序，如果第一个参数应该在前，则返回一个小于0的数值，反之则返回大于零的数值。

```javascript
var arr = [33,10,2,9];
arr.sort();  //=>[10, 2, 33, 9]  字母表顺序
arr.sort(function(a,b){
    return a-b;
}) //=>[2, 9, 10, 33]  从小到大
arr //=>[2, 9, 10, 33]  原数组被修改
```
* concat()
concat方法创建并返回一个新数组，新的数组元素包括原数组和concat的每个参数，**如果参数是数组，则连接的是元素本身，而非数组本身**。

```javascript
var arr = [1];
var newArr = arr.concat(2,3,[4,5],[6,[7,8]]); //=>[1, 2, 3, 4, 5, 6, [7,8]] 新数组    
arr //=>[1]  原数组没有改变
```
* slice()
slice方法可以获取数组的子集，他的两个参数分别指定了子集的开始和结束位置，如果只有一个参数，那么将从当前位置到数组结尾的所有元素，如果是负数，则是相对数组末尾的位置。

```javascript
var arr = [1,2,3,4,5,6];
arr.slice(-1) //=>[6]  最后一个元素
arr.slice(0,2) //=>[1, 2] 前两个元素
new Object().toString().slice(8,-1); //=>"Object" 获取对象的类属性
```
* splice()
splice方法可以删除和插入数组元素，此方法直接修改原数组并**返回删除的元素组成的数组**。splice第一参数指定了插入或删除的起始位置，第二个参数表示删除的个数，随后的n个参数指定了要插入数组的元素**区别于concat方法，splice会插入数组本身，而不是数组的元素**。

```javascript
var arr = [1,2,3,4,5,6];
arr.splice(0,2,[7,8],9); //=>[1, 2] 返回删除的元素数组
arr  //=>[[7,8], 9, 3, 4, 5, 6] //原数组被修改
```
* pop()和push()
push方法用于向数组尾部添加一个元素，返回数组新的长度。pop方法正好相反，它删除数组最后一个元素，并返回删除的元素，两个方法都会改变原数组长度，两者配合能够实现先进后出的栈。

```javascript
var arr = [1,2,3,4,5,6];
arr.push(7);   //=>7  数组长度变为7
arr  //=>[1,2,3,4,5,6,7] //原数组添加了新元素
arr.pop();     //=>7
arr  //=>[1,2,3,4,5,6] /原数组删除了元素
```
* shift()unshift()
unshift方法用于向数组首部添加一个元素，返回数组新的长度。shift方法正好相反，它删除数组第一个元素，并返回删除的元素，两个方法都会改变原数组长度。注意**unshift方法的参数是一次性添加到数组中，而非一次一个，这意味着插入的元素顺序和他们在参数列表中的顺序一致**

```javascript
var arr = [1,2,3];
arr.unshift(4,5,6);   //=>6 数组长度变为6
arr  //=>[4,5,6,1,2,3] //原数组添加了新元素
arr.shift();     //=>4
arr  //=>[5,6,1,2,3] /原数组删除了元素
```
* toString()
数组的toString方法会将数组的**每个元素**转为字符串，并输出使用“，”拼接的字符串

```javascript
var arr = [1,2,3,[4,5,6]];
arr.toString();   //=>"1,2,3,4,5,6"
```
* foreach()
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
* map()
map方法同foreach，区别是方法返回新的数组，新的数组是由参数函数返回值组成的，map方法不改变原数组。

```javascript
var arr = [4,5,6];
arr.map(function(v,i,a){
    return v+1;
}); //=>[5, 6, 7]  返回新的数组
arr//=>[4,5,6]  原数组不变
```
* filter()
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
* every()和some()
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
* reduce()reduceRight()
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
* indexOf()和lastIndexOf()
indexOf和lastIndexOf搜索整个数组查找给定的元素是否在数组中，方法返回找到的第一个元素的索引值，没有找到、参数为空、不传参数和数组为空的情况下都返回-1。方法第二个参数是可选的，它指定数组开始开始查找的索引。第二个参数也可以是负数，表示相对数组最后一个元素的偏移量。与indexOf不用，lastIndexOf反向搜索。字符串也有相同方法，用法类似。

```javascript
var arr = [4,5,6];
arr.indexOf(6);    //=>2
arr.indexOf(100); //=>-1
arr.indexOf();    //=>-1
arr.indexOf(6,2); //=>2
arr.indexOf(6,-1); //=>2
```
## 判断数组

给定一个对象，如何判断它是否是数组呢，E5可以使用`Array.isArray()`方法来判断，E3可以使用对象的类属性来判断。

```javascript
var arr = [4,5,6];
Array.isArray(arr); //true
//E3中的判断方法
typeof arr ==="object" &&
        Object.prototype.toString.call(arr)==="[object Array]"
```
## 作为数组的字符串

E5中字符串可以使用[]访问单个字符，当然也可以使用数组的一些方法，但是由于字符串是不可变的，所以修改原数组的方法，用在字符串上是无效的，还会导致报错。

```javascript
var str = "456";
str[0] //=>"4"
Array.prototype.join.call(str,"-"); //=>"4-5-6"
Array.prototype.push.call(str,"7"); //=>ypeError: Cannot assign to read only property 'length' of object '[object String]
Array.prototype.map.call(str,function(v,i,a){
    return v+1;
}); //=>["41", "51", "61"]
```