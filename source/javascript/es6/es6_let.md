# let
## 基本用法
let 用于声明变量，区别于var,let声明的变量只在let所在的代码块内有效，也就是说，let是块级作用域。
```javascript
{
  let letv = 10;
  var varv = 1;
}

console.log(letv) // =>ReferenceError: a is not defined.
console.log(varv) // =>1
```
let 适合处理for循环
```javascript
var arr = [];
for (var i = 0; i < 3; i++) {
  arr["var"+i] = function(){
    console.log(i);
  };
}
for (let i = 0; i < 3; i++) {
  arr["let"+i] = function(){
    console.log(i);
  };
}
arr["var"+0](); // =>3
arr["let"+0](); // =>0
```
因为let是块级作用域的，所以每次循环后，i的值都是新的变量，所以输出的是0
## 变量不会提升（hoisting）
var 会有变量提升`hoisting`现象，JavaScript引擎在执行的时候，会把所有变量的声明都提升到当前作用域的最前面,
而let则没有，变量必须先声明后使用
```javascript
console.log(varv); // =>undefined
console.log(letv); // =>ReferenceError
let letv = 10;
var varv = 1;
```
变量提升有时会使你掉入陷阱。

* 内层变量会覆盖外层变量
```javascript
var v = "global";

function f(){
  console.log(tmp);
  if (false){
    var v = "hello world";
  }
}
f() // => undefined
```
由于变量提升，变量v的声明会提升至函数f最开始位置，但是没有赋值，所以会输出undefined。

* 循环使用的变量泄露为全局变量
## 暂时性死区（temporal dead zone）
在代码块内，使用let声明变量之前，该变量都是不可用的，这在语法上，称为“暂时性死区”（temporal dead zone，简称TDZ）
```javascript
var global = 1;

if (true) {
  global = '2'; // ReferenceError: global is not defined
  let global;
}
```
## 不允许重复声明
let不允许在**相同**作用域内，重复声明同一个变量
```javascript
var letv = 1;
let letv = 1; //=>SyntaxError:Identifier 'letv' has already been declared
```
## 函数块级作用域
es6中块级作用域同样适用于函数
```javascript
function f() { 
	console.log('I am outside!');
}
{
  if(false) {
    function f() { console.log('I am inside!'); }
  }

  f();
}
```
示例代码在es6环境中运行会输出 “I am outside!”，es5环境会输出“I am inside!”，因为es5存在函数提升，会覆盖外围的函数。

## window全局变量属性
在客户端es5中，在全局使用var声明的变量会成为window的一个属性，而es6中使用let则不会。
```javascript
var varv = "1";
let letv = "2";
console.log(window.varv);  //=>1
console.log(window.letv);  //=>undefined
```