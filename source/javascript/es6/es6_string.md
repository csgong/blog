# 字符串的扩展

## 新增方法

* includes()、startsWith()、endsWith()

`includes()` 方法用以判断字符串是否包含所给参数字符串

`startsWith()` 方法用以判断所给参数字符串是否位于字符串的头部

`endsWith()` 方法用以判断所给参数字符串是否位于字符串的尾部

三个方法均返回判断结果true/false,当不传参数或参数为null时，返回false,空的字符串返回true。

```javascript
"abc".includes();     //=> false
"abc".includes("");   //=> true
"abc".includes("a");  //=> true
"abc".includes("abc");//=> true

"abc".startsWith();     //=> false
"abc".startsWith("");   //=> true
"abc".startsWith("a");  //=> true
"abc".startsWith("abc");//=> true
"abc".startsWith("b");  //=> false

"abc".endsWith();      //=> false
"abc".endsWith("");    //=> true
"abc".endsWith("c");   //=> true
"abc".endsWith("abc"); //=> true
"abc".endsWith("b");   //=> false
```
同`indexOf()`和`lastIndexOf()`一样，三个方法同样支持第二个参数，表示开始搜索的位置
```javascript
"abcdef".includes("c",2);  //=> true
"abcdef".includes("c",3);  //=> false
"abcdef".includes("c",-3); //=> true
"abcdef".includes("c",-4); //=> false

"abcdef".startsWith("c",2);      //=> true
"abcdef".startsWith("c",3);      //=> false
"abcdef".startsWith("c",-3);     //=> false
"abcdef".startsWith("c",-4);     //=> false
"abcdef".startsWith("a",-4);     //=> true
"abcdef".startsWith("abcdef",-4);//=> true

"abcdef".endsWith("c",2);   //=> true
"abcdef".endsWith("b",2);   //=> true
"abcdef".endsWith("ab",2);  //=> true
"abcdef".endsWith("e",-1);  //=> false
"abcdef".endsWith("f",-3);  //=> false
"abcdef".endsWith("d",-4);  //=> false
```
从上述例子中可以看到，第二个参数为负值时，对于`startsWith()`方法相当与没有设置，而对于`endsWith()` 方法是不支持的，返回结果均为false。
参数为正值时，`endsWith()`的行为与其他两个方法有所不同,它针对前n个字符，而其他两个方法针对从第n个位置直到字符串结束。

* repeat()
方法返回一个新字符串，表示将原字符串重复n次，如果参数是小数，会被取整，负值或者Infinity会报错
```javascript
"abc".repeat();      //=> ""
"abc".repeat(3);     //=> "abcabcabc"
"abc".repeat("3");   //=> "abcabcabc"
"abc".repeat(-3);    //=> RangeError: Invalid count value  负数会报错
"abc".repeat(1/0);   //=> RangeError: Invalid count value  1/0的结果为正无穷大(Infinity)
```
如果参数为NaN或者不能转为数字类型的值，方法均返回空的字符串。
```javascript
"abc".repeat("a"); //=> ""
"abc".repeat({});  //=> ""
"abc".repeat([]);  //=> ""
```

## 字符串模板(template string)
模板字符串（template string）是增强版的字符串，用反引号（`）标识。它可以当作普通字符串使用，也可以用来定义多行字符串并且字符串之间的格式将完全被保留。

```javascript
var str = `this 'is' "a" string`;
var mutiLineStr = `
<div>
   <p>hello word!</p>                
</div`;
console.log(str);  //=> this 'is' "a" string  可以包含'和"引号
console.log(`hello ` + `world !`);  //=> hello world ! 可以连接
console.log(mutiLineStr);  //=> "
                               <div>
                                  <p>hello word!</p>                
                               </div"
```
模板字符串中可以嵌入变量，需要将变量名写在`${}`之中，`${}`内部可以放入任意的JavaScript表达式，可以进行运算，引用对象属性以及调用函数

```javascript
var name = "world";
var obj = {name:"world"};
function getName(){
    return "world"
}
console.log(`${"name"}`); //=>name  大括号内的字符串将原样输出
console.log(`hello ${name} !`); //=>hello world !
console.log(`hello ${name + ` !`.repeat(3)}`); //=>hello world ! ! !
console.log(`hello ${obj.name} !`); //=>hello world !
console.log(`hello ${getName()} !`); //=>hello world !
```
如果`${}`内部的值不是字符串，将调用相应类型的`toString`方法转为字符串，如果如果`${}`内的变量未定，将报错！

```javascript
console.log(`hello ${name} !`); //=>ReferenceError: name is not defined
console.log(`${new Date()}`); //=>Tue May 24 2016 15:30:45 GMT+0800
```
借助于箭头函数，模板字符串还可以嵌套
```javascript
const tmpl = addrs => `
  <table>
  ${addrs.map(addr => `
    <tr><td>${addr.first}</td></tr>
    <tr><td>${addr.last}</td></tr>
  `).join('')}
  </table>
`;
```

## 标签模板
模板字符串可以紧跟在一个函数名后面，该函数将被调用来处理这个模板字符串。这被称为`标签模板`功能（tagged template）。
函数的参数就是模板字符串，函数返回值就是表达式的返回值。
```javascript
alert`hello` //=> 弹出hello
isNaN`ddd`   //=>true
```
标签模板函数的形式如下：
```javascript
function tag(stringArr, value1, value2){
  // ...
}

// 等同于

function tag(stringArr, ...values){
  // ...
}
```
其中第一个参数stringArr是一个数组，由那些没有变量替换的部分组成，也就是说整个模板字符串被多个待替换的变量分割成了若干部分，
stringArr可以近似的看成是模板字符串执行了split("$(.*?)")操作的结果集。
函数的其他参数，都是模板字符串各个变量被替换后的值。
```javascript
var a = 10;
var b =20;
function compile(){
  console.log(arguments.length);
    console.log(Array.prototype.join.call(arguments,"||"));
}
compile`${a}+${b}=${a+b}` //=>",+,=,||10||20||30"
```
上述例子只是将参数参数打印，下面提供一种compile函数的实现
```javascript
function compile(){
    var strs = arguments[0];
  var result = strs[0];
    for (let i = 1; i < arguments.length; i++) {
        result += arguments[i]+ strs[i];  
    }
  return result;
}
```
参数stringArr有一个raw属性，也指向一个数组。该数组的成员与stringArr数组完全一致。区别就是字符串里面的斜杠都被转义了。比如，strings.raw数组会将\n视为\和n两个字符，而不是换行符。这是为了方便取得转义之前的原始模板而设计的。

## String.raw()
`String.raw()`是一个模板字符串的标签函数，它的作用类似于 Python 中的字符串前缀 r 和 C# 中的字符串前缀 @，是用来获取一个模板字符串的原始字面量值的.
```javascript
function r(strings, ...values) {
  console.log(strings.raw[0] === '\\n');
}
r`\n` // => true
```
