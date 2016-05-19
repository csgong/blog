# 解构（destructuring）

ES6允许按照一定模式，从数组和对象中提取值，对变量进行赋值，这被称为解构(Destructuring）。
## 数组的解构

### 基本用法
```javascript
var [a,b,c] = [1,2,3];
console.log(a,b,c); //=> 1 2 3
```
只要左边和右边的模式相同，就可以进行解构,解构不成功，则值为`undefined`
```javascript
let [a, [[b], c],d] = [1, [[2], 3]];
console.log(a,b,c,d); //=> 1 2 3 undefined
```
### 默认值
解构允许指定默认值
```javascript
let [a,b,c=3] = [1, 2];
console.log(a,b,c); //=> 1 2 3 
```
如果一个数组成员不严格等于（===）`undefined`，那么默认值是不会生效的
```javascript
var [x = 1] = [undefined];
console.log(x); //=> 1 
var [x = 1] = [null];
console.log(x); //=> null
```
默认值表达式是惰性的，只有数组成员严格等于undefined时才会求值
```javascript
function f(){
  console.log('aaa');
}

let [x = f()] = [1];
console.log(x); //=> 1 
```
默认值可以引用解构赋值的其他变量，但该变量必须已经声明
```javascript
let [x = 1, y = x] = [];     // x=1; y=1
let [x = 1, y = x] = [2];    // x=2; y=2
let [x = 1, y = x] = [1, 2]; // x=1; y=2
let [x = y, y = 1] = [];     // ReferenceError
```
## 对象的解构
数组解构是按数组顺序赋值的，而对象是按照属性的名字赋值，即变量名必须与属性名相同
```javascript
var { a, b } = { a: "a", b: "b" };
console.log(a,b); //=> a b
var { a } = { d: "aaa" };
console.log(a); //=> undefined
```
如果变量名与属性名不一致，必须写成下面这样。
```javascript
var { d:a } = { d: "aaa" };
console.log(a); //=> aaa
```
如果给一个已经声明的变量进行解构赋值，要特别小心。
```javascript
let a;
{a} = { a: "aaa" };  
```
上面代码是错误的，解析引擎会报语法错误，因为解析引擎会将{a}解析成一个块代码。
解决方法是避免将大括号写在首行

```javascript
let a;
({a} = { a: "aaa" });  
```
## 字符串的解构

```javascript
const [a, b, c] = 'abc';
console.log(a,b,c); //=> a b c
```
类似数组的对象都有一个length属性，因此还可以对这个属性解构赋值
```javascript
let {length : len} = 'hello';
console.log(len ); //=> 5
```
## 数值和布尔值的解构赋值
数值和布尔值解构赋值的规则是，只要等号右边的值不是对象，就先将其转为对象。
```javascript
let {toString: s} = 123;
s === Number.prototype.toString // true
let {toString: s} = true;
s === Boolean.prototype.toString // true
```
## 函数参数的解构赋值
函数的参数也可以使用解构赋值。
```javascript
function move({x = 0, y = 0} = {}) {
  return [x, y];
}

move({x: 3, y: 8}); // [3, 8]
move({}); // [0, 0]
move(); // [0, 0]
```
## 解构的用途

1. 交换变量的值
```javascript
[x, y] = [y, x];
```
2. 函数返回多个值
```javascript
function example() {
  return [1, 2, 3];
}
var [a, b, c] = example();
```
3. 函数参数的定义
```javascript
// 参数是一组有次序的值
function f([x, y, z]) { ... }
f([1, 2, 3])

// 参数是一组无次序的值
function f({x, y, z}) { ... }
f({z: 3, y: 2, x: 1})
```
4. 函数参数的默认值
```javascript
$.ajax = function (url, {
  async = true,
  cache = true
}) {
  // ... 
};
```