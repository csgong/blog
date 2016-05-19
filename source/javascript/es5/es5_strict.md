# 严格模式（strict）
    严格模式`"user strict"`是ECMAScript 5引入的一条指令，他出现在脚本的开始或者函数体的开始、任何实体语句之前，使用`"user strict`表明其后续的代码将采用严格模式执行，严格模式下，Javascript 语言受到了一定的限制，修正了一些缺陷，此时语言正健壮，更安全
## 严格模式与非严格模式的区别

* 变量声明
严格模式下所有变量都要先声明，否则抛出异常。在非严格模式下，没有声明变量就赋值的话，就是隐式的创建了一个全局变量。
```javascript
function nostrictFun(){
  nodefinedVar1  = 1;
  console.log(nodefinedVar1);
}
function strictFun(){
   "use strict"
  nodefinedVar2  = 1;
  console.log(nodefinedVar2);
}
nostrictFun();    =>1
strictFun();      =>ReferenceError: nodefinedVar2 is not defined
```


* 函数调用this
在严格模式中，调用函数（不是方法）中的this是undefined,而非严格模式中，this始终是全局对象。

```javascript
function strictFun(){
  "use strict"
  console.log(this);
}
function nostrictFun(){
   console.log(this);
}
strictFun();      =>undefined
nostrictFun();    =>Window object
```
* call和apply
在严格模式中，通过call()和apply()调用函数时，其中的this就是传入的第一个参数（非严格模式中，null和undefined将会被全局对象所替代）。
```javascript
function strictFun(){
  "use strict"
  console.log(this);
}
function nostrictFun(){
   console.log(this);
}
strictFun.call();      =>undefined
nostrictFun.call();    =>Window object
```
*  属性
在严格模式中，给只读属性赋值和给不可扩展的对象创建新成员都将抛出一个类型异常（非严格模式中，这些操作只会简单的操作失败，不会报错）。
```javascript
function strictFun(){
  "use strict"
   Object.prototype = 0;
}
function nostrictFun(){
    //内置构造函数的原型是只读的
    Object.prototype = 0;  //赋值失败， 但没报错，Object.prototype 没有修改
}
strictFun.call();      =>TypeError
nostrictFun.call();    =>undefined //赋值不成功，也不报错
```  
* 关于eval
忽略，用不上。
* 关于arguments
严格模式下，arguments拥有传入参数值的静态副本，而非严格模式下却指向同一个引用（待整理）。
* 关于delete
严格模式下，delete后跟非法的标识符，将会报语法异常，而非严格模式下，就什么也不做，并返回false。
```javascript
function strictFun(){
    "use strict"
    var a = 1;
    console.log(delete a);
}  //编译器会报语法错误 "Uncaught SyntaxError: Delete of an unqualified identifier in strict mode"
function strictFun(){
    var a = 1;
    console.log(delete a);
}    =>false  //打印false
```