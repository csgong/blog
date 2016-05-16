---
title: Javascript权威指南读书笔记-运算符
date: 2016-03-21 12:54:33
tags: Javascript权威指南
categories: javascript
---
## 1. in 运算符
in 用于检测对象是否包含某个属性

```javascript
var obj = {a:1,b:2};
"a" in obj   => true
```
<!-- more --> 
in也可以用来检测数组元素，但是只能检测数组是否存在第几个元素，而不能检测某个元素是否存在数组中，也就是说，他的左操作数是数组下标

```javascript
var arr = [9,8,7,6];
9 in arr       => false
"9" in arr     => false
0 in arr       => true
```
## 2. instanceof 运算符
如果`instanceof`左边操作数不是一个对象，instanceof返回false
如果右边不是函数，则抛出异常

```javascript
var obj = {};
obj instanceof Object        =>true
"object" instanceof Object   =>false
obj instanceof "object"      =>TypeError
```
## 3. 带操作的赋值运算

带操作的赋值运算是指a op = b 这种形式的它与a =a op  b等价，但是当遇到++、--带有副作用的表达式时，两者不等价

```javascript
var arr = [9,8,7,6],i = 0;
arr[i++] +=2;        =>11
arr[i++]=arr[i++]+2; =>10
```
## 4. void运算符
void 会忽略操作结果并返回`undefined`,这个运算符常见于客户端url处理，让浏览器忽略表达式的返回值。
