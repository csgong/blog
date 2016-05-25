# Number的扩展

## Number.isFinite(), Number.isNaN()
ES6在Number对象上，新提供了Number.isFinite()和Number.isNaN()两个方法，用来检查Infinite和NaN这两个特殊值.
它们与全局方法isFinite()和isNaN()的区别在于，传统方法先调用Number()将非数值的值转为数值，再进行判断，而这两个新方法只对数值有效，非数值一律返回false
```javascript
isNaN(undefined);// =>true  不传参数或参数为undefined时，返回true
isNaN(Infinity); // =>false  
isNaN("");       // =>false  "" == 0
isNaN(null);     // =>false  
isNaN("NaN");    // =>true  
isNaN(NaN);      // =>true  
isNaN("aa");     // =>true  

Number.isNaN(undefined);// =>false  
Number.isNaN(Infinity); // =>false  
Number.isNaN("");       // =>false
Number.isNaN(null);     // =>false  
Number.isNaN("NaN");    // =>false  
Number.isNaN(NaN);      // =>true  
Number.isNaN("aa");     // =>false
  
isFinite(undefined);// =>false  
isFinite(Infinity); // =>false  
isFinite("");       // =>true   "" == 0
isFinite(null);     // =>true  
isFinite(NaN);      // =>false  
isFinite(12);       // =>true  
isFinite("12");     // =>true  
 
Number.isFinite(undefined);// =>false  
Number.isFinite(Infinity); // =>false  
Number.isFinite("");       // =>false
Number.isFinite(null);     // =>false  
Number.isFinite(NaN);      // =>false  
Number.isFinite("12");     // =>false  
Number.isFinite(12);       // =>true  
```

## Number.parseInt(), Number.parseFloat()
ES6将全局方法parseInt()和parseFloat()，移植到Number对象上面，行为完全保持不变。

* Number.parseFloat() 
函数可解析一个字符串，并返回一个浮点数。
该函数指定字符串中的首个字符是否是数字。如果是，则对字符串进行解析，直到到达数字的末端为止，然后以数字返回该数字，如果参数不能转为数字，则返回NaN
```javascript
Number.parseFloat("");     // =>NaN  
Number.parseFloat("1");    // =>1
Number.parseFloat("  11"); // =>11 
Number.parseFloat("1.11");//=>1.11 
```
* Number.parseInt() 
函数同`Number.parseFloat()`一样，区别在于返回的是整数。
```javascript
Number.parseInt("");     // =>NaN  
Number.parseInt("1");    // =>1
Number.parseInt("  11"); // =>11 
Number.parseInt("1.11");//=>1 
```
## Number.isInteger()
`Number.isInteger()`用来判断一个值是否为整数。
```javascript
Number.isInteger(1);  // =>true  
Number.isInteger(1.0);// =>true 
Number.isInteger("1");// =>false
Number.isInteger(1.1);// =>false
```