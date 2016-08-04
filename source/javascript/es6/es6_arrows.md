# Arrows
Arrows are a function shorthand using the => syntax.
It supports both statement block bodies as well as expression bodies which return the value of the expression.
Unlike functions, arrows share the same lexical(does not bind its own `this`, `arguments`, `super`, or `new.target`) `this` as their surrounding code. 
These function expressions are best suited for non-method functions and they can not be used as constructors.
Arrow functions are always anonymous.

# Examples
* Expression bodies
```javascript
//无参数函数定义
let f = () => 5;
//包含一个参数
[].map(v => v+1);
//包含两个参数
[].map((v,i) => v+i);
//返回对象函数
//由于大括号被解释为代码块，所以如果箭头函数直接返回一个对象，必须在对象外面加上括号
[].map((v,i) => ({val:v,ind:i}));
```
* Statement bodies
```javascript
//函数体是多语句，需要使用大括号将函数体括起来，就像正常函数体一样。
let f = () =>{
    Statements...
};
```
* 函数没有`this`,使用定义时的`this`。
```javascript
// 作用域 this
let bob = {
    _name: 'Bob',
    _friends: ['Amy'],
    printFriends() {
        this._friends.forEach(f => 
            console.log(this._name  + ' known ' + f));
    }    
}; // => Bob known Amy
'use strict';
var obj = {
  i: 10,
  b: () => console.log(this.i, this),
  c: function() {
    console.log( this.i, this)
  }
}
obj.b(); // prints undefined, Window
obj.c(); // prints 10, Object {...}
```
# More info
* [MDN Arrow_functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)
* [es6katas#Arrow functions](http://es6katas.org/)
* [es6.ruanyifeng#箭头函数](http://es6.ruanyifeng.com/#docs/function#箭头函数)
* [lukehoban#arrows](https://github.com/lukehoban/es6features#arrows)