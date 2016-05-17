# Javascript权威指南读书笔记-类和模块
![知识点](http://7xrv9g.com1.z0.glb.clouddn.com/Function.png)
## 1. 函数定义和命名
函数有两种定义方式，分别为函数定义和表达式
函数名通常是动词或以动词为前缀的词组。第一个字符为小写，当函数包含多个单词时，可以使用下划线或者驼峰标识，内部函数或私有函数常以下划线为前缀。
```javascript
//表达式方式
var afun = function add(){};  //表达式方式定义函数，函数名可以省略
//函数定义方式
function add(){};
```
 
因为JavaScript声明被提前，所以函数定义方式声明的函数，可以在它被定义之前调用，不过表达式方式则不能，声明提前了，但是赋值却不可以提前。
```javascript
add(1,2);  //=>3 声明提前，可以调用
addFun(1,2) //=>TypeError: addFun is not a function
//函数定义方式
function add(x,y){return x+y;};
var addFun = function(x,y){return x+y;};
```
## 2. 函数调用
### 2.1. 链式调用
当方法不需要返回值时，最好返回this,使API可以形成链式调用编程风格。

```javascript
var shape = {
    x:0,
    y:0,
    setX:function(a){this.x = a;return this;},
    setY:function(b){this.y = b;return this;}
};
shape.setX(1).setY(2); //链式调用风格
```

### 2.2. 关于this
`this`没有作用域的限制，嵌套的函数不会调用他的函数中继承this，如果想访问外部函数的this，则需要将this保存在一个变量里，通常使用`self = this`。

```javascript
var o = {
m:function(a){
    var self = this;
    f();
    function f(){
        console.log(this===o); //=> false:this的值是全局对象或者是undefined
        console.log(self===o) //=>true
    }
}};
```
### 2.3. 构造函数调用
如果调用函数或者方法带有关键字`new`,则这种就是构造函数调用。没有形参的构造函数调用可以省略圆括号，例如下面两个语句是等价的。
```javascript
var o = new Object();
var o = new Object;
```
构造函数创建一个空对象，这个对象继承自函数的prototype属性，构造函数使用新创建的对象组为函数的上下文
```javascript
var o = {
m:function(a){
   console.log(this===o); //=> false:this的值是新创建的空对象
}};
o.m();
```
如果构造函数有返回值并且返回值是对象，表达式的值就是返回的值，如果没有返回值或者返回值是原始值，那么将使用新创建的空对象作为调用结果。
```javascript
function returnObj(){
    return {a:1};
}
function returnOrig(){
    return 1;
}
console.log(new returnObj().a);  //=>1
console.log(new returnOrig()==1); //=>false:函数返回新的空对象
console.log(typeof new returnOrig()); //=>"object"
```
### 2.4. 间接调用
函数可以使用`call()`和`apply()`调用，call使用自身的实参列表作为函数的实参，apply则是以数组的形式传入实参，两个方法都需要显示的指定函数调用上下文。
## 3. 形参和实参

### 3.1.可选形参
对于那些可选的形参，最好在函数定义时添加注释optional来强调参数是可选的，并且函数内要给省略的参数一个默认值。
### 3.2.实参对象（arguments）
arguments最大的一个用处就是允许函数操作任意数量的实参
```javascript
function max () {
var result = Number.NEGATIVE_INFINITY;  //负无穷值-Infinity
    for(var i=0,len = arguments.length;i<len;i++){
        if(arguments[0] > result) result = arguments[0];
    }
    return result;
}
```
实参对象还包含callee和caller属性，callee指代当前正在执行的函数。caller不是标准的，但是大多数浏览器都实现了这个属性，它指代当前正在执行函数的函数，通过caller可以访问调用栈。
E5严格模式中读写这两个属性都会报类型错误，非严格模式则不会。
```javascript
function strictFun () {
    "use strict"
    return arguments.callee;
}
strictFun(); //=>Uncaught TypeError: 'caller', 'callee', and 'arguments' properties may not be accessed on strict mode functions or the arguments objects for calls to them
```
callee的一个应用场景就是在匿名函数中递归调用自己。
```javascript
var factorial = function(x){ 
    if (x <= 1) return 1; 
    return x * arguments. callee(x-1);
}
```
### 3.3.将对象属性用作实参
当函数包含的参数过多时，很难记住调用实参的顺序，每次调用都需要查看文档，可以通过键值对这种形式传递参数解决这个问题。
```javascript
function multiArgs(from,to,len,from_in,end_in,to_Start,to_End){ 
    //逻辑代码
}
function easyCall(args){  //包装函数，使用对象调用函数multiArgs
    multiArgs(args.from,args.to,args.len,args.from_in,args.end_in,args.to_Start,args.to_End);
}
easyCall({
    end_in:2,
    to_End:1
})   //调用方便
```

## 4. 闭包

### 4.1. 闭包概念
Javascript采用词法作用域，也就是函数执行依赖变量作用域，变量作用域是在函数定义时决定的，而不是调用时。所以Javascript函数内除了代码逻辑，还必须引用当前作用域链，函数体内的变量都可以保存在函数作用域内，这种特性称作**闭包**。
### 4.2. 实现闭包
每个函数都有一个与之相关联的作用域链，作用域链存储着变量绑定对象，在外部是访问不到这个变量绑定对象的，但是利用闭包就可以操作定义在函数内部的局部变量。
```javascript
//通过嵌套函数实现闭包
var counter = (function(){
    var n = 1;
    return function(){return n++;}; //内部函数可以访问变量绑定对象，如果在外部将函数保存起来，则就可以在外部访问内部的变量函数。
}());
counter();  //=>1;
counter();  //=>2;
//通过对象实现闭包
var counter = (function(){
    var n = 1;
    return {count:function(){return n++;},
            reset:function(){n = 1;}
    }; 
}());
counter.count();  //=>1;
counter.count();  //=>2;
counter.reset();  
counter.count();  //=>1;
//通过getter和setter实现闭包
 function counter() {
        var n = 1;
        return {
            get count() {return n++;},
            set count(m) {n = m;}
        }
    };
var counter1 = counter();
counter1.count; //=>1
```
关联到闭包的作用域链都是活动的，嵌套的函数不会将绑定的变量对象赋值或者生成一个快照，这点在把闭包放在循环中时，容易出现错误。
```javascript
function genFuns(){
    var i=0,funcs = [];
    for(;i<5;i++){
       funcs[i] = function(){return i;} 
    }
    return funcs;
}

function genFuns2(){
    var i=0,funcs = [];
    function f(v){return function(){return v;} }
    for(;i<5;i++){
       funcs[i] = f(i) 
    }
    return funcs;
}
var f = genFuns();
var f2 = genFuns2();
f[2](); //=>5;所有闭包共享变量i,所以返回的值都是同一个。
f2[2](); //=>2;
```
## 5. 函数属性、方法以及柯里化

### 5.1. 函数的length属性
函数的length的属性是指函数的形参长度，也就是定时时的参数个数，利用它可以检测函数调用时，参数个数是否正确。
```javascript
function funlen(a,b,c){
    if(arguments.length != funlen.length) {
        throw Error("Expected"+funlen.length+"args,got"+arguments.length);
    }
}
```
### 5.2. 柯里化（currying）
柯里化是一种允许使用部分参数构造函数的的方式，也就意味着你在调用函数时，可以传入部分参数得到一个返回结果。
```javascript
function greet(name,str){
    console.log(name+":"+str);
}
//定义针对greet的柯里化函数1
function curried1(name){
    greet(name,"hello");
}
//定义针对greet的柯里化函数2
function curried2(name){
    return function(str){
         greet(name,+str);
    }
}
greet("csgong","hello");
curried("csgong");
curried2("csgong")("hello");
```
这种动态改变函数方式可以使函数更具可读性和灵活性，但是可能缩小函数的作用范围。
### 5.3. bind方法
bind方法主要用于将函数绑定至某个对象，方法返回新的函数，调用新的函数会把原始函数当作参数对象的方法来调用。
```javascript
var o = {p:1};

function add(y){
    return this.x+y
}
var newFun = add.bind(o);
newFun(2);  //=>3通过调用newFun(y)来调用o.add(y);
```
可以很容易的模拟实现上述bind方法
```javascript
function bind(f,o){
    if(f.bind) return f.bind(o);
    else{
        return function(){
            f.apply(o,arguments);
        }
    }
}
```
bind方法还支持柯里化，除了第一个参数外，其余的参数也会绑定到新的方法中。
```javascript
var o = {p:1};

function add(x,y,z){
    return x+y+z
}
var newFun = add.bind(o,1,2);
newFun(3);  //=>6:1绑定到x,2绑定到y,3绑定到z
```
模拟标准的bind方法
```javascript
function f(a,b,c){
    return a+b+c;
}
Function.prototype.bind = function(o){
    var self = this,args = Array.prototype.slice.call(arguments,1);
    return function(){
        return self.apply(o,args.concat(Array.prototype.slice.call(arguments,0)));
    }
};
var newFun = f.bind(null,1,2);
newFun(3);  //=>6
```
## 6. 通过构造函数Function()定义函数

`Function()`函数可以定义方法，其最后一个参数是函数体字符串，其余参数全部是函数定义的形参，通过构造函数定义的方法都是匿名的。
```javascript
var newFun = new Function("x","y","z","return x+y+z");
newFun(1,2,3); //=>6  等价于var newFun = function(x,y,z){return x+y+z}
newFun.name; //=>"anonymous"匿名的
```
构造函数最重要的一点就是函数体代码总是在顶层代码执行，也就是在全局作用域中执行。
```javascript
var scope = "global";
function constructFun(){
    var scope = "local";
    return new Function("return scope");
}
constructFun()(); //=>"global" 读取不到局部作用域
```
## 7. 记忆

在一些函数中，可以将上次的计算结果缓存下来，达到一种函数记忆功能。
```javascript
function memorize(f){
    var cache = {};
    var key = arguments.length+Array.prototype.join.call(arguments,"-");
    if(cache[key]) return cache[key];
    return cache[key] = f.apply(this,arguments);
}
```
