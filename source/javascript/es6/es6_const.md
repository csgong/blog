# const

* const 用于声明常量，一旦声明，值是不能被修改的,修改将会报错。
```javascript
const KEY = "123";
KEY = "456"; //TypeError: Assignment to constant variable
```
* const特性和let相同，作用域只在声明的快内有效、不可重复声明、存在暂时性死区。
* 如果使用const定义常量类型为复合类型，则任何修改定义这个复合常量地址的操作都是不允许的。
```javascript
const API = {};
API.a = 1;
console.log(API.a); // =>1
API = {};  //TypeError: Assignment to constant variable
```
如果你真想冻结对象，可以使用`Object.freeze`
