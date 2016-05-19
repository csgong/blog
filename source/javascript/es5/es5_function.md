# 函数新特性

### 柯里化(currying)
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
### bind方法
`bind`方法主要用于将函数绑定至某个对象，方法返回新的函数，调用新的函数会把原始函数当作参数对象的方法来调用。
```javascript
var o = {p:1};

function add(y){
    return this.p+y
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
var o = {};

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