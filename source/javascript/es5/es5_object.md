# 对象新特性
    
## 创建对象
Object.create 是ECMAScript 5新增的方法，用来创建对象，方法有两个参数，第一个为对象的原型，第二个为属性的特性描述，如果第一个参数为`null`，那么新创建的对象不继承任何东西，自然也就不会有`Object.prototype`中的基础方法，而前两种方法默认都继承自`Object.prototype`。

```javascript
var obj = Object.create(Object.prototype);//创建一个空对象
obj.toString();  =>"[object Object]"  //继承了Object.prototype
var obj = Object.create(); //创建了无任何继承的空对象
obj.toString();  =>Uncaught TypeError: obj.toString is not a function 不存在该方法
```
    
## 检测属性

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
result.toString(); =>"a" 
//检测非空属性
result = [];
for(var p in obj) {
    if(obj[p] != null) result.push(p);
}
result.toString(); =>"a,b,d" 
```
## 枚举属性

枚举属性的话可以使用`for/in`,ECMAScript 5新增的`Object.keys()`以及`Object.getOwnPropertyNames()`。

* for/in
for/in可以遍历所有可枚举的自有属性和继承属性。

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
属性除了值和名字外，还包含writable、configurable,enumerable

* 可写（writable）
    是否可以设置属性值。
* 可枚举（enumerable）
    是否可以使用`for/in`循环返回该属性。
* 可配置（configurable）
    是否可删除和修改属性。

如果把属性的值也算作特性的话，那么属性就有四种特性，E5之前，属性的特性是不可配置的，新增的属性都是可写，可读和可配置的。E5中特性是可以获取和定义的，E5定义了一个**属性描述符(property discriptor**)对象，用来操作特性。

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
* **方法只影响本身，不影影响原型**

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

序列化就是将对象转为字符串和逆向操作的统称，E5提供了相对应的方法来实现以上操作。

* `JSON.stringify(value[, replacer [, space]])`
用以将对象转为字符串，不可枚举的属性将被忽略
replacer:如果该参数是一个函数，则在序列化过程中，被序列化的值的每个属性都会经过该函数的转换和处理；如果该参数是一个数组，则只有包含在这个数组中的属性名才会被序列化到最终的 JSON 字符串中。
space :指定缩进用的空白字符串，用于美化输出（pretty-print）。
`JSON.stringify()`和，`JSON.parse()`第二个可选参数都是一个转换结果的函数，用以自定义转换结果
**函数，RegExp、Error和undefined是不能被序列化的**，日期对象序列化的结果是ISO格式的字符串，但parse却不会将其还原为日期，仍然保留字符串形态

```javascript
var obj = {p:1,reg:/d/,err:new Error("error"),d:new Date()};
var jsonStr = JSON.stringify(obj) 
//结果为："{"p":1,"reg":{},"err":{},"d":"2016-03-28T13:52:02.507Z"}"
JSON.parse(jsonStr);  
//结果为：Object {p: 1, reg: Object, err: Object, d: "2016-03-28T13:52:02.507Z"}
```
如果第二个参数是数组，那么只转换数组里面的属性
```javascript
var obj = {a:1,b:2,c:3};
var jsonStr = JSON.stringify(obj,["a"]) 
console.log(jsonStr); //=>{"a":1}
```
`JSON.stringify()`在序列化时，调用的是相应对象的toJSON方法，如果想自定义序列化结果，可以定义此方法
```javascript
var obj = {
    a:1,
    b:2,
    c:3,
    toJSON:function(){
        return "{\"cusAttr\":1}";
    }
};
var jsonStr = JSON.stringify(obj) ;
console.log(jsonStr); //=>"{\"cusAttr\":1}"
```
space 参数用来控制结果字符串里面的间距。如果是一个数字, 则在字符串化时每一级别会比上一级别缩进多这个数字值的空格（最多10个空格）；
```javascript
JSON.stringify({ a: 2 }, null, "\n");
//=> "{
   
   "a": 2
   }"

```
* `JSON.parse(text[, reviver])`
用以将字符串转对象。
reviver 一个函数，用来转换解析出的属性值
如果指定了 reviver 函数，则解析出的 JavaScript 值（解析值）会经过一次转换后才将被最终返回（返回值）。
更具体点讲就是：解析值本身以及它所包含的所有属性，会按照一定的顺序（从最最里层的属性开始，一级级往外，最终到达顶层，也就是解析值本身）分别的去调用 reviver 函数，在调用过程中，当前属性所属的对象会作为 this 值，当前属性名和属性值会分别作为第一个和第二个参数传入 reviver 中。
如果 reviver 返回 undefined，则当前属性会从所属对象中删除，如果返回了其他值，则返回的值会成为当前属性新的属性值。
```javascript
JSON.parse('{"p": 5}', function (k, v) {
    if(k === '') return v;     // 如果到了最顶层，则直接返回属性值，
    return v * 2;              // 否则将属性值变为原来的 2 倍。
});                            // { p: 10 }

JSON.parse('{"1": 1, "2": 2,"3": {"4": 4, "5": {"6": 6}}}', function (k, v) {
    console.log(k); // 1 2 4 6 5 3
    return v; 
});
```
