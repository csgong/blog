# 正则表达式读书笔记

## Parentheses(括号)

### 分组

如果用两次限定出现次数的元素不是字符组或者字符，而是几个字符或者表达式，这时就应该用括号将他们括起来，分为一组。
例如，要限定 `abc`出现的次数为两次，则表达式就应该为`/(abc){2}/`。
```javascript
var idReg = /^[1-9]\d{14}(\d{2}[\dx])?$/; //身份证为15位或者18位
idReg.test('12345678901234567');   //=> false
idReg.test('123456789012345');     //=> true
idReg.test('123456789012345678');  //=> true
```

### 多选结构

在正则中也可以使用`|`,来表示多选，只要`|`前后的表达式有一个匹配成功，则整个表达式就匹配成功。
```javascript
var phoneReg = /^(0|\+86)?1[358]\d{9}$/; //手机简单规则
phoneReg.test('159608244944'); //=> false
phoneReg.test('15960824494');  //=> true
phoneReg.test('14960824494');  //=> false
```
多选结构注意事项：

    1. 多选结构可以不使用括号括起来，但是最好都用括号，这样清晰，不容易出错，例如：/^a | b$/和/^（a | b）$/表达的含义却天壤之别。
    2. 多选有些情况下可以和字符组互相替换，但是不推荐，首先字符组要比多选结构简单，其次多选不能使用量词，更重要的是，字符组要比多选效率高很多！！
    3. 多选排列顺序是有讲究的，例如表达式`/(gong | gongcs)/` 用他匹配`gong` 结果是`gong`还是`gongcs`呢？

### 捕获分组（capturing group）

#### 1.概念
使用括号后，正则会将括号内的表达式匹配的内容保存起来，使用`match`（javascript）之类的方法引用匹配的内容。
这种功能叫捕获分组。
`match`方法的第一个参数是整个表达式匹配的字符串，其余参数则是括号内子表达式匹配的字符串。
```javascript
var reg = /([a-c]+)([d-f]+)/; 
'abcdef'.match(reg); //=> ["abcdef", "abc", "def"]
```
`match`方法的参数顺序取决于分组的顺序，分组的顺序就是括号出现的顺序
```javascript
var reg = /((([a-c]+)([d-f]+)))/; 
'abcdef'.match(reg); //=> ["abcdef", "abcdef", "abcdef", "abc", "def"]
```
上面的表达式一共个有四个分组，可以看到除了第一个参数外，其余的参数顺序就是括号出现的循序，如下图所示：

![括号的层次](http://7xrv9g.com1.z0.glb.clouddn.com/paren_layer)

javascript `replace`方法也同`match`方法类似，都可以引用捕获分组。当`replace`方法的第二个参数是函数时，该函数的第一个参数为匹配的字符串，
接下来的参数是子表达式匹配的字符串，可以有 0 个或多个这样的参数，接下来的参数是一个整数，声明了匹配在字符串中出现的位置，最后一个参数是字符串本身。
```javascript
var reg = /([a-c]+)([d-f]+)/; 
'abcdefghi'.replace(reg,function(){
  console.log([].slice.call(arguments)); //=>["abcdef", "abc", "def", 0, "abcdefghi"]
});
```

#### 2.可以使用捕获分组实现替换功能

```javascript
var dateReg = /(\d{4})-(\d{1,2})-(\d{1,2})/; 
'2015-3-2'.replace(dateReg,'$3/$2/$1'); //=>"2/3/2015"
'2015-3-2'.replace(dateReg,function(value,$1,$2,$3){
    return $3+'/'+$2 +'/'+$1
}); //=>"2/3/2015"
```
#### 2.反向引用
可以在表达式内部引用之前分组匹配的内容，这种功能叫反向引用。格式为 `\ + 组号`，例如`\1`表示应用第一个分组匹配的内容。
利用反向引用可以检查字符串中是否包含连续的重复字母，像abbc,jeff等。
```javascript
var twoRepeatReg = /([a-z])\1/g; 
'abbcjeff'.match(twoRepeatReg); //=> ["bb", "ff"]

var moreRepeatReg = /([a-z])\1+/g; 
'abbbbbcjeffffff'.match(moreRepeatReg); //=> ["bbbbb", "ffffff"]
```
只要有括号，正则就会把括号内匹配的内容存储起来，但是如果不需要引用捕获分组，则存储这些将会影响性能，为了解决这个问题，正则提供了**非捕获分组（non-capturing group）**
非捕获分组就是在开始的括号后紧跟着一个问号和冒号`（?:）`，他只限定量词的作用范围，不捕获内容。
```javascript
var capturReg = /([a-c]+)([d-f]+)/; 
'abcdef'.match(capturReg); //=> ["abcdef", "abc", "def"]

var nonCapturReg = /(?:[a-c]+)(?:[d-f]+)/; 
'abcdef'.match(nonCapturReg); //=> ["abcdef"]
```