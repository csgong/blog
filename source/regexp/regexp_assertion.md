# 正则表达式读书笔记

## Assertion(断言)

### 1. 单词边界（word boundary）
正则提供了单词边界（\b）,它匹配的是这样的字符串：一边是单词字符，一边不是单词字符
```javascript
var reg = /\bword\b/
console.log(reg.test('word'));           //=>true
console.log(reg.test('wordone'));        //=>false
console.log(reg.test('onewordone'));     //=>false
console.log(reg.test('one word done'));  //=>true
console.log(reg.test('one\rword\ndone'));//=>true
```
关于`\r`和`\n`的区别，可以参考>[回车和换行](http://www.ruanyifeng.com/blog/2006/04/post_213.html)

### 2. 行起始位置
单词边界匹配的是某个位置，在正则中，这类匹配位置的元素叫做锚点（anchor）,它用来定位位置，常见的还有`^`和`$`
`^`用来检测起始位置是否匹配，例如可以检测字符串是否以某个字符或字符串开头。

```javascript
var reg = /^head/;
console.log(reg.test('headabc'));  //=>true
console.log(reg.test('abchead'));  //=>false
```
`$`用来检测字符串是否以某个字符或者字符串结尾。
```javascript
var reg = /head$/;
console.log(reg.test('headabc'));  //=>false
console.log(reg.test('abchead'));  //=>true
```
同时使用时，可以判断整个字符串是否由表达式匹配。
```javascript
var reg = /^head$/;
console.log(reg.test('headabc'));  //=>false
console.log(reg.test('abchead'));  //=>false
console.log(reg.test('head'));     //=>true
```
需要注意的是，锚点的匹配都是相对行的，比如`^`和`$`匹配的就是行首和行尾，在多行模式下，可以匹配换行符之后的位置。
javascript中多行匹配的模式为`m`.
```javascript
'headabc\nheaddef'.match(/^head/mg); //=>["head", "head"]
'abchead\ndefhead'.match(/head$/mg); //=>["head", "head"]
'head\nhead'.match(/^head$/mg);      //=>["head", "head"]
```
利用`^`和`$`去除首尾空格
```javascript
' headabc \n    headdef '.replace(/(?:^\s+)|(?:\s+$)/g,'');
//"headabc 
//       headdef"
' headabc \n    headdef '.replace(/(?:^\s+)|(?:\s+$)/gm,'');
//"headabc 
//headdef"
```
### 3. 环视

javascript 的环视可以理解为在当前位置的右侧必须（肯定环视）或者不能出现（否定环视）匹配的文本。
肯定环视的结构为`(?=express)`,否定环视的结构为`(?!express)`,express就是匹配的表达式。
同其他断言一样，环视匹配的同样是位置。考虑如下例子：使用正则给12345添加千分号
```javascript
//右侧必须包含三位数字的正则
var reg = /(?=\d{3})/g;
var str = '12345';
reg.test(str);  //=> true
str.match(reg); //=> ["", "", ""]
str.replace(reg,',') //=> ",1,2,345"
```
如上例，表达式匹配了三个位置，每个位置都满足右侧是三个数字的条件，但这样添加的千分位并不是我们需要的，
我们千分号的位置除了右侧是3位数字外，还应该满足3个数字后面是非数字字符的条件，这时就要使用环视组合来完成了
```javascript
//右侧包含三位数字,三位数字后不能是数字的正则
var reg = /(?=\d{3}(?!\d))/g;
var str = '12345';
reg.test(str);  //=> true
str.match(reg); //=> [""]
str.replace(reg,',') //=> "12,345"
```
环视可以并列，并列的环视表示“且”的关系，即要求当前位置必须所有并列的环视判断都成功。
```javascript
//右侧包含三位数字并且三位数字不能是666的正则
var reg = /(?=\d{3})(?!666)/g;
var str = '12345666';
reg.test(str);  //=> true
str.match(reg); //=> ["", "", "", "", ""]
```
环视还可以使用多选结构，表示当前位置只要有一个环视匹配，就可以。
```javascript
//匹配右侧或者是g或者是G这样的位置
var reg = /(?=g)|(?=G)/g;
var str = 'gogGo';
reg.test(str);  //=> true
str.match(reg); //=> ["", "",]
```