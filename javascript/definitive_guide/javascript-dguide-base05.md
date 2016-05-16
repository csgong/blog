---
title: Javascript权威指南读书笔记-对象
date: 2016-03-23 10:01:41
tags: Javascript权威指南
categories: javascript
---
![知识点](http://7xrv9g.com1.z0.glb.clouddn.com/Object.png)
## 关于对象
***
对象是基本的数据类型，它是属性的无序集合，除了包含属性，对象还拥有三个相关的特性。
* 原型（prototype）对象
本身属性继承自原型对象。
* 对象的类
标识对象类型的字符串。
* 扩展标记
指明了是否可以添加新属性。
<!-- more --> 
## 对象属性特征
***
属性除了名字和值外，还包含三个特性
* 可写（writable）
    是否可以设置属性值。
* 可枚举（enumerable）
    是否可以使用`for/in`循环返回该属性。
* 可配置（configurable）
    是否可删除和修改属性。

## 创建对象
***
创建对象主要有如下三种方法
* 对象直接量
对象直接量就是直接使用{}创建对象,也是用的最多的方法，简单、直接。

```javascript
var obj = {a:1,b:2,c:2+1};
```
* 通过new 创建
这种方法就是调用构造函数创建对象，javascript原始类型都有构造函数。

```javascript
var obj = new Object(); //创建空对象 等价于{}
var strObj = new Object("a"); //创建一个字符串对像，等价于new String("a");
var numObj = new Object(1);//创建一个字符串对像，等价于new Number(1);
```
* Object.create()
Object.create 是ECMAScript 5新增的方法，用来创建对象，方法有两个参数，第一个为对象的原型，第二个为属性的特性描述，如果第一个参数为`null`，那么新创建的对象不继承任何东西，自然也就不会有`Object.prototype`中的基础方法，而前两种方法默认都继承自`Object.prototype`。

```javascript
var obj = Object.create(Object.prototype);//创建一个空对象，等价于前两种方法
obj.toString();  =>"[object Object]"  //继承了Object.prototype
var obj = Object.create(); //创建了无任何继承的空对象
obj.toString();  =>Uncaught TypeError: obj.toString is not a function 不存在该方法
```
对于第二个参数，后面会补充，这里先略过。
## 原型
***
* 每一个对象（除了null）都是从原型继承属性，使用对象直接量和构造函数创建对象原型就是构造函数的prototype，例如{}和new Object() 的原型对象为Object.prototype,同理 使用[]和new Array()创建的对象原型自然就是Array.prototype。
* 只有Object.prototype没有原型，所有内置的构造函数继承自Object.prototype，例如Array.prototype属性就继承自Object.prototype，这一些列的链接原型对象就是所谓的·**原型链**。

## 读取和设置属性
---
可以通过`.`和`[]`读取和设置属性,`[]`里面可以是表达式，这种方式更加灵活。

```javascript
var obj = {att1:1,att2:2,att3:3};
obj.att1    =>1  //属性存在
obj.att8    =>undefined  //属性不存在
for(var i=1;i<4;i++) {
    obj["att"+i]    //灵活读取属性
}
```
如果要访问的属性不存本对象中，则会继续在其原型对象上查找
```javascript
var obj = {att1:1,att2:2,att3:3};
var obj2 = Object.create(obj);
obj2.att1    =>1  //属性在原型对象上，成功返回
```
设置属性时，如果属性不存在，则会创建舒适，同名属性会被覆盖，原型链上的属性也会被自身属性覆盖，另外属性操作不会去修改原型链
```javascript
var obj = {att1:1,att2:2,att3:3};
obj.att1 = 100; 
obj.att4 = 400; 
obj.att1   =>100 //同名属性被覆盖
obj.att4   =>400 //创建了属性
var obj2 = Object.create(obj);
obj2.att2 = 200;
obj2.att2 =>200 //原型属性被自身属性覆盖
```
设置属性每次都会先检查原型链，以此来判断是否允许赋值操作，例如原型对象属性att1是只读的，那么给obj2的att1赋值操作是不允许的
## 检测属性
***
有时候，我们需要象判断对象是否拥有某个属性，可以通过 in 运算符、hasOwnProperty()和propertyIsEnumerable()方法来完成，甚至通过属性访问也可以间接的做到。
* in 运算符
in 运算符可以检测自身和继承的属性，存在返回true。

```javascript
var obj = {a:1};
"a" in obj        =>true
"toString" in obj =>true  //原型对象属性
```
* hasOwnProperty()
`hasOwnProperty()`方法用于检测自有属性，不包括继承属性。

```javascript
var obj = {a:1};
obj.hasOwnProperty("a");       =>true
obj.hasOwnProperty("toString");=>false  //不包括原型对象属性
```
* propertyIsEnumerable()
`propertyIsEnumerable()`方法用于检测自有属性并且是可枚举的。

```javascript
var obj = {a:1};
obj.propertyIsEnumerable("a");       =>true  //自有属性并且可枚举
obj.propertyIsEnumerable("toString");=>false 继承属性返回false
```
* 通过属性访问方式
通过属性访问方式也可以检测属性，它是通过检测属性值间接的检测

```javascript
var obj = {a:1,b:false,c:undefined,d:0,e:null},result = []; 
//检测值为0,null,undefined,false属性
for(var p in obj) {
    if(obj[p]) result.push(p);
}
push.toString(); =>"a" 
//检测非空属性
result = [];
for(var p in obj) {
    if(obj[p] != null) result.push(p);
}
push.toString(); =>"a,b,d" 
```
## 枚举属性
***
枚举属性的话可以使用`for/in`,ECMAScript 5新增的`Object.keys()`以及`Object.getOwnPropertyNames()`。
* for/in
for/in可以遍历所有可枚举的自由属性和继承属性。

```javascript
var obj = Object.create({p1:1,p2:2});
obj.a = 3;
for(var p in obj){
    console.log(p);  //依次输出a,p1,p2
}
```
从以上例子可以看出：
**`for/in`先查找自有属性，然后是原型**
**内置对象的属性是不可枚举的**
* Object.keys()和Object.getOwnPropertyNames()
`Object.keys()`返回所有可枚举的自有属性，而`getOwnPropertyNames()`则是所有属性，包括不可枚举属性

```javascript
var obj = Object.create({p1:1,p2:2});
obj.a = "a";
obj.b = "b";
Object.defineProperty(obj,"a",{enumerable:false});//定义属性a为不可枚举
Object.keys(obj)  =>["b"]  
Object.getOwnPropertyNames(obj)  =>["a","b"]
```
## 存取器属性（getter和setter）
***
* 概念
ECMASript 5 以后，Javascript属性的值可以通过get 和set存取，此时这个属性就是存取器属性
* 定义
定义存取器属性很简单，就是使用get和set

```javascript
var gsObj = {
    set name(val){this.a = val;},
    get name(){return this.a;}
}
```
* 使用
存取器属性和一般属性没什么太大区别，可以被枚举，检测和继承，只是读写是通过get和set设置的，一般用于为某些私有变量提供访问接口以及返回某些特殊值。
```javascript
var gsObj = {   
    $n:0,   //私有变量
    get random(){return Math.random();}, //产生随机数
    get next(){return this.$n++;}
}
gsObj.next =>0
gsObj.next =>1
gsObj.random =>0.27040234516466843
```
## 属性特性
***
对象属性有writable、configurable,enumerable,如果把属性的值也算作特性的话，那么属性就有四种特性，E5之前，属性的特性是不可配置的，新增的属性都是可写，可读和可配置的。E5中特性是可以获取和定义的，E5定义了一个**属性描述符(property discriptor**)对象，用来操作特性。

通过`Object.getOwnPropertyDescriptor()`可以获取对象属性的特性，
`Object.getOwnPropertyDescriptor()`只能获取自有属性的描述对象，要想获取继承属性的特性，需要遍历原型链

```javascript
var obj = {a:1};
Object.getOwnPropertyDescriptor(obj,"a"); =>Object {value: 0, writable: true, enumerable: true, configurable: true}
Object.getOwnPropertyDescriptor(gsObj,"toString"); =>undefined //获取不到原型属性描述
```

要设置属性的特性，可以使用`Object.defineProperty()`，这个方法要么修改已有属性，要么新建属性，但是不能修改继承属性。
如果要定义多个属性，使用`Object.defineProperties()`,对于不允许修改和创建的属性，两个方法都将抛出类型异常。

```javascript
var obj = {a:1};
Object.defineProperty(obj,"a",{writable:true,configurable:true,enumerable:false}); //修改属性a为不可枚举
Object.defineProperty(obj,"b",{value:2,writable:true,configurable:false,enumerable:true}); //添加不可配置属性b=2
Object.defineProperty(obj,"c",{writable:false,configurable:true,enumerable:true}); //添加只读属性c

obj.a   // =>1 属性可访问
obj.a = 100;  //修改成功,属性可修改
obj.propertyIsEnumerable("a");  //=>false,属性不可枚举 

obj.b   // =>2 属性可访问
obj.b = 200;  //修改成功,属性可修改
delete obj.b  //=> false,不允许删除
Object.defineProperty(obj,"b",{value:2,writable:true,configurable:false,enumerable:false}); //=>TypeError: Cannot redefine property，如果属性为不可配置，则属性不允许配置
Object.defineProperty(obj,"b",{value:2,writable:false,configurable:false,enumerable:true}); //配置成功，如果属性为不可配置，但是此时可以修改属性writable为false

obj.c   // =>3 属性可访问
obj.c = 300;  //修改失败,属性不可修改

Object.defineProperties(obj,{ //定义多个属性
    "d":{value:4,writable:true,configurable:true,enumerable:true},
    "e":{value:5,writable:true,configurable:true,enumerable:true}
});
```
对于存取器属性，则将value和writable替换成相应的函数即可。
```javascript
var obj = {a:1};
Object.defineProperty(obj,"a",{get:function(){return  1;},configurable:true,enumerable:false}); //修改属性a为不可枚举
```

再聊一下关于`Object.create()`方法，它的第二个参数就是属性的描述对象，可以通过第二个参数给对象添加多个属性，如果没有给属性设置描述对像，那么描述对像的值默认都为false。
```javascript
var o = Object.create({a:1},{b:null,c:{value:3,writable:true, enumerable:true,configurable:true}});
Object.getOwnPropertyDescriptor(o,"b")//=>{value: undefined, writable: false, enumerable: false, configurable: false}
```
## 对象的三个属性
***
### 1.原型
E5中可以通过`Object.getPrototypeOf()`来获取某个对象的原型，但E3没有相应方法，经常使用`Object.contructor.prototype`检测对象的原型。
创建对象时，都会继承一个contructor属性，用以指代构造函数，contructor.prototype就是对象的原型。

```javascript
var obj = {a:1};
var proto = Object.getPrototypeOf(obj);
proto.constructor.prototype ===proto;//=>true
```
要判断一个对象是否是另一个对象的原型，可以使用`isPrototype()`方法，语法为a.isPrototype(b),如果a在b的原型链上，那么`isPrototype()`将返回true,`Object.getPrototypeOf()`获取的是第一直接原型对象，例如c继承自b,b继承自a,那么`Object.getPrototypeOf(c)`获取到的就是b,虽然a也是c的原型。

```javascript
var pobj = {p:1};
var sobj = Object.create(pobj);
var proto = Object.getPrototypeOf(sobj);
proto ===pobj;//=>true
pobj.isPrototype(sobj);  //=>true
Object.prototype.isPrototype(sobj); //=>true 
```
### 2.类属性
对象的类属性是一个字符串，用以描述对象的类型，E3,E5都没有提供查询和设置此属性的方法，只有间接使用`toString()`,`toString()`返回的格式为`[object Array]`,所以只要取第八个到倒数第二个位置的字符串，因为对象很可能重写`toString()`方法，所以最好间接的调用。

```javascript
var o = {p:1};
o.toString(); //=>"[object Object]"
Object.prototype.toString.call([]); //=>"[object Array]"
Object.prototype.toString.call(1); //=>"[object Number]"
```
### 3.可扩展
对象的可扩展性用以表示是否可以给对象添加属性，E5中，所有的内置对象和自定义对象默认都是可扩展的。
E5定义了设置和查询扩展性的方法，`Object.isExtensible()`用以查询对象是否可扩展,`Object.preventExtensions()`用以将对象设为不可扩展，该方法有两点需要注意：
* **方法不可逆，一旦设置，将无法更更改**
* **方法只影响本身，不影原型**

```javascript
var obj = {p:1};
Object.isExtensible([]); //=>true
Object.isExtensible(obj); //=>true
Object.isExtensible(Object.prototype); //=>true
Object.isExtensible("str"); //=>false

Object.preventExtensions(obj); =>{p:1}  //方法返回被设置的对象
Object.isExtensible(obj);   //=>false
```
如果想锁定一个对象可以将对象的属性设置为不可扩展和不可配置，E5提供了便捷的操作方法：
* Object.seal()
seal方法将对象设置为不可扩展的，并且将自有属性设置为不可配置的，也就是说不能添加，删除，配置属性。使用`Object.isSealed()`可以检测对象是否是封闭的。

```javascript
var obj = {p:1};
Object.seal(obj); =>{p:1}  //方法返回被设置的对象
Object.isSealed(obj);   //=>true
```
* Object.freeze()
freeze比seal更加严格，除了设置不可扩展和不可配置外，还将自有属性设置为只读（getter/setter属性不受影响），使用`Object.isFrozen()`来检测对象是否是冻结的

```javascript
var obj = {p:1};
Object.freeze(obj); //=>{p:1}  //方法返回被设置的对象
obj.p = 2;
obj.p //=>1  设置值无效
Object.isFrozen(obj);   //=>true
```
## 序列化对象
***
序列化就是将对象转为字符串和逆向操作的统称，E5提供了相对应的方法来实现以上操作。
`JSON.stringify()`用以将对象转为字符串，`JSON.parse()`用以将字符串转对象。
`JSON.stringify()`和，`JSON.parse()`第二个可选参数都是一个转换结果的函数，用以自定义转换结果
**函数，RegExp、Error和undefined是不能被序列化的**，日期对象序列化的结果是ISO格式的字符串，但parse却不会将其还原为日期，仍然保留字符串形态

```javascript
var obj = {p:1,reg:/d/,err:new Error("error"),d:new Date()};
var jsonStr = JSON.stringify(obj) 
//结果为："{"p":1,"reg":{},"err":{},"d":"2016-03-28T13:52:02.507Z"}"
JSON.parse(jsonStr);  
//结果为：Object {p: 1, reg: Object, err: Object, d: "2016-03-28T13:52:02.507Z"}
```