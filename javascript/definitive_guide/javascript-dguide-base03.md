---
title: Javascript权威指南读书笔记-delete
date: 2016-03-21 13:40:30
tags: Javascript权威指南
categories: javascript
---
## 详解delete运算符
之所以把delete运算符单独拿出来，是因为它的知识点很多，而且容易被忽视

* 成功删除属性则返回true
```javascript
var obj = {attr1:"a1",attr2:"a2"};
delete obj.attr1 => true;
"attr1" in obj   => false  //删除成功
```
<!-- more --> 
* 操作数不是左值，则返回true（左值可以理解为对象的属性）
```javascript
delete 1 => true;
```
* 删除不存在的属性，返回true
```javascript
var obj = {};
delete obj.attr1 => true; //attr1不存在，返回true
```
* 在非严格模式下，删除全局属性，则返回true(严格模式下会报语法异常)
```javascript
global = 1;
delete global => true; //删除成功，严格模式下需要使用this.global，否则报语法错误
var gvar2 = "global2";
delete gvar2  =>false //不能删除使用var 定义的全局变量
```
* delete只能删除自有属性，无法删除继承属性，但是删除继承属性时却返回true.
```javascript
 var obj = {attr1:"a1",attr2:"a2"};
 var obj2 = Object.create(obj);
 delete obj2.attr2;
 obj2.attr2  =>a2  //属性依然存在
 obj.attr2   =>a2  //更不会影响被继承的对象
```
* delete只是断开属性和宿主的关系，并不会删除属性中的属性。
```javascript
var book =  {name:"a book",author:{name:"someone"}};
var author1 = book.author;
delete book.author;
author1.name    =>"someone"
book.author     =>undefined
```
* 删除数组元素
delete 删除数组元素时，数组长度不会改变
```javascript
var arr = [1,2,3];
delete arr[0];
arr      =>[undefined × 1, 2, 3]
arr.length =>3
```
* 3.未完待续