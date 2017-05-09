# 正则表达式读书笔记

## JavaScript API

### 1. RegExp 属性

* source
只读属性，保存文本形式的表达式
* global 
只读属性， 是否启用了全局匹配模式
* ignoreCase
只读属性，是否启用了忽略大小写模式
* multiline
只读属性，是否启用了多行模式
* lastIndex
可编辑属性，表达式下次匹配的起始位置，默认为0。

### 2. RegExp.exec(string)
`exec`方法从`lastIndex`位置开始匹配字符串，返回的是这样的一个数组：该数组的第一个元素是与正则第一个匹配的值，其余是捕获的分组值，它的`index`属性表示与正则第一个匹配的字符串在原字符串中的起始索引，`input`属性表示原始字符串。
```javascript
let reg = /(\d+)/;
reg.exec('abc123edf456'); //=>["123", "123", index: 3, input: "abc123edf456"]
reg.lastIndex //=>0
```
如果表达式启用了全局模式，则`exec`方法每次调用后，都会更新表达式的`lastIndex`属性，将其设置为下一次匹配的位置。
```javascript
let reg = /(\d+)/g;
reg.exec('abc123edf456'); //=>["123", "123", index: 3, input: "abc123edf456"]
reg.lastIndex //=>6
reg.exec('abc123edf456'); //=>["456", "456", index: 9, input: "abc123edf456"]
reg.lastIndex //=>12
reg.exec('abc123edf456'); //=>null
reg.lastIndex //=>0
```
**需要注意的是，如果方法找不到与之匹配的字符串，则返回`null`,同时`lastIndex`设置为0**.

### 3. RegExp.test(string)

`test`方法同`exec`方法一样，区别就是该方法返回的是布尔值。
```javascript
let reg = /(\d+)/;
reg.test('abc123edf456'); //=>true
reg.lastIndex //=>0
reg.test('abc123edf456'); //=>true
reg.lastIndex //=>0

let greg = /(\d+)/g;
greg.test('abc123edf456'); //=>true
greg.lastIndex //=>6
greg.test('abc123edf456'); //=>true
greg.lastIndex //=>12
greg.test('abc123edf456'); //=>false
greg.lastIndex //=>0
```

### 4. String.match(RegExp)
`match`方法在正则没有启用全局模式下，同`exec`方法的返回值一模一样，如果启用了全局模式，则方法返回的数组是包含所有匹配的文本，但是没有`input`和`index`属性。
还有很重要的一点就是无论是否启用了全局模式，`match`方法的匹配都和正则的`lastIndex`属性无关，始终从字符串的0位置开始匹配。
```javascript
let reg = /(\d+)/;
'abc123edf456'.match(reg); //=>["123", "123", index: 3, input: "abc123edf456"]

let greg = /(\d+)/g;
'abc123edf456'.match(greg); //=>["123", "456"]
greg.lastIndex //=>0
```
### 5. String.search(RegExp)
该方法用来查找表达式在字符串中的第一次匹配成功的位置，没找到则返回-1，
方法匹配时会忽略全局模式以及lastIndex属性的值。
```javascript
let reg = /(\d+)/;
'abc123edf456'.search(reg); //=>3

let greg = /(\d+)/g;
'abc123edf456'.match(greg); //=>3
greg.lastIndex = 100;
'abc123edf456'.match(greg); //=>3
```

# Additional resources

>[RegExp.prototype.exec()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/exec)
