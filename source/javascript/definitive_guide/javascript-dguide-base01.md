# Javascript权威指南读书笔记-算数运算
### 1. Infinity
当运算结果超出Javascript表示的最大值时，Javascript 返回Infinity。
例如:1/0的结果始终是Infinity,并不会像其他语言一样报错。
<!-- more --> 
```javascript
1/0 == Infinity  =>true
1/0 ===Infinity  =>true
```
### 2. NaN
对于一些无意义的算数运算，Javascript会返回NaN
例如:0/0,数字类型和非数字类型或无法转换为数字的类型进行运算，结果都为NaN.

```javascript
1*"str"  => NaN
1*{}     => NaN
```
但是有个例外，就是空的字符串包括有空格在内的空串(""," ") "",在进行数学运算时，除了加法运算之外Javascript会将空的字符串转为数字0进行运算,而加法嘛，自然就是进行字符串连接操作咯。

```javascript
1*""   => 0
1*" "  => 0
1/""   => Infinity
1+""   => "1"
```
所以,空的字符串和0是相等的，但是类型不相等。

```javascript
0==""   => true
0==""   => false
```
非数字值NaN 有一点特殊，那就是它和任何值都不相等，包括自己，所以你不能用NaN==NaN和 1*"str"  == NaN等这样的条件去判断运算结果是否正确。

```javascript
NaN==NaN     => false
NaN===NaN    => false
1*"str"==NaN => false
```
### 3. 检测算术运算结果
虽然这个NaN这么另类，但是有方法判断值是否是NaN,就是用不等(!=)来判断，
只要一直值不等于它自己，那么它一定是NaN类型。

```javascript
var anum = 1/"str";  anum值为NaN
anum  !=anum   => true
```
除了用!=这种方法，Javacript还提供了全局方法`isNaN()`来判断。

```javascript
isNaN(NaN)      => true
isNaN(1*"str")  => true
```
用以上方法只能判断运算结果是否为NaN,非法的运算结果可能还有Infinity和-Infinity，
Javacript同样提供了`isFinite()`全局方法来检测这些情况，
finite英文为"有限的","有穷的"英文好的话，这个方法不难记住（可惜我的英文。。）

```javascript
isFinite(1/0)        => false
isFinite(NaN)        => false
isFinite(Infinity)   => false
```
### 4. 精度
JavaScript采用了`IEEE-754`浮点数表示法，但是`IEEE-754`标准中的浮点数并不能精确地表达小数（比如说0.1），所以在进行**纯小数**运算时，偶尔会得不到正确结果。

```javascript
0.3-0.2 == 0.2-0.1  =>false
0.3-0.2             =>0.09999999999999998
0.2-0.1             =>0.1
```
解决办法就是利用JavaScript内置函数`toFixed()`进行四舍五入操作喽。

```javascript
var result = 0.3-0.2;
result.toFixed(1)         =>"0.1"
result.toFixed(1) ==0.1   =>true
result.toFixed(1) ===0.1  =>false     //toFixed返回的是字符串
```