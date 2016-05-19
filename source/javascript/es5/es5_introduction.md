# ES5 学习笔记

## 关于ES5
ECMAScript 5.1 (或仅 ES5) 是ECMAScript(基于JavaScript的规范)标准最新修正。
ES5通过对现有JavaScript方法添加语句和原生ECMAScript对象做合并实现标准化。
以下浏览器都可以良好的支持ECMAScript5的行为了：Opera 11.60+、Internet Explorer 9+、Firefox 4+、Safari 5.1+、Chrome 13。
其中目前来看，IE9不支持strict，但IE10是支持的.
## 新增内容

### 新增了严格模式(strict)

### 数组对象添加了一些新的方法：
* 添加了静态方法Array.isArray(obj)用于判断obj是否为一个Array对象的实例。
* 添加了两个用于查找指定项索引的方法indexOf()和lastIndexOf()。查找时使用全等（===）进行匹配。
* 添加了every()、some()、forEach()、map()、filter()迭代方法。
* 添加了reduce()和reduceRight()简化方法

### String对象添加了trim()方法

### JSON对象添加了原生JSON内建对象

### Date对象添加了Date.now()、Date.prototype.toJSON()等方法

### Function对象添加了bind()方法

### ES5新增了属性描述属性enumerable、configurable、writable、get、set

### Object对象
* 继承相关方法：create()、getPrototypeOf() 
* 属性相关方法：defineProperty()、defineProperties()、getOwnPropertyDescriptor()、getOwnPropertyNames()、keys()
* 防篡改方法：preventExtensions()、isExtensible()、seal()、isSealed()、freeze()、isFrozen() 